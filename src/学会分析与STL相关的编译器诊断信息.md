# 学会分析与STL相关的编译器诊断信息

在一个被声明为 `const` 的成员函数内部，该类的所有非静态数据成员都自动被转变为相应的 `const` 类型。

一些技巧：

-  `vector` 和 `string` 的迭代器通常就是指针，所以当错误地使用了 `iterator` 的时候，编译器的诊断信息中可能会引用到指针类型。例如，如果源代码中引用了 `vector<double>::iterator`，那么编译器的诊断信息中极有可能就会提及 `double*` 指针。
- 如果诊断信息中提到了 `back_insert_iterator`、`front_insert_iterator` 或者 `insert_iterator`，则几乎总是意味着你错误地调用了 `back_inserter`、`front_inserter` 或者 `inserter`。如果你并没有直接调用这些函数，则一定是你所调用的某个函数直接或者间接地调用了这些函数。
- 类似地，如果诊断信息中提到了 `binder1st` 或者 `binder2nd`，那么你可能是错误地使用了 `bind1st` 和 `bind2nd`。
- 输出迭代器(如 `ostream_iterator`、`ostreambuf_iterator` 以及那些由 `back_inserter`、front_inserter、`front_inserter` 和 `inserter` 函数返回的迭代器)在赋值操作符内部完成其输出或者插入工作，所以，如果在使用这些迭代器的时候犯了错误，那么你所看到的错误消息中可能会提到与赋值操作符有关的内容。
- 如果你得到的错误消息来源于某一个 STL 算法的内部实现(例如，引起错误的源代码在 `<algorithm>` 中)，那也许是你在调用算法的时候使用了错误的类型。例如，你可能使用了不恰当的迭代器类型。
-  如果你正在使用一个很常见的 STL 组件，比如 `vector`、`string` 或者 `for_each` 算法，但是从错误消息来看，编译器好像对此一无所知，那么可能是你没有包含相应的头文件。
  

