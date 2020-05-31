# 理解自定义allocator的合理用法

```
void* mallocShared(size_t bytesNeeded)
{
	return malloc(bytesNeeded);
}
 
void freeShared(void* ptr)
{
	free(ptr);
}
 
template<typename T>
class SharedMemoryAllocator { //@ 把STL容器的内容放到共享内存(即由mallocShared生成的)中去
public:
	typedef T* pointer; //@ pointer是个类型定义，它实际上总是T*
	typedef size_t size_type; //@ 通常情况下,size_type是size_t的一个类型定义
	typedef T value_type;
 
	pointer allocate(size_type numObjects, const void* localityHint = 0)
	{
		return static_cast<pointer>(mallocShared(numObjects * sizeof(T)));
	}
 
	void deallocate(pointer ptrToMemory, size_type numObjects)
	{
		freeShared(ptrToMemory);
	}
 
	template<typename U>
	struct rebind {
		typedef std::allocator<U> other;
	};
};

int test_item_11()
{
	typedef std::vector<double, SharedMemoryAllocator<double>> SharedDoubleVec;
	/* v所分配的用来容纳其元素的内存将来自共享内存而v自己----包括它所有的数据成员----几乎肯定不会位于共享内存中，v只是普通的基于栈(stack)的对象，所以，像所有基于栈的对象一样，它将会被运行时系统放在任意可能的位置上。这个位置几乎肯定不是共享内存
	*/
	SharedDoubleVec v; //@  创建一个vector,其元素位于共享内存中
 
	//@  为了把v的内容和v自身都放到共享内存中，需要这样做
	//@ 为SharedDoubleVec对象分配足够的内存
	void* pVectorMemory = mallocShared(sizeof(SharedDoubleVec)); 
	//@ 使用"placement new"在内存中创建一个SharedDoubleVec对象
	SharedDoubleVec* pv = new (pVectorMemory)SharedDoubleVec;
	//@  ... //@  使用对象(通过pv)
	pv->~SharedDoubleVec(); //@  析构共享内存中的对象
	freeShared(pVectorMemory); //@  释放最初分配的那一块共享内存
 
	return 0;
}
```



