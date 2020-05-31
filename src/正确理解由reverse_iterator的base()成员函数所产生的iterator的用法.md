# 正确理解由reverse_iterator的base()成员函数所产生的iterator的用法

使用 `reverse_iterator` 的 `base()` 成员函数所产生的 `iterator` 和原来的 `reverse_iterator` 之间有一个元素的偏移量。

![](../img/reverse_iterator.png)

容器的插入、删除和修改操作都是基于 `iterator` 的，所以对于 `reverse_iterator`，必须通过 `base()` 成员函数转换为`iterator` 之后才能进行增删改的操作。

- 对于插入操作而言，新插入的元素都在3和4之间，所以可以直接使用 `insert(ri.base(),xxx)`
- 对于修改和删除操作，由于 `ri` 和 `ri.base()` 并不指向同一元素，所以在修改和删除前，必须修正偏移量

修正 `ri` 和 `ri.base()` 偏移量的做法：

```
	std::map<int, std::string> m{ { 0, "xxx" } };
	std::vector<int> v;
	v.reserve(5);

	for (int i = 1; i <= 5; ++i) v.push_back(i);

	std::vector<int>::reverse_iterator ri = std::find(v.rbegin(), v.rend(), 3); //@ 使ri指向3
	std::vector<int>::iterator i(ri.base());
	fprintf(stdout, "%d\n", (*i)); //@ 4
	v.insert(i, 99);
	for (auto it = v.cbegin(); it != v.cend(); ++it) fprintf(stdout, "value: %d\n", *it); //@ 1 2 3 99 4 5

	v.clear(); v.reserve(5);
	for (int i = 1; i <= 5; ++i) v.push_back(i);
	ri = std::find(v.rbegin(), v.rend(), 3);
	v.erase((++ri).base());
	for (auto it = v.cbegin(); it != v.cend(); ++it) fprintf(stdout, "value: %d\n", *it); //@ 1 2 4 5
```

如果要在一个 `reverse_iterator ri` 指定的位置上插入新元素，则只需在 `ri.base()` 位置处插入元素即可。对于插入操作而言，`ri` 和 `ri.base()` 是等价的，`ri.base()` 是真正与 `ri` 对应的 `iterator`。

如果要在一个 `reverse_iterator ri` 指定的位置上删除一个元素，则需要在 `ri.base()` 前面的位置上执行删除操作。对于删除操作而言，`ri` 和 `ri.base()` 是不等价的，`ri.base()` 不是与 `ri` 对应的 `iterator`。






