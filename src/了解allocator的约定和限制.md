# 了解allocator的约定和限制


如果需要编写自定义的 `allocator`，有以下几点需要注意

- 当 `allocator` 是一个模板，模板参数 `T` 代表为其分配内存的对象的类型
- 提供类型定义 `pointer` 和 `reference`，始终让 `pointer` 为 `T*` 而 `reference` 为 `T&`
- 不要让 `allocator`  拥有随对象而不同的状态，通常，`allocator` 不应该有非静态数据成员
- 传递给 `allocator` 的是要创建元素的个数而不是申请的字节数，该函数返回 `T*`，尽管此时还没有 `T` 对象构造出来
- 必须提供 `rebind` 模板，因为标准容器依赖于该模板