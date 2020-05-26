# 当心C++编译器最烦人的分析机制

C++ 会尽可能的将一条语句解释为函数声明。
下列语句都声明了一个函数返回值为 `int` 类型的函数 `f`，其参数是 `double` 类型：

```
int f1(double d); //@ 声明了一个带double参数并返回int的函数
int f2(double(d)); //@ 同上，d两边的括号被忽略,可以给参数名加上圆括号
int f3(double); //@ 同上，参数名被忽略
```

下列语句都声明了一个返回值为 `int` 类型的函数 `g`，它的参数是返回值为 `double` 类型且无参的函数指针：

```
int g1(double(*pf)()); //@  参数是一个指向不带任何参数的函数的指针，该函数返回double值；g1以指向函数的指针为参数
int g2(double pf()); //@  同上，pf为隐式指针
int g3(double()); //@  同上，省去参数名
```

对于如下语句，编译器会做出这样的解释：声明了一个返回值为  `list<int>` 的函数 `data`，该函数有两个参数，一个是`istream_iterator<int>` 类型的变量，另一个是返回值为 `istream_iterator<int>` 类型的无参函数指针：

```
ifstream dataFile("ints.dat");
list<int> data(istream_iterator<int>(dataFile),istream_iterator<int>());
```

如果希望构造一个 `list<int>` 类型的变量 `data`，最好的方式是使用命名的迭代器。尽管这与通常的 STL 风格相违背，但是消除了编译器的二义性而且增强了程序的可读性：

```
ifstream dataFile("ints.dat");
istream_iterator dataBegin(dataFile);
istream_iterator dataEnd;
list<int> data(dataBegin,dataEnd);　
```

