#  切勿创建包含auto_ptr的容器对象

由于 `auto_ptr` 对于其"裸指针"必须具有独占性，当将一个 `auto_ptr` 的指针赋给另一个 `auto_ptr` 时，其值将被置空。

```
auto_ptr<int> p1(new int(1)); //@ p1 = 1
auto_ptr<int> p2(new int(2)); //@ p2 = 2

p2 = p1; //@ p2 = 1  p1 = emtpy;
```

