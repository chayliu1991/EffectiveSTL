# vector和string优先于动态分配的数组

如果使用  `new` 来动态分配内存，使用者必须承担以下的责任：

- 确保之后调用 `delete` 将内存释放
- 确保使用的是正确的 `delete` 形式，对于单个对象要用 `delete`，对于数组对象需要用 `delete[]`
- 确保对于一个对象只 `delete` 一次

`vector`、`string` 自动管理其所包含元素的构造与析构，并有一系列的 STL 算法支持，同时 `vector` 也能够保证和老代码的兼容。

使用了引用计数的 `string` 可以避免不必要的内存分配和字符串拷贝(COW- copy on write)，但是在多线程环境里，对这个 `string` 进行线程同步的开销远大于 COW 的开销。此时，可以考虑使用 `vector<char>` 或动态数组。