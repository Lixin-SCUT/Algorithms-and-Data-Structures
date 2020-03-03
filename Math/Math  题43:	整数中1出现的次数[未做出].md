题目描述：
> 输入一个整数n，求1~n这n个整数的十进制表示中1出现的次数。例如，输入12, 1〜12这些整数中包含1的数字有1、10、11和12,共出现了5次。

&emsp;&emsp;一开始把题目看成了统计当前数字中的1....
&emsp;&emsp;暴力法比较麻烦的是不仅要循环递增数字，还要循环数字本身，数字一旦大了时间复杂度就爆炸了
&emsp;&emsp;然后就是书中的规律法，比较有趣，就是使用逐位计算，需要注意的就是
1. 位数为1时的处理
2. 最高位为1的次数需要额外+1
3. 剩余位需要-2（当前位和最高位），理解为何剩余位可以取0-9而不是除去1或者只能取到当前最大值

```
class Solution {
public:
    //数学规律递归法【未做出】
    int NumberOf1Between1AndN_Solution(int n)
    {
        if(n<1)
            return 0;
        string ns=to_string(n);
        int length=ns.size();
        int count=0;
        for(auto i:ns){
            count+=count_one(i-'0',length,n);
            --length;
        }
        return count;
    }
    
    int count_one(int i,int bit,int &n){
        int count=0;
        if(i==0&&bit==1)
            return 0;
        if(bit==1){
            return 1;
        }
        
        //最高位为1的次数
        if(i>1)
            count+=pow(10,bit-1);
        else if(i==1)
            count+=n-1*pow(10,bit-1)+1;
        
        //剩余位数为1的次数 
        count+=i*(bit-1)*pow(10,bit-2);
        n-=i*pow(10,bit-1);
        return count;
    }
    
    
    /*
    //逐个数计算法
    int NumberOf1Between1AndN_Solution(int n)
    {
        if(n<1)
            return 0;
        int count;
        count=0;
        for(int i=1;i<=n;++i){
            count+=count_one(i);
        }
        return count;
    }
    
    int count_one(int n){
        int count=0;
        int temp;
        while(n>0){
            temp=n%10;
            if(temp==1)
                ++count;
            n/=10;
        }
        return count;
    }
    */
};
```

书本题解：
&emsp;&emsp;最直观的方法, 也就是累加1〜n中每个整数1出现的次数。我们可以每次通过对10求余数判断整数的个位数字是不是1。如果这个数字大于10,则除以10之后再判断个位数字是不是1。
&emsp;&emsp;在上述思路中，我们对每个数字都要做除法和求余运算，以求出该数字中1出现的次数。如果输入数字n，n有O(logn)位，我们需要判断每一位是不是1,那么它的时间复杂度是O(nlogn)。当输入的n非常大的时候，需要大量的计算，运算效率不高。
&emsp;&emsp;如果希望不用计算每个数字的1的个数，那就只能去寻找1在数字中出现的规律了。为了找到规律，我们不妨用一个稍微大一点的数字如21345 作为例子来分析。我们把1〜21345的所有数字分为两段：一段是1〜1345： 另一段是1346〜21345。
&emsp;&emsp;我们先看1346〜21345中1出现的次数。1的出现分为两种情况。首先分析1出现在最高位(本例中是万位)的情况。在1346-21345的数字中， 1出现在10000〜19999这10000个数字的万位中，一共出现了10^4次。
&emsp;&emsp;值得注意的是，并不是对所有5位数而言在万位出现的次数都是10000 次。对于万位是1的数字如输入12345, 1只出现在10000-12345的万位, 出现的次数不是1次，而是2346次，也就是除去最高数字之后剩下的数字再加上1 (2345+1=2346次)。
&emsp;&emsp;接下来分析1出现在除最高位之外的其他4位数中的情况。例子中 1346〜21345这20000个数字中后4位中1出现的次数是8000次。由于最高位是2,我们可以再把1346〜21345分成两段：1346〜11345和11346〜21345,每一段剩下的4位数字中，选择其中一位是1，其余三位可以在0〜 9这10个数字中任意选择，因此根据排列组合原则，总共出现的次数是2X 4x10^3=8000 次。
&emsp;&emsp;至于在1〜1345中1出现的次数，我们就可以用递归求得了。这也是 我们为什么要把1〜21345分成1〜1345和1346-21345两段的原因。因为把21345的最高位去掉就变成1345,便于我们采用递归的思路。
&emsp;&emsp;这种思路是每次去掉最高位进行递归，递归的次数和位数相同。一个数字n有O(logn)位，因此这种思路的时间复杂度是O(logn),比前面的原始方法要好很多。
```
int NumberOf1(unsigned int n);

int NumberOf1Between1AndN_Solution1(unsigned int n)
{
    int number = 0;

    for(unsigned int i = 1; i <= n; ++ i)
        number += NumberOf1(i);

    return number;
}

int NumberOf1(unsigned int n)
{
    int number = 0;
    while(n)
    {
        if(n % 10 == 1)
            number ++;

        n = n / 10;
    }

    return number;
}

// ====================方法二====================
int NumberOf1(const char* strN);
int PowerBase10(unsigned int n);

int NumberOf1Between1AndN_Solution2(int n)
{
    if(n <= 0)
        return 0;

    char strN[50];
    sprintf(strN, "%d", n);

    return NumberOf1(strN);
}

int NumberOf1(const char* strN)
{
    if(!strN || *strN < '0' || *strN > '9' || *strN == '\0')
        return 0;

    int first = *strN - '0';
    unsigned int length = static_cast<unsigned int>(strlen(strN));

    if(length == 1 && first == 0)
        return 0;

    if(length == 1 && first > 0)
        return 1;

    // 假设strN是"21345"
    // numFirstDigit是数字10000-19999的第一个位中1的数目
    int numFirstDigit = 0;
    if(first > 1)
        numFirstDigit = PowerBase10(length - 1);
    else if(first == 1)
        numFirstDigit = atoi(strN + 1) + 1;

    // numOtherDigits是01346-21345除了第一位之外的数位中1的数目
    int numOtherDigits = first * (length - 1) * PowerBase10(length - 2);
    // numRecursive是1-1345中1的数目
    int numRecursive = NumberOf1(strN + 1);

    return numFirstDigit + numOtherDigits + numRecursive;
}

int PowerBase10(unsigned int n)
{
    int result = 1;
    for(unsigned int i = 0; i < n; ++ i)
        result *= 10;

    return result;
}
```
