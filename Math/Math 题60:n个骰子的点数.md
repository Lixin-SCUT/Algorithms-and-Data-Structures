题目描述：把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出s的所有可能的值出现的概率。

书本题解：
> 一共有 6 个面，每个面上都有一个点数, 对应的 是1〜6之间的一个数字。所以n个骰子的点数和的最小值为n，最大值为6n。另外，根据排列组合的知识，我们还知道n个骰子的所有点数 的排列数为 6^n。要解决这个问题，我们需要先统计出每个点数出现的次数，然后把每个点数出现的次数除以 6^n,就能求出每个点数出现的概率。

> 解法一:基于递归求骰子点数,时间效率不够高
要想求出n个骰子的点数和，可以先把n个骰子分为两堆:第一堆只有一个;另一堆有n-1个。单独的那一个有可能出现1〜6的点数。我们需要计算1〜6的每一种点数和剩下的n-1个骰子来计算点数和。接下来把剩下的n-1个骰子仍然分成两堆:第一堆只有一个;第二堆有n-2个。我们把上一轮那个单独骰子的点数和这一轮单独骰子的点数相加，再和剩下的个骰子来计算点数和。分析到这里，我们不难发现这是一种递归的思路，递归结束的条件就是最后只剩下一个骰子。
我们可以定义一个长度为6n-n+1的数组，将和为s的点数出现的次数保存到数组的第s-n个元素里。

```
int g_maxValue = 6;

void PrintProbability_Solution1(int number)
{
    if(number < 1)
        return;
 
    int maxSum = number * g_maxValue;
    int* pProbabilities = new int[maxSum - number + 1];
    for(int i = number; i <= maxSum; ++i)
        pProbabilities[i - number] = 0;
 
    Probability(number, pProbabilities);
 
    int total = pow((double)g_maxValue, number);
    for(int i = number; i <= maxSum; ++i)
    {
        double ratio = (double)pProbabilities[i - number] / total;
        printf("%d: %e\n", i, ratio);
    }
 
    delete[] pProbabilities;
}
 
void Probability(int number, int* pProbabilities)
{
    for(int i = 1; i <= g_maxValue; ++i)
        Probability(number, number, i, pProbabilities);
}
 
void Probability(int original, int current, int sum, 
                 int* pProbabilities)
{
    if(current == 1)
    {
        pProbabilities[sum - original]++;
    }
    else
    {
        for(int i = 1; i <= g_maxValue; ++i)
        {
            Probability(original, current - 1, i + sum, pProbabilities);
        }
    }
} 
```
> 上述思路很简洁，实现起来也容易。但由于是基于递归的实现，它有很多计算是重复的，从而导致当 number 变大时性能慢得让人不能接受。

> 解法二:基于循环求骰子点数，时间性能好
可以换一种思路来解决这个问题。我们可以考虑用两个数组来存储骰子点数的每个总数出现的次数。在一轮循环中，第一个数组中的第 n 个数 字表示骰子和为n
出现的次数。在下一轮循环中，我们加上一个新的骰子, 此时和为 n 的骰子出现的次数应该等于上一轮循环中骰子点数和为n-1、 n-2、n-3、n-4、n-5与 n-6的次 数的总和，所以我们把另一个数组的第 n 个数字设为前一个数组对应的第n-1、 n-2、n-3、n-4、n-5与 n-6 个数字之和。
(需要两个数组的原因：比如第一次得到123456.第二次则是234567...12,注意23456会重复，而且这五个没有n-6，会超出数组范围)
```
void PrintProbability_Solution2(int number)
{
    if(number < 1)
        return;

    int* pProbabilities[2];
    pProbabilities[0] = new int[g_maxValue * number + 1];
    pProbabilities[1] = new int[g_maxValue * number + 1];
    for(int i = 0; i < g_maxValue * number + 1; ++i)
    {
        pProbabilities[0][i] = 0;
        pProbabilities[1][i] = 0;
    }
 
    int flag = 0;
    for (int i = 1; i <= g_maxValue; ++i) 
        pProbabilities[flag][i] = 1; 
    
    for (int k = 2; k <= number; ++k) 
    {
        for(int i = 0; i < k; ++i)
            pProbabilities[1 - flag][i] = 0;

        for (int i = k; i <= g_maxValue * k; ++i) 
        {
            pProbabilities[1 - flag][i] = 0;
            for(int j = 1; j <= i && j <= g_maxValue; ++j) 
                pProbabilities[1 - flag][i] += pProbabilities[flag][i - j];
        }
 
        flag = 1 - flag;
    }
 
    double total = pow((double)g_maxValue, number);
    for(int i = number; i <= g_maxValue * number; ++i)
    {
        double ratio = (double)pProbabilities[flag][i] / total;
        printf("%d: %e\n", i, ratio);
    }
 
    delete[] pProbabilities[0];
    delete[] pProbabilities[1];
}
```
> 上述代码中，我们定义了两个数组 pProbabilities[0]和 pProbabilities[l] 来存储骰 子的点数之和。在一轮循环中，一个数组的第 n 项等于另一个数组的第n-1、 n-2、n-3、n-4、n-5与 n-6项的和。在下一轮循环中，我们交换这两个数组(通过改变变量 flag 实现)再 重复这一计算过程。
值得注意的是，上述代码没有在函数里把一个骰子的最大点数硬编码 (HardCode) 为 6,而是用一个变量 g maxValue 来表示。

书本题解确实很巧妙
1.从k开始外循环，不用考虑k之前的数，内循环则循环递增n-1至n-6的新骰子数
2.利用两个数组轮流存储，并且利用bool隐式转换数组下标，精简了代码
