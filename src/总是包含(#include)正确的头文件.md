# 总是包含(#include)正确的头文件

C++ 标准与 C 的标准有所不同，它没有规定标准库中的头文件之间的相互包含关系。

总结每个与 STL 有关的标准头文件中所包含的内容：

- 几乎所有的标准 STL 容器都被声明在与之同名的头文件中，比如 `vector` 被声明在 `<vector>` 中，`list` 被声明在`<list>` 中，等等。但是 `<set>` 和 `<map>` 是个例外，`<set>` 中声明了 `set` 和 `multiset`，`<map>` 中声明了 `map` 和 `multimap`。
- 除了4个 STL 算法以外，其它所有的算法都被声明在 `<algorithm>` 中，这4个算法是 `accumulate`、`inner_product`、`adjacent_difference` 和 `partial_sum`，它们被声明在头文件 `<numeric>` 中。
- 特殊类型的迭代器，包括 `istream_iterator` 和 `istreambuf_iterator`，被声明在 `<iterator>` 中。
- 标准的函数子(比如 `less<T>`)和函数子配接器(比如 `not1`、`bind2nd`)被声明在头文件 `<functional>` 中。

任何时候如果你使用了某个头文件中的一个 STL 组件，那么你就一定要提供对应的 `#include` 指令，即使你正在使用的 STL 平台允许你省略 `#include` 指令，你也要将它们包含到你的代码中。当你需要将代码移植到其它平台上的时候，移植的压力就会减轻。

