# 理解ptr_fun、men_fun和mem_fun_ref的来由

```
class Widget41 {
public:
	void test() {}
};
 
void test(Widget41& w) {}
 
int test_item_41()
{
	std::vector<Widget41> vw;
 
	std::for_each(vw.begin(), vw.end(), test);
	std::for_each(vw.begin(), vw.end(), std::ptr_fun(test));
	std::for_each(vw.begin(), vw.end(), std::mem_fun_ref(&Widget41::test));
 
	std::list<Widget41*> lpw;
	std::for_each(lpw.begin(), lpw.end(), std::mem_fun(&Widget41::test));
 
	return 0;
}
```

`std::ptr_fun`：将函数指针转换为函数对象。

`std::mem_fun`：将成员函数转换为函数对象(指针版本)。

`std::mem_fun_ref`：将成员函数转换为函数对象(引用版本)。

像 `mem_fun_t` 这样的类被称为函数对象配接器(function object adapter)。这些由 `mem_fun` 和 `mem_fun_ref` 产生的对象不仅使得 STL 组件可以通过同一种语法形式来调用所有的函数，而且它们还提供了一些重要的类型定义，就像 `fun_ptr` 所产生的对象一样。

如果你对什么时候该使用 `ptr_fun`，什么时候不该使用 `ptr_fun` 感到困惑，那么你可以在每次将一个函数传递给一个 STL 组件的时候总是使用它。STL 不会在意的，而且这样做也不会带来运行时的性能损失。另一种策略是，只有在迫不得已的时候才使用 `ptr_fun`。如果你省略了那些必要的类型定义，编译器就会提醒你，然后你再回去把 `ptr_fun` 加上。

`mem_fun` 和 `mem_fun_ref` 的情形则截然不同。每次在将一个成员函数传递给一个 STL 组件的时候，你就要使用它们。因为它们不仅仅引入了一些类型定义(可能是必要的，也可能不是必要的)，而且它们还转换调用语法的形式来适应算法----它们将针对成员函数的调用语法转换为 STL 组件所使用的调用语法。

C++11 中已不再推荐使用 `ptr_fun`、`mem_fun`、`mem_fun_ref` 等相关函数。
