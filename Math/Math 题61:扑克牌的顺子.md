 题目描述：
>  从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。
 2～10为数字本身，A为1，J为11，Q为12，K为13，而大、小王可以看成任意数字。
 
&emsp;&emsp;第一个想法是利用大顶堆或者小顶堆，按顺序输出，最后看看间隔数和0的数目之差，时间复杂度 O(nlogn) 空间复杂度O(n)
&emsp;&emsp;第二个想法是利用排序法，思想一样，时间复杂度 O(nlogn)
&emsp;&emsp;书中的题解就是第二个想法，进一步改进时间复杂度的话，因为牌的种类是有限的，并且同样的牌不分顺序，就可以用size固定为14的容器来存储。

```
class Solution {
public:
    bool IsContinuous( vector<int> numbers )
    {
        if(numbers.size() != 5)
            return false;
        vector<int> numCount(14,0);
        for(auto num : numbers)
        {
            ++numCount[num];
        }
        int numZero;
        int needZero;
        int numPre;
        numZero = numCount[0];
        needZero = 0;
        numPre = 0;
        for(int i=1;i < 14;++i)
        {
            if(numCount[i] > 1)
                return false;
            else if(numCount[i] == 1)
            {
                if(numPre == 0)
                    numPre = i;
                else
                {
                    needZero += (i - numPre - 1);
                    numPre = i;
                }
            }
        }
        return numZero >= needZero;
    }
    /*
    //排序法
    bool IsContinuous( vector<int> numbers ) {
        if(numbers.size() != 5)
            return false;
        QuickSort(numbers,0,numbers.size()-1);
        int numZero;
        int preNum;
        int needZero;
        numZero = 0;
        preNum = 0;
        needZero = 0;
        for(auto num : numbers)
        {
            if(num == 0)
                ++numZero;
            else if(num == preNum)
                return false;
            else if(preNum != 0) //必须加上这个判断条件
            {
                needZero += (num - preNum - 1);
                preNum = num;
            }
            else    //也不能漏掉设置第一个preNum
                preNum = num;
        }
        return numZero >= needZero;
    }
    
    void QuickSort(vector<int> &numbers,int beg,int end)
    {
        if(beg < end) //这里不小心写成了while ，debug 才发现
        {
            int mid; 
            mid = SortMid(numbers,beg,end);
            QuickSort(numbers,beg,mid-1);
            QuickSort(numbers,mid+1,end);
        }
    }
    
    int SortMid(vector<int> &numbers,int beg,int end)
    {
        int pivotkey = numbers[beg];
        while(beg < end)
        {
            while(beg < end && numbers[end] >= pivotkey)
                --end;
            numbers[beg] = numbers[end];
            while(beg < end && numbers[beg] <= pivotkey)
                ++beg;
            numbers[end] = numbers[beg];
        }
        numbers[beg] = pivotkey;
        return beg;
    }
    */
};
```

书本题解：
> 最直观的方法是把数组排序。值得注意的是，由于0可以当成任意数字，我们可以用0去补满数组中的空缺。如果排序之后的数组不是连续的，即相邻的两个数字相 隔若干个数字，那么只要我们有足够的0可以补满这两个数字的空缺，这个数组实际上还是连续的。
首先把数组排序;其次统计数组中0的个数;最 后统计排序之后的数组中相邻数字之间的空缺总数。如果空缺的总数小于或者等于0的个数，那么这个数组就是连续的;反之则不连续。
注意一点:如果数组中的非 0 数字重复出现，则该数组不是连续的。
由于扑克牌的值出现在 0〜13 之间，我们可 以定义一个长度为 14 的哈希表，这样在 O(n)时间内就能完成排序

```
bool IsContinuous(int* numbers, int length)
{
    if(numbers == nullptr || length < 1)
        return false;

    qsort(numbers, length, sizeof(int), Compare);

    int numberOfZero = 0;
    int numberOfGap = 0;

    // 统计数组中0的个数
    for(int i = 0; i < length && numbers[i] == 0; ++i)
        ++numberOfZero;

    // 统计数组中的间隔数目
    int small = numberOfZero;
    int big = small + 1;
    while(big < length)
    {
        // 两个数相等，有对子，不可能是顺子
        if(numbers[small] == numbers[big])
            return false;

        numberOfGap += numbers[big] - numbers[small] - 1;
        small = big;
        ++big;
    }

    return (numberOfGap > numberOfZero) ? false : true;
}

int Compare(const void *arg1, const void *arg2)
{
    return *(int*) arg1 - *(int*) arg2;
}
```


 
