# 切勿对STL容器的线程安全性有不切实际的依赖

当涉及到 STL 容器和线程安全性时：

- 对于 STL 容器的多线程读是安全的
- 对于多个不同的 STL 容器写入操作是安全的
- 但是不能依赖于任何线程支持，不要奢望 STL 库会把你从手工同步控制中解脱出来

```
template<typename Container>
class lock
{
public:
	Lock(const Container& container):c(container)
	{
		getMutexFor(c);
	}

	~Lock()
	{
		releaseMutex(c);
	}

private:
	Container& c;
},

vector<int> v;
...

{
	Lock<vector<int>> lock(v); //@ 构造lock,加锁v
	doSthSync(v); //@ 对v进行多线程的操作
} //@ 析构lock,解锁v

```

