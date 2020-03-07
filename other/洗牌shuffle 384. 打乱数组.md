> 打乱一个没有重复元素的数组。
示例:
// 以数字集合 1, 2 和 3 初始化数组。
int[] nums = {1,2,3};
Solution solution = new Solution(nums);
// 打乱数组 [1,2,3] 并返回结果。任何 [1,2,3]的排列返回的概率应该相同。
solution.shuffle();
// 重设数组到它的初始状态[1,2,3]。
solution.reset();
// 随机返回数组[1,2,3]打乱后的结果。
solution.shuffle();
来源：力扣（LeetCode）
链接：https://leetcode-cn.com/problems/shuffle-an-array
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。

这道题其实就是针对洗牌算法而设，但是注意虽然主要功能是随机函数，但是我们的重点是实现随机函数，而是通过随机函数生成的随机数的实现打乱效果。
这次的重点就是 学习C++自带的随机数生成 和 shuffle打乱洗牌算法

```
class Solution {
public:
    Solution(vector<int>& nums) {
        origNums = nums;
    }
    
    /** Resets the array to its original configuration and return it. */
    vector<int> reset() {
        return origNums;
    }
    
    
    /** Returns a random shuffling of the array. */
    /*
		// 取余法
    vector<int> shuffle() 
    {
        randNums = reset();
        int length = origNums.size();

        for(int cur = 0 ; cur < length ; ++cur)
        {
            int randNum = rand() % (length - cur) + cur;
            swap(randNums[cur], randNums[randNum]);
        }
        
        return randNums;
    }
    */
    
		// 划定随机数范围 
    vector<int> shuffle() {
        randNums = reset();
        int length = origNums.size();
        // default_random_engine num;  //是不能放在这里的，否则每次调用都会重新定义
        for(int cur = 0; cur < length; ++cur)
        {
            uniform_int_distribution<unsigned> num(cur,length-1);
            swap(randNums[cur], randNums[num(rand)]);
        }
        return randNums;
    }
    

private:
    default_random_engine rand; // 重点 得把随机引擎放在外面
    vector<int> randNums;
    vector<int> origNums;
};

/**
 * Your Solution object will be instantiated and called as such:
 * Solution* obj = new Solution(nums);
 * vector<int> param_1 = obj->reset();
 * vector<int> param_2 = obj->shuffle();
 */
```

可以看到实现还是很简单的，主要还是将排列组合的概率论思想转化为循环
然后细节就是C++的随机数生成
1.注意随机引擎不能每次都初始化，除非额外确定随机种子，否则必须保持只有一个随机引擎
2.注意取余的除数选择，以及随机数范围的选择

网友题解：
同样是比较长的网友题解，我就不画蛇添足了
[洗牌算法深度详解](https://leetcode-cn.com/problems/shuffle-an-array/solution/xi-pai-suan-fa-shen-du-xiang-jie-by-labuladong/)

洗牌算法

此类算法都是靠随机选取元素交换来获取随机性，直接看代码（伪码），该算法有 4 种形式，都是正确的：

```
// 得到一个在闭区间 [min, max] 内的随机整数
int randInt(int min, int max);

// 第一种写法
void shuffle(int[] arr) {
    int n = arr.length();
    /******** 区别只有这两行 ********/
    for (int i = 0 ; i < n; i++) {
        // 从 i 到最后随机选一个元素
        int rand = randInt(i, n - 1);
        /*************************/
        swap(arr[i], arr[rand]);
    }
}

// 第二种写法
    for (int i = 0 ; i < n - 1; i++)
        int rand = randInt(i, n - 1);

// 第三种写法
    for (int i = n - 1 ; i >= 0; i--)
        int rand = randInt(0, i);

// 第四种写法
    for (int i = n - 1 ; i > 0; i--)
        int rand = randInt(0, i);
```
> 假设数组有五个元素，我们先用这个准则分析一下第一种写法的正确性：
for 循环第一轮迭代时，i = 0，rand 的取值范围是 [0, 4]，有 5 个可能的取值。
for 循环第二轮迭代时，i = 1，rand 的取值范围是 [1, 4]，有 4 个可能的取值。
后面以此类推，直到最后一次迭代，i = 4，rand 的取值范围是 [4, 4]，只有 1 个可能的取值。
可以看到，整个过程产生的所有可能结果有` n! = 5! = 5*4*3*2*1 `种，所以这个算法是正确的。
分析第二种写法，前面的迭代都是一样的，少了一次迭代而已。所以最后一次迭代时 i = 3，rand 的取值范围是 [3, 4]，有 2 个可能的取值。
所以整个过程产生的所有可能结果仍然有` 5*4*3*2 = 5! = n! `种，因为乘以 1 可有可无嘛。所以这种写法也是正确的。
如果以上内容你都能理解，那么你就能发现第三种写法就是第一种写法，只是将数组从后往前迭代而已；第四种写法是第二种写法从后往前来。所以它们都是正确的。
