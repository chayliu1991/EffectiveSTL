# 当效率至关重要时，请在map:operator[]和map:insert之间谨慎作出选择　

从效率方面的考虑，当向 `map` 中添加元素时，应该使用 `insert`，当需要修改一个元素的值的时候，需要使用 `operator[]`。

如果使用 `operator[]` 添加元素：

```
class Widget{
};

map<int,Widget> m;
Widget w;

//@ 如果m[0]没有对应的值，则会通过默认的构造函数生成一个widget对象
//@ 然后再用operator=将w的值赋给这个widget对象。
m[0] = w；
//@ Widget构造函数被调用两次 
```

 使用 `insert` 可以避免创建中间对象：

```
map<int,Widget> m;
Widget w; 

m.insert(map<int,Widget>::value_type(0,w));  //@ 没有调用构造函数
```

一个通用的添加和修改 `map` 中元素的方法：

```
//@ 提供高效的添加和更新功能
template<typename MapType, typename KeyArgType, typename ValueArgType>
typename MapType::iterator efficientAddOrUpdate(MapType& m, const KeyArgType& k, const ValueArgType& v)
{
	typename MapType::iterator lb = m.lower_bound(k); //@ 确定k在什么位置或应在什么位置
 
	if (lb != m.end() && !(m.key_comp()(k, lb->first))) { //@ 如果lb指向的pair的健与k等价,
		lb->second = v;				      //@ 则更新pair的值并返回指向该pair的迭代器
		return lb;
	}
	else {
		typedef typename MapType::value_type MVT;
		return m.insert(lb, MVT(k, v)); //@ 把pair<k,v>添加到m中，并返回一个指向该新元素的迭代器
	}
}

int test_item_24()
{
	std::map<int, std::string> m;
	m[1] = "xxx"; //@ m[1]是m.operator[](1)的缩写形式
	m.operator[](1) = "xxx";
 
	//@ m[1] = "xxx"; 在功能上等同于
	typedef std::map<int, std::string> IntStrMap;
	std::pair<IntStrMap::iterator, bool> result = m.insert(IntStrMap::value_type(1, std::string()));
	result.first->second = "xxx";
	//@ 以上方式性能降低的原因：先默认构造了一个string，然后立刻赋给它新的值。如果"直接使用我们所需要的值构造一个
	//@ string"比"先默认构造一个string再赋值"效率更高，那么，我们最好把对operator[]的使用(包括与之相伴的构造和
	//@ 赋值)换成对insert的直接调用
	m.insert(IntStrMap::value_type(1, "xxx")); //@ 它通常会节省三个函数调用：一个用于创建默认构造的临时string对象，
	//@ 一个用于析构该临时对象，另一个是调用string的赋值操作符
 
	efficientAddOrUpdate(m, 2, "yyy");
 
	return 0;
}
```

对效率的考虑使我们得出结论：当向映射表中添加元素时，要优先选用 `insert`，而不是 `operator[]`；而从效率和美学的观点考虑，结论是：当更新已经在映射表中的元素的值时，要优先选择 `operator[]`。











