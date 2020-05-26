# 如果容器中包含了通过new操作创建的指针，切记在容器对象析构前将指针delete掉

STL 容器在析构之前，会将其所包含的对象进行析构：

```
class widget
{
	...
};

doSth()
{
	widget w; //@ 一次构造函数
	vector<widget> v;
	v.push_back(w); //@ 一次拷贝构造函数
} //@ 两次析构函数
```

但如果容器中包含的是指针的话，一旦没有特别将指针 `delete` 掉将会发生内存泄漏：

```
class widget
{
   ...
};

doSth()
{
	widget* w = new widget();
	vector<widget*> v;
	v.push_back(w); 
} //@ memory leak!!!
```

最为方便并且能够保证异常安全的做法是将容器所保存的对象定义为带有引用计数的智能指针：

```
class widget
{
	...
};

doSth()
{
	shared_ptr<widget> w(new widget()); //@ 构造函数一次
	vector<shared_ptr<widget>> v;
	v.push_back(w); 
} //@ 析构函数一次 没有内存泄漏
```









