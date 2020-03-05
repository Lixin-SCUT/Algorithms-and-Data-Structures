题目描述
> 输入数字n，按顺序打印出从1最大的n位十进制数。比如输入3，则打印出1、2、3一直到最大的3位数即999。

书本题解：
> &emsp;&emsp;如果面试题是关于n位的整数并且没有限定n的取值范围，或者输入任意大小的整数，那么这道题目很有可能是需要考虑大数问题的 字符串 是一种简单、有效地表示大数的方法
&emsp;&emsp;如果我们在数字前面补0,就会发现n位所有十进制数其实就是n个从0到9 的全排列。也就是说，我们把数字的每一位都从0到9排列一遍，就得到 了所有的十进制数。只是在打印的时候，排在前面的。不打印出来罢了。
&emsp;&emsp;全排列用递归很容易表达，数字的每一位都可能是0〜9中的一个数， 然后设置下一位。递归结束的条件是我们已经设置了数字的最后一位。
&emsp;&emsp;定义了函数PrintNumbur,在这个函数里，只有在碰到第一个非0的字符之后才开始打印，直至字符串的结尾。

```
void Print1ToMaxOfNDigits_2(int n)
{
    if (n <= 0)
        return;

    char* number = new char[n + 1];
    number[n] = '\0';

    for (int i = 0; i < 10; ++i)
    {
        number[0] = i + '0';
        Print1ToMaxOfNDigitsRecursively(number, n, 0);
    }

    delete[] number;
}

void Print1ToMaxOfNDigitsRecursively(char* number, int length, int index)
{
    if (index == length - 1)
    {
        PrintNumber(number);
        return;
    }

    for (int i = 0; i < 10; ++i)
    {
        number[index + 1] = i + '0';
        Print1ToMaxOfNDigitsRecursively(number, length, index + 1);
    }
}

// 字符串number表示一个数字，数字有若干个0开头
// 打印出这个数字，并忽略开头的0
void PrintNumber(char* number)
{
    bool isBeginning0 = true;
    int nLength = strlen(number);

    for (int i = 0; i < nLength; ++i)
    {
        if (isBeginning0 && number[i] != '0')
            isBeginning0 = false;

        if (!isBeginning0)
        {
            printf("%c", number[i]);
        }
    }

    printf("\t");
}
```
