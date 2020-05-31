# 使用swap技巧除去多余的容量

```
vector<int>(v).swap(v);
```

`vector<int>(v)` 使用 `v` 创建一个临时变量，`v` 中空余的内存将不会被拷贝到这个临时变量的空间中，再利用 `swap` 将这个临时变量与 `v` 进行交换，相当于去除掉了 `v` 中的多余内存。

由于 STL 实现的多样行，swap的方式并不能保证去掉所有的多余容量，但它将尽量将空间压缩到其实现的最小程度。

利用 `swap` 的交换容器的值的好处在于可以保证容器中元素的迭代器、指针和引用在交换后依然有效。 

```
vector<int> v1;
v1.push_back(1);
vector<int>::iterator i = v1.begin();
 
vector<int> v2(v1);
v2.swap(v1);
cout<<*i<<endl;  //@output 1  iterator指向v2的begin
```

但是在使用基于临时变量的 `swap` 要当心 `iterator` 失效的情况：

```
vector<int> v1;
v1.push_back(1);
vector<int>::iterator i = v1.begin();

vector<int>(v1).swap(v1);
cout<<*i<<endl;  //@ crash here
```

原因在于构造的临时变量在该行结束后就被析构了。

C++11 中增加了 `shrink_to_fit` 成员函数：

```

class Contestant {};
 
int test_item_17()
{
	//@ 从contestants矢量中除去多余的容量
	std::vector<Contestant> contestants;
	//@ ... //@ 让contestants变大，然后删除它的大部分元素
	//@ vector<Contestant>(contestants)创建一个临时矢量，vector的拷贝构造函数只为所拷贝的元素分配所需要的内存
	std::vector<Contestant>(contestants).swap(contestants);
 
	contestants.shrink_to_fit(); //@ C++11
 
	std::string s;
	/@/ ... //@ 让s变大，然后删除它的大部分字符
	std::string(s).swap(s);
    
	s.shrink_to_fit(); //@ C++11
 
	std::vector<Contestant>().swap(contestants); //@ 清除contestants并把它的容量变为最小
 
	std::string().swap(s); //@ 清除s并把它的容量变为最小
 
	return 0;
}
```

