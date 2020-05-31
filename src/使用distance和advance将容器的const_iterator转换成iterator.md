# 使用distance和advance将容器的const_iterator转换成iterator

对于大多数的容器，`const_cast` 并不能将 `const_iterator` 转换为  `iterator`。即使在某些编译器上可以将 `vector` 和`string` 的 `const_iterator` 转换为 `iterator`，但存在移植性的问题

通过 `distance` 和 `advance` 将 `const_iterator` 转换为 `iterator` 的方法：

```
typedef std::deque<int> IntDeque;
typedef IntDeque::iterator Iter;
typedef IntDeque::const_iterator ConstIter;

IntDeque d(5, 10);
ConstIter ci;
ci = d.cbegin() + 1; // 使ci指向d
Iter i(d.begin());
std::advance(i, std::distance<ConstIter>(i, ci));
```

`std::distance` 用以取得两个迭代器(它们指向同一个容器)之间的距离；`std::advance` 则用于将一个迭代器移动指定的距离。

