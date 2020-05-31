# 如果确实需要删除元素，则需要在remove这一类算法之后调用erase

```

int test_item_32()
{
	std::vector<int> v;
	v.reserve(10);
	for (int i = 1; i <= 10; ++i) v.push_back(i);
	fprintf(stdout, "v.size: %d\n", v.size()); //@ 输出10
	v[3] = v[5] = v[9] = 99;
	std::remove(v.begin(), v.end(), 99); //@ 删除所有值等于99的元素
	fprintf(stdout, "v.size: %d\n", v.size()); //@ 仍然输出10, remove不是真正意义上的删除，因为它做不到
	for (auto i : v) fprintf(stdout, "%d\n", i);
 
	v.erase(std::remove(v.begin(), v.end(), 99), v.end()); //@ 真正删除所有值等于99的元素	
 
	return 0;
}
```

`std::remove` 并不接受容器作为参数，所以 `remove` 并不知道这些元素被存放在哪个容器中。并且，`remove` 也不可能推断出是什么容器，因为无法从迭代器推知对应的容器类型。因为从容器中删除元素的唯一方法是调用该容器的成员函数，而 `remove` 并不知道它操作的元素所在的容器，所以 `remove` 不可能从容器中删除元素。

`std::list` 的 `remove` 成员函数是 STL 中唯一一个名为 `remove` 并且确实删除了容器中元素的函数。

`std::remove` 并不是唯一一个适用于这种情形的算法，其它还有两个属于 ”remove类” 的算法：`remove_if` 和 `unique`。如同 `list::remove` 会真正删除元素(并且比使用 `erase-remove` 习惯用法更为高效)一样，`std::list::unique` 也会真正删除元素(而且比使用 `erase-unique` 更为高效)。









