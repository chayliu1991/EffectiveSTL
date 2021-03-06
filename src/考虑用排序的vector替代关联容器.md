# 考虑用排序的vector替代关联容器

哈希容器大部分情况下可以提供常数时间的查找效率，标准容器也可以达到对数时间的查找效率。

标准容器通常基于平衡二叉树实现， 这种实现对于插入、删除和查找的混合操作提供了优化。但是对于3步式的操作(首先进行插入操作，再进行查找操作，再修改元素或删除元素)，排序的 `vector` 能够提供更好的性能。
因为相对于 `vector`，关联容器需要更大的存储空间。在排序的 `vector` 中存储数据比在关联容器中存储数据消耗更少的内存，考虑到页面错误的因素，通过二分搜索进行查找，排序的 `vector` 效率更高一些。

如果使用排序的 `vector` 替换 `map`，需要实现一个自定义的排序类型，该排序类型依照键值进行排序。  

