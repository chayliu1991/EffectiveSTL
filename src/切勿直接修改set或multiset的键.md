#  切勿直接修改set或multiset的键　　

`set`、`multiset`、`map`、`multimap` 都会按照一定的顺序存储其中的元素，但如果修改了其中用于排序的键值，则将会破坏容器的有序性。

对于 `map` 和 `multimap` 而言，其存储元素的类型为 `pair<const key, value>`，修改 `map` 中的 `key` 值将不能通过编译(除非使用 `const_cast` )。
对于 `set` 和 `multiset`，其存储的键值并不是 `const` 的，在修改其中元素的时候，要小心不要修改到键值。

```

int test_item_22()
{
	std::map<int, std::string> m{ { 0, "xxx" } };
	//m.begin()->first = 10; // build error, map的键不能修改
 
	std::multimap<int, std::string> mm{ { 1, "yyy" } };
	//mm.begin()->first = 10; // build error, multimap的键同样不能修改
 
	std::set<int> s{ 1, 2, 3 };
	//*(s.begin()) = 10; // build error, set的键不能修改
	const_cast<int&>(*s.begin()) = 10; // 强制类型转换
 
	std::vector<int> v{ 1, 2, 3 };
	*v.begin() = 10;
 
	return 0;
}
```

