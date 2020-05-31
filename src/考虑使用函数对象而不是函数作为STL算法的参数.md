# 考虑使用函数对象而不是函数作为STL算法的参数

```
struct StringSize : public std::unary_function<std::string, std::string::size_type> {
	std::string::size_type operator()(const std::string& s) const
	{
		return s.size();
	}
};
 
int test_item_46()
{
	std::set<std::string> s{ "abc", "cde", "xyzw" };
	std::transform(s.begin(), s.end(), std::ostream_iterator<std::string::size_type>(std::cout, "\n"), std::mem_fun_ref(&std::string::size)); //@ 3 3 4，普通函数
 
	std::transform(s.begin(), s.end(), std::ostream_iterator<std::string::size_type>(std::cout, "\n"), StringSize()); //@ 3 3 4, 函数对象
 
	return 0;
}
```

在 C/C++ 中并不能真正地将一个函数作为参数传递给另一个函数。如果我们试图将一个函数作为参数进行传递，则编译器会隐式地将它转换成一个指向该函数的指针，并将该指针传递过去。函数指针参数抑制了内联机制。

以函数对象作为 STL 算法的参数，这种做法提供了包括效率在内的多种优势。从代码被编译器接受的程度而言，它们更加稳定可靠。当然，普通函数在 C++ 中也是非常实用的，但是就有效使用 STL 而言，函数对象通常更加实用一些。
