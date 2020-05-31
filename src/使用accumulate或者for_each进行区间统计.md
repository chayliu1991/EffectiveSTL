# 使用accumulate或者for_each进行区间统计

```
//@ 接受当前的长度总和值和新的字符串，然后返回更新之后的总和值
std::string::size_type stringLengthSum(std::string::size_type sumSoFar, const std::string& s)
{
	return sumSoFar + s.size();
}

struct Point {
	Point(double initX, double initY) : x(initX), y(initY) {}
	double x, y;
};

class PointAverage : public std::unary_function<Point, void> {
public:
	PointAverage() : xSum(0), ySum(0), numPoints(0) {}

	void operator()(const Point& p)
	{
		++numPoints;
		xSum += p.x;
		ySum += p.y;
	}

	Point result() const
	{
		return Point(xSum / numPoints, ySum / numPoints);
	}

private:
	size_t numPoints;
	double xSum, ySum;
};

int test_item_37()
{
	std::vector<double> v{ 1.0f, 2.2f, 3.3f, 4.5f };
	double sum = std::accumulate(v.cbegin(), v.cend(), 0.0); //@ 注意：初始值被指定为0.0,而不是简单的0
	fprintf(stdout, "vaule: %f\n", sum); //@ 11.000000

	fprintf(stdout, "sum of the ints: %d\n", std::accumulate(std::istream_iterator<int>(std::cin), std::istream_iterator<int>(), 0)); //@ 输入非整数值结束,如字母

	std::set<std::string> ss{ "abc", "cde", "xyzw" };
	//@ 计算一个容器中字符串的长度总和
	std::string::size_type lengthSum = std::accumulate(ss.cbegin(), ss.cend(),
		static_cast<std::string::size_type>(0), stringLengthSum);
	fprintf(stdout, "length sum: %d\n", lengthSum); //@ 10

	//@ 计算一个区间中数值的乘积
	std::vector<float> vf{ 1.f, 2.f, 3.f, 1.5f };
	float product = std::accumulate(vf.cbegin(), vf.cend(), 1.f, std::multiplies<float>());
	fprintf(stdout, "product: %f\n", product); //@ 9.000000

	//@ 计算出一个区间中所有点的平均值
	std::list<Point> lp{ { 1, 2 },{ 2, 3 },{ 3, 4 },{ 4, 5 } };
	Point avg = std::for_each(lp.cbegin(), lp.cend(), PointAverage()).result();

	return 0;
}
```

`std::accumulate` 有两种形式：第一种形式有两个迭代器和一个初始值，它返回该初始值加上由迭代器标识的区间中的值的总和。

`std::accumulate` 只要求输入迭代器，所以你可以使用 `std::istream_iterator` 和 `std::istreambuf_iterator`。

`std::accumulate` 的第二种形式带一个初始值和一个任意的统计函数。

`std::for_each` 是另一个可被用来统计区间的算法，而且它不受 `accumulate` 的那些限制。如同 `accumulate` 一样，`for_each` 也带两个参数：一个是区间，另一个是函数(通常是函数对象)----对区间中的每个元素都要调用这个函数，但是，传给 `for_each` 的这个函数只接收一个实参(即当前的区间元素)。`for_each` 执行完毕后会返回它的函数。(实际上，它返回的是这个函数的一份拷贝。)重要的是，传给 `for_each` 的函数(以及后来返回的函数)可以有副作用。

`std::for_each` 和 `std::accumulate` 在两个方面有所不同：首先，名字 `accumulate` 暗示着这个算法将会计算出一个区间的统计信息。而 `for_each` 听起来就好像是对一个区间的每个元素做一个操作。用 `for_each` 来统计一个区间是合法的，但是不如 `accumulate` 来得清晰。其次，`accumulate` 直接返回我们所要的统计结果，而 `for_each` 却返回一个函数对象，我们必须从这个函数对象中提取出我们所要的统计信息。在C++中，这意味着我们必须在函数子类中 加入一个成员函数，以便获得我们想要的统计信息。









