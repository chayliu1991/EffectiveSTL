# 对于逐个字符的输入请考虑使用istreambuf_iterator

常用的 `istream_iterator` 内部使用的 `operator>>` 实际上执行了格式化的输入，每一次的 `operator>>` 操作都有很多的附加操作：

- 一个内部 `sentry` 对象的构造和析构(设置和清理行为的对象)
- 检查可能影响行为的流标志(比如 `skipws`)
- 检查可能发生的读取错误
- 出现错误时检查流的异常屏蔽标志以决定是否抛出异常

对于 `istreambuf_iterator`，它直接从流的缓冲区中读取下一个字符，不存在任何的格式化，所以效率相对`istream_iterator` 要高得多。

对于非格式化的输出，也可以考虑使用 `ostreambuf_iterator` 代替 `ostream_iterator`。（损失了格式化输出的灵活性） 。

```
//@ 把一个文本文件的内容拷贝到一个string对象中
std::ifstream inputFile("interestingData.txt");
inputFile.unsetf(std::ios::skipws); //@ 禁止忽略inputFile中的空格
std::string fileData((std::istream_iterator<char>(inputFile)), std::istream_iterator<char>()); 
//@ 速度慢

std::string fileData2((std::istreambuf_iterator<char>(inputFile)), std::istreambuf_iterator<char>()); //@ 速度快
```

`std::istream_iterator<char>` 对象使用 `operator>>` 从输入流中读取单个字符，而 `std::istreambuf_iterator<char>` 则直接从流的缓冲区中读取下一个字符。`std::istreambuf_iterator` 不会跳过任何字符，它只是简单地取回流缓冲区中的下一个字符，而不管它们是什么字符，因此用不着清除输入流的 `skipws` 标志。

同样地，对于非格式化的逐个字符输出过程，你也应该考虑使用 `std::ostreambuf_iterator`，它可以避免因使用`std::ostream_iterator` 而带来的额外负担。







