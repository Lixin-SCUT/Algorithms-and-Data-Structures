题目描述
> 数字以0123456789101112131415…的格式序列化到一个字符序列中。在这个序列中，第5位（从0开始计数）是5，第13位是1，第19位是4，等等。请写一个函数求任意位对应的数字。

书本题解：
序列的第1001位是什么？
序列的前10位是0〜9这10个只有一位的数字。显然第1001位在这 10个数字之后，因此这10个数字可以直接跳过。我们再从后面紧跟着的序列中找第991 (991=1001-10)位的数字。
接下来180位数字是90个10-99的两位数。由于991>180,所以第 991位在所有的两位数之后。我们再跳过90个两位数，继续从后面找881 (881=991-180)位。
接下来的2700位是900个100-999的三位数。由于811<2700,所以 第811位是某个三位数中的一位。由于811=270x3+1,这意味着第811位是 从100开始的第270个数字即370的中间一位，也就是7。

```
int digitAtIndex(int index)
{
	if(index < 0)
		return -1;

	int digits = 1;
	while(true)
	{
		int numbers = countOfIntegers(digits);
		if(index < numbers * digits)
			return digitAtIndex(index, digits);

		index -= digits * numbers;
		digits++;
	}

	return -1;
}

int countOfIntegers(int digits)
{
	if(digits == 1)
		return 10;

	int count = (int) std::pow(10, digits - 1);
	return 9 * count;
}

int digitAtIndex(int index, int digits)
{
	int number = beginNumber(digits) + index / digits;
	int indexFromRight = digits - index % digits;
	for(int i = 1; i < indexFromRight; ++i)
		number /= 10;
	return number % 10;
}

int beginNumber(int digits)
{
	if(digits == 1)
		return 0;

	return (int) std::pow(10, digits - 1);
}
```
