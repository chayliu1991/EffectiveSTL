# 理解copy_if算法的正确实现

```
int test_item_36()
{
	std::vector<int> v1{ 1, 2, 3, 4, 5 }, v2(v1.size());
 
	auto it = std::copy_if(v1.begin(), v1.end(), v2.begin(), [](int i) { return (i % 2 == 1); });
	v2.resize(std::distance(v2.begin(), it));
 
	for (const auto& v : v2)
		fprintf(stdout, "%d\n", v); // 1 3 5
 
	return 0;
}
```

C++11 中增加了 `std::copy_if` 函数。

