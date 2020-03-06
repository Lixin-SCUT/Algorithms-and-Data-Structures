题目描述
> 把只包含质因子2、3和5的数称作丑数（Ugly Number）。例如6、8都是丑数，但14不是，因为它包含质因子7。 习惯上我们把1当做是第一个丑数。求按从小到大的顺序的第N个丑数。

&emsp;&emsp;一开始我想着循环\*2 \*3 \*5，但是突然发现不对啊，比如4，4应该优先\*2而不是\*3，
也就是说得到的下一个值应该先\*2，然后再考虑上一个值\*3之类的
当然马上发现也不是绝对，比如1\*2后应该继续1\*3而不是2\*2
似乎可以用一个大顶堆hhh

&emsp;&emsp;然后就是书里的办法吧，类似于动态规划，首先从乘2 乘3 乘5的位置出发，比较三者的最小值，只有最小那个可以前进一步
暴力循环法的话就是理解取余再除以的思想就行，然后超时了，但是ide中通过了数据比

```
class Solution {
public:
    /*
    //暴力循环法，超时
    int GetUglyNumber_Solution(int index){
        if(index<1)
            return 0;
        int count,res;
        count=1;
        res=1;
        while(count<index){
            ++res;
            int temp=res;
            while(temp%2==0)
                temp/=2;
            while(temp%3==0)
                temp/=3;
            while(temp%5==0)
                temp/=5;
            if(temp==1)
                ++count;
        }
        return res;
    }
    */
    
    
    //动态规划
    int GetUglyNumber_Solution(int index) {
        if(index<1)
            return 0;
        vector<int> uglynum(index,1);
        int multiby2,multiby3,multiby5,count;
        multiby2=0;
        multiby3=0;
        multiby5=0;
        count=1;
        while(count<index){
            int cur_min=min(uglynum[multiby2]*2,uglynum[multiby3]*3,uglynum[multiby5]*5);
            uglynum[count]=cur_min;
            while(uglynum[multiby2]*2<=cur_min)
                ++multiby2;
            while(uglynum[multiby3]*3<=cur_min)
                ++multiby3;
            while(uglynum[multiby5]*5<=cur_min)
                ++multiby5;
            ++count;
        }
        return uglynum[index-1];
    }
    
    int min(int multiby2,int multiby3,int multiby5){
        int temp=std::min(multiby2,multiby3);
        return std::min(temp,multiby5);
    } 
};
```

书本题解：
> &emsp;&emsp;逐个判断每个整数是不是丑数的解法,直观但不够高效
&emsp;&emsp;所谓一个数m是另一个数n的因子，是指n能被m整除 也就是n%m = 0。根据丑数的定义，丑数只能被2、3和5整除。也就是说，如果一个数能被2整除，就连续除以2；如果能被3整除，就连续除以3；如果能被5 整除，就除以连续5。如果最后得到的是1,那么这个数就是丑数；否则不是。
```
bool IsUgly(int number)
{
    while(number % 2 == 0)
        number /= 2;
    while(number % 3 == 0)
        number /= 3;
    while(number % 5 == 0)
        number /= 5;

    return (number == 1) ? true : false;
}

int GetUglyNumber_Solution1(int index)
{
    if(index <= 0)
        return 0;

    int number = 0;
    int uglyFound = 0;
    while(uglyFound < index)
    {
        ++number;

        if(IsUgly(number))
            ++uglyFound;
    }

    return number;
}
```
> 创建数组保存已经找到的丑数，用空间换时间的解法
&emsp;&emsp;前面的算法之所以效率低，很大程度上是因为不管一个数是不是丑数， 我们都要对它进行计算。接下来我们试着找到一种只计算丑数的方法，而不在非丑数的整数上花费时间。根据丑数的定义，丑数应该是另一个丑数乘以2、3或者5的结果(1除外)。因此，我们可以创建一个数组，里面的数字是排好序的丑数，每个丑数都是前面的丑数乘以2、3或者5得到的。
&emsp;&emsp;这种思路的关键在于怎样确保数组里面的丑数是排好序的。假设数组中已经有若干个排好序的丑数，并且把已有最大的丑数记作M,接下来分析如何生成下一个丑数。该丑数肯定是前面某一个丑数乘以2、3或者5的 结果，所以我们首先考虑把已有的每个丑数乘以2。在乘以2的时候，能得到若干个小于或等于M的结果。由于是按照顺序生成的，小于或者等于M 肯定己经在数组中了，我们不需再次考虑；还会得到若干个大于M的结果， 但我们只需要第一个大于M的结果，因为我们希望丑数是按从小到大的顺序生成的，其他更大的结果以后再说。我们把得到的第一个乘以2后大 M的结果记为M2，同样，我们把已有的每个丑数乘以3和5,能得到第一 个大于M的结果M3和M5。那么下一个丑数应该是M2、M3和M5这3个数的最小者。
&emsp;&emsp;在前面分析的时候提到把已有的每个丑数分别乘以2、3和5。事实上这不是必需的，因为已有的丑数是按顺序存放在数组中的。对于乘以2而言，肯定存在某一个丑数T2，排在它之前的每个丑数乘以2得到的结果都会小于已有最大的丑数，在它之后的每个丑数乘以2得到的结果都会太大。 我们只需记下这个丑数的位置，同时每次生成新的丑数的时候去更新这个T2即可。对于乘以3和5而言，也存在同样的T3和T5。
```
int GetUglyNumber_Solution2(int index)
{
    if(index <= 0)
        return 0;

    int *pUglyNumbers = new int[index];
    pUglyNumbers[0] = 1;
    int nextUglyIndex = 1;

    int *pMultiply2 = pUglyNumbers;
    int *pMultiply3 = pUglyNumbers;
    int *pMultiply5 = pUglyNumbers;

    while(nextUglyIndex < index)
    {
        int min = Min(*pMultiply2 * 2, *pMultiply3 * 3, *pMultiply5 * 5);
        pUglyNumbers[nextUglyIndex] = min;

        while(*pMultiply2 * 2 <= pUglyNumbers[nextUglyIndex])
            ++pMultiply2;
        while(*pMultiply3 * 3 <= pUglyNumbers[nextUglyIndex])
            ++pMultiply3;
        while(*pMultiply5 * 5 <= pUglyNumbers[nextUglyIndex])
            ++pMultiply5;

        ++nextUglyIndex;
    }

    int ugly = pUglyNumbers[nextUglyIndex - 1];
    delete[] pUglyNumbers;
    return ugly;
}

int Min(int number1, int number2, int number3)
{
    int min = (number1 < number2) ? number1 : number2;
    min = (min < number3) ? min : number3;

    return min;
}
```
> 和第一种思路相比，第二种思路不需要在非丑数的整数上进行任何计算，因此时间效率有明显提升。但也需要指出，第二种算法由于需要保存已经生成的丑数，则因此需要一个数组，从而增加了空间消耗。如果是求第1500个丑数，则将创建一个能容纳1500个丑数的数组，这个数组占据 6KB的内容空间。而第一种思路没有这样的内存开销。总的来说，第二种思路相当于用较小的空间消耗换取了时间效率的提升。
