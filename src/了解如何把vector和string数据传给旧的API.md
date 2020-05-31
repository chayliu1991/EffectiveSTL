# 了解如何把vector和string数据传给旧的API

将 `vector` 传递给接受数组指针的函数，要注意 `vector` 为空的情况。迭代器并不等价于指针，所以不要将迭代器传递给参数为指针的函数。

```
void foo(const int* ptr, size_t size);

vector<int> v;
...
foo(v.empty() ? NULL : &v[0], v.size());
```

将 `string` 传递给接受字符串指针的函数。该方法还适用于 `s` 为空或包含 `\0`  的情况：

```
void foo(const char* ptr);

string s;
...
foo(s.c_str());
```

使用初始化数组的方式初始化 `vector`：

```
//@ 向数组中填入数据
size_t fillArray(int* ptr, size_t size);

int maxSize = 10;
vector<int> v(maxSize);
v.resize(fillArray(&v[0],v.size()));
```

借助 `vector` 与数组内存布局的一致性，我们可以使用 `vector` 作为中介，将数组中的内容拷贝到其他 STL 容器之中或将其他 STL 容器中的内容拷贝到数组中：

```
//@ 向数组中填入数据
size_t fillArray(int* ptr, size_t size);

vector<int> v(maxSize);
v.resize(fillArray(&v[0],v.size()));
set<int> s(v.begin(),v.end());

void foo(const int* ptr, size_t size);

list<int> l();
...

vector<int> v(l.begin(),l.end());
foo(v.empty()? NULL : &v[0],v.size());
```

C++ 标准要求 `vector` 中的元素存储在连续的内存中，就像数组一样。`string` 中的数据不一定存储在连续的内存中，而且 `string` 的内部表示不一定是以空字符结尾的。

```
void doSomething(const int* pInts, size_t numInts) {}
void doSomething(const char* pString) {}
size_t fillArray(double* pArray, size_t arraySize) { return (arraySize - 2); }
size_t fillString(char* pArray, size_t arraySize) { return (arraySize - 2); }
 
int test_item_16()
{
	//@  vector/string --> C API
	std::vector<int> v{ 1, 2 };
	if (!v.empty()) {
		doSomething(&v[0], v.size());
		doSomething(v.data(), v.size()); //@  C++11
		//@  doSomething(v.begin(), v.size()); //@  错误的用法
		doSomething(&*v.begin(), v.size()); //@  可以，但不易于理解
	}
 
	std::string s("xxx");
	doSomething(s.c_str());
 
	//@  C API 初始化vector
	const int maxNumDoubles = 100;
	std::vector<double> vd(maxNumDoubles); //@  创建大小为maxNumDoubles的vector
	vd.resize(fillArray(&vd[0], vd.size())); //@  使用fillArray向vd中写入数据，然后把vd的大小改为fillArray所写入的元素的个数
 
	//@  C API 初始化string
	const int maxNumChars = 100;
	std::vector<char> vc(maxNumChars); //@  创建大小为maxNumChars的vector
	size_t charsWritten = fillString(&vc[0], vc.size()); //@  使用fillString向vc中写入数据
	std::string s2(vc.cbegin(), vc.cbegin() + charsWritten); //@  通过区间构造函数，把数据从vc拷贝到s2中
 
	//@  先让C API把数据写入到一个vector中，然后把数据拷贝到期望最终写入的STL容器中，这一思想总是可行的
	std::deque<double> d(vd.cbegin(), vd.cend()); //@  把数据拷贝到deque中
	std::list<double> l(vd.cbegin(), vd.cend()); //@  把数据拷贝到list中
	std::set<double> s22(vd.cbegin(), vd.cend()); //@  把数据拷贝到set中
 
	//@  除了vector和string以外的其它STL容器也能把它们的数据传递给C API，你只需要把每个容器的元素拷贝到一个vector中，然后传给该API
	std::set<int> intSet; //@  存储要传给API的数据的set
	std::vector<int> v2(intSet.cbegin(), intSet.cend()); //@  把set的数据拷贝到vector
	if (!v2.empty()) doSomething(&v2[0], v2.size()); //@  把数据传给API
 
	return 0;
}
```







