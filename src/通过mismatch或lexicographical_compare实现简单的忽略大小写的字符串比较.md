# 通过mismatch或lexicographical_compare实现简单的忽略大小写的字符串比较

```
//@ 忽略大小写地比较字符c1和c2，如果c1<c2,返回-1；如果c1>c2,返回1；如果c1==c2,返回0
int ciCharCompare(char c1, char c2)
{
	int lc1 = std::tolower(static_cast<unsigned char>(c1));
	int lc2 = std::tolower(static_cast<unsigned char>(c2));

	if (lc1 < lc2) return -1;
	if (lc1 > lc2) return 1;
	return 0;
}

int ciStringCompareImpl(const std::string& s1, const std::string& s2)
{
	typedef std::pair<std::string::const_iterator, std::string::const_iterator> PSCI;
	PSCI p = std::mismatch(s1.begin(), s1.end(), s2.begin(), std::not2(std::ptr_fun(ciCharCompare)));

	if (p.first == s1.end()) { //@ 如果为true,要么s1和s2相等，或者s1比s2短
		if (p.second == s2.end()) return 0;
		else return -1;
	}

	return ciCharCompare(*p.first, *p.second); //@ 字符串之间的关系和这两个不匹配的字符之间的关系相同
}

int ciStringCompare(const std::string& s1, const std::string& s2)
{
	//@ 把短的字符串作为第一个区间传入
	if (s1.size() <= s2.size()) return ciStringCompareImpl(s1, s2);
	else return -ciStringCompareImpl(s2, s1);
}

//@ 返回在忽略大小写的情况下，c1是否在c2之前
bool ciCharLess(char c1, char c2)
{
	return std::tolower(static_cast<unsigned char>(c1)) <
		std::tolower(static_cast<unsigned char>(c2));
}

bool ciStringCompare2(const std::string& s1, const std::string& s2)
{
	return std::lexicographical_compare(s1.begin(), s1.end(), s2.begin(), s2.end(), ciCharLess);
}

bool ciStringCompare3(const std::string& s1, const std::string& s2)
{
	//@ 前提：不考虑国际化支持，也确定字符串中不会包含内嵌的空字符
	return strcmp(s1.c_str(), s2.c_str());
}

int test_item_35()
{
	std::string str1{ "xxz" }, str2{ "xxx" };
	fprintf(stdout, "str1:str2: %d\n", ciStringCompare(str1, str2));
	fprintf(stdout, "str1:str2: %d\n", ciStringCompare2(str1, str2));
	fprintf(stdout, "str1:str2: %d\n", ciStringCompare3(str1, str2));

	return 0;
}
```

`std::lexicographical_compare` 是 `strcmp` 的一个泛化版本。不过，`strcmp` 只能与字符数组一起工作，而`lexicographical_compare` 则可以与任何类型的值的区间一起工作。而且，`strcmp` 总是通过比较两个字符来判断它们的关系相等、小于还是大于，而 `lexicographical_compare` 则可以接受一个判别式，由该判别式来决定两个值是否满足一个用户自定义的准则。

`strcmp` 通常是被优化过的，它们在字符串的处理上一般要比通用算法 `mismatch` 和 `lexicographical_compare` 快。
