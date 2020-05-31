#  对包含指针的容器使用remove这一类算法时要特别小心

```
class Widget33 {
public:
	bool isCertified() const { return true; }
};

//@ 如果*pWidget是一个未被验证的Widget33,则删除该指针，并把它置成空
void delAndNullifyUncertified(Widget33*& pWidget)
{
	if (!pWidget->isCertified()) {
		delete pWidget;
		pWidget;
	}
}

int test_item_33()
{
	std::vector<Widget33*> v;
	for (int i = 0; i < 5; ++i) v.push_back(new Widget33);

	//@ 删除那些指向未被验证过的Widget33对象的指针，会资源泄露
	v.erase(std::remove_if(v.begin(), v.end(), std::not1(std::mem_fun(&Widget33::isCertified))), v.end());

	//@ 一种可以消除资源泄露的做法
	//@ 将所有指向未被验证的Widget33对象的指针删除并置成空
	std::for_each(v.begin(), v.end(), delAndNullifyUncertified);
	//@ 删除v中的空指针，必须将0转换成一个指针，这样C++才能正确推断出remove的第三个参数类型
	v.erase(std::remove(v.begin(), v.end(), static_cast<Widget33*>(0)), v.end());

	//@ 使用智能指针可防止资源泄露
	std::vector<std::shared_ptr<Widget33>> v2;
	for (int i = 0; i < 5; ++i) v2.push_back(std::make_shared<Widget33>());
	//@ 下面语句需要编译器必须能够把智能指针类型std::shared<Widget33>隐式转换为对应的内置指针类型Widget33*才能通过编译
	//@v2.erase(std::remove_if(v2.begin(), v2.end(), std::not1(std::mem_fun(&Widget33::isCertified))), v2.end());

	return 0;
}
```

当容器中存放的是指向动态分配的对象的指针的时候，应该避免使用 `remove` 和类似的算法( `remove_if` 和 `unique`)。

如果容器中存放的不是普通指针，而是具有引用计数功能的智能指针，那么就可以直接使用 `erase-remove` 的习惯用法。

