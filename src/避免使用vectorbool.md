# 避免使用`vector<bool>`

`vector<bool>` 并不是一个真正的容器，也并不存储真正的 `bool` 类型，为了节省空间，它存储的是 `bool` 的紧凑表示，通常是一个 `bit`。  

由于指向单个 `bit` 的指针或引用都是不被允许的，`vector<bool>` 采用代理对象模拟指针指向单个 `bit`。 

```
vector<bool> v;

bool *pb = &v[0]; //@ compile error
vector<bool>::reference *pr = &v[0];  //@ OK
```

可以考虑两种方式替代 `vector<bool>`：

- `deque<bool>` 但是要注意 `deque` 的内存布局与数组并不一致
- `bitse`，`bitset` 不是 STL 容器所以不支持迭代器，其大小在编译器就已经确定，`bool` 也是紧凑的存储在内存中



