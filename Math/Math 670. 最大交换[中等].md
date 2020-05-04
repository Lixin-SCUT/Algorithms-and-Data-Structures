题目描述：
> 给定一个非负整数，你至多可以交换一次数字中的任意两位。返回你能得到的最大值。       
示例 1 :              
输入: 2736       
输出: 7236       
解释: 交换数字2和数字7。      
示例 2 :      
输入: 9973        
输出: 9973             
解释: 不需要交换。        
注意:       
给定数字的范围是 [0, 10^8]       
来源：力扣（LeetCode）        
链接：https://leetcode-cn.com/problems/maximum-swap      
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。       

第一想法是 首先找出最大的数字
如果在第一位，从第二位继续
如果不在第一个位，就和第一位替换

然后考虑到多个最大数字的情况，就把最后一个和第一位更换就好

实现中需要注意的问题
1. 每次循环都需要更新max和index，我直接初始化为-1
2. 由于必定大于-1 所以判断条件不用担心max等于-1
3. 然后需要额外考虑max是否和第一位相同，相同的话作废
4. 时间复杂度有点高，而且我用的是转化为字符串

```
class Solution {
public:
    int maximumSwap(int num) {
        string num_str = to_string(num);
        int length = num_str.size();
        
        for(int i = 0; i < length; ++i)
        {
            int max = -1;
            int index = -1;
            for(int j = i; j < length; ++j)
            {
                if(num_str[j] >= max)
                {
                   max =  num_str[j];
                index = j;
                }
            }
            if(max != num_str[i])
            {
                swap(num_str[i], num_str[index]);
                break;
            }
        }
        return stoi(num_str);
    }
};
```

网友题解：
> 这题我们的想法肯定是，尽量交换前面的大数位，并且和它交换的数还得是在它后面大于它的最大数          
倒序使用数组存储下来每个位置，在它及它以后的最大数的索引        
然后再正序从一个数开始遍历，如果它及它以后的最大数不是它本身，那么这个数就是我们需要交换的。        
```
class Solution {
    public int maximumSwap(int num) {
        char[] c = String.valueOf(num).toCharArray();
        int max = Integer.MIN_VALUE;
        int max_index = 0;
        int [] arr = new int[c.length];
        arr[c.length - 1] = c.length - 1;
        
        for (int i = c.length - 1; i >= 0; i --) {
            if (c[i] - '0' > max) {
                max = c[i] - '0';
                max_index = i;
            }
            arr[i] = max_index;
        }
        for (int i = 0; i < c.length; i ++) {
            if (arr[i] != i && c[arr[i]] != c[i]) {
                char tmp = c[i];
                c[i] = c[arr[i]];
                c[arr[i]] = tmp;
                break;
            }
        }
        return Integer.parseInt(new String(c));
        
    }
}
```
> 时间复杂度：O(n)    
空间复杂度：O(n)        

官方解法：
> 方法一：暴力法        
数字最多只有 8 位，因此只有 28 个可用的互换。       
算法：             
我们将数字存储为长度为 len(num) 的列表。对于位置为 (i, j) 的每个候选交换，我们交换数字并记录组成的新数字是否大于当前答案，然后交换回来恢复原始数字。   
唯一的细节可能是检查我们没有引入前导零。我们实际上不需要检查它，因为我们的原始数据没有。                          
复杂度分析        
时间复杂度：O(N^3)。其中 N 是输入数字的总位数。对于每对数字，我们最多花费 O(N) 的时间来比较最后的序列。         
空间复杂度：O(N)，存储在 A 中的信息。        
方法二：贪心算法        
算法：          
我们将计算 last[d] = i，最后一次出现的数字 d（如果存在）的索引 i。          
然后，从左到右扫描数字时，如果将来有较大的数字，我们将用最大的数字交换；如果有多个这样的数字，我们将用最开始遇到的数字交换。      
复杂度分析          
时间复杂度：O(N)。其中，N 是输入数字的总位数。每个数字最多只考虑一次。      
空间复杂度：O(1)，last 使用的额外空间最多只有 10 个。       

注意方法二其实就是前一个方法的固定数组版，并且是从前往后遍历，更加方便一点
