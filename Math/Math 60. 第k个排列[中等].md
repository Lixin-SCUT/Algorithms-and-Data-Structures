题目描述：
> 给出集合 [1,2,3,…,n]，其所有元素共有 n! 种排列。     
按大小顺序列出所有排列情况，并一一标记，当 n = 3 时, 所有排列如下：      
"123"      
"132"      
"213"      
"231"      
"312"      
"321"      
给定 n 和 k，返回第 k 个排列。       
说明：      
给定 n 的范围是 [1, 9]。      
给定 k 的范围是[1,  n!]。     
示例 1:       
输入: n = 3, k = 3       
输出: "213"      
示例 2:      
输入: n = 4, k = 9      
输出: "2314"       
来源：力扣（LeetCode）       
链接：https://leetcode-cn.com/problems/permutation-sequence      
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。    

最简单的话就是回溯穷举法了，但是很明显应该是有规律的

首先我们将其视为一个迭代的选择过程，有n层迭代，每层选择上一层没选择过的数字，所以我引入了used的bool数组
然后可以固定每一位上数字的时候，它所处的范围都是固定的，
比如123中 如果选定首位为2，那它至少是3起步的，最多为4的，因为选定首位为1的时候可能的数有两个

然后每一层的可能性数量刚好是`(n-1)*(n-2)*...*1`,所以流程如下
1. int num = (k - 1) / counts ; counts为当前位数的可能性个数（比如第一位可能性共有6个）算出第一位所需的used的第num+1个数字
2. 然后注意index初始化为-1（受num+1影响），然后记得将j赋给index
3. 赋值并修改used状态
4. k -= num * counts;减去当前位数所占的数字，然后重复上述步骤计算剩余位
5. 最后一位比较特殊，因为k=0的时候k-1得不到我们想要的结果，所以直接寻找used数组中唯一为true的位数，赋值即可

其实很多地方可以优化的，很明显的一个就是我完全可以用vector保存1234...n，然后每次取出然后erase，但是erase存在内存搬移，本质上时间复杂度变化，主要还是代码更加简洁，但是暂时无头绪如何优化，先看看题解
```
class Solution {
public:
    string getPermutation(int n, int k) {
        string ans ;
        vector<bool> used(n, true);
        int counts = 1;
        for(int i = 1; i < n; ++i)
        {
            counts *= i;
        }
        
        
        
        for(int i = 0; i < n; ++i)
        {
            if(i < n - 1)
            {
                int num = (k - 1) / counts ;
                int index = -1;
                for(int j = 0;j < n; ++j)
                {
                    if(used[j])
                    {
                        ++index;
                        if(index == num)
                        {
                            index = j;
                            break;
                        }
                    }   
                }
                ans.push_back('1' + index);
                used[index] = false;
            
                k -= num * counts;
                
                counts /= (n - i - 1);
            }
            else
            {
                int j = 0;
                for(; j < n ; ++j)
                {
                    if(used[j])
                    {
                        break;
                    }
                }       
                ans.push_back('1' + j);
            }
        }
        
        return ans;
    }
};
```

网友题解比较精彩， 不过主体也是利用容器保存123...n，然后逐步erase，然后就是回溯法吧

> 深度优先遍历 + 剪枝、双链表模拟      
思路分析：           
比较容易想到的是，使用同「力扣」第 46 题： “全排列” ，即使用回溯搜索的思想，依次得到全排列，输出所求的第 k 个全排列即可。但事实上，我们不必求出所有的全排列。基于以下几点考虑：  
1、我们知道所求排列一定在叶子结点处得到。事实上，进入每一个分支的时候，我们都可以通过递归的层数，直接计算这一分支可以得到的叶子结点的个数；       
这是因为：进入一个分支的时候，我们可以根据已经选定的数的个数，进而确定还未选定的数的个数，然后计算阶乘，就知道这一个分支的叶子结点有多少个。      
2、如果 k 大于这一个分支将要产生的叶子结点数，直接跳过这个分支，这个操作叫“剪枝”；       
3、如果k 小于等于这一个分支将要产生的叶子结点数，那说明所求的全排列一定在这一个分支将要产生的叶子结点里，需要递归求解；      
4、计算阶乘的时候，你可以使用循环计算，特别注意：0!=1，它表示了没有数可选的时候，即表示到达叶子结点了，排列数只剩下 1 个；      
又因为题目中说“给定 n 的范围是 [1,9]，故可以实现把从 0 到 9 的阶乘计算好，放在一个数组里，可以根据索引直接获得阶乘值，见文后“代码 2”。       
下面以示例 2：输入: n=4，k=9，介绍如何使用“回溯 + 剪枝” 的思想得到输出 "2314"。      
（温馨提示：下面的幻灯片中，有几页上有较多的文字，可能需要您停留一下，可以点击右下角的后退 “|◀” 或者前进 “▶|” 按钮控制幻灯片的播放。）       
方法一：借助“回溯”方法中的“剪枝”技巧        
```
import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

public class Solution {

    /**
     * 记录数字是否使用过
     */
    private boolean[] used;

    /**
     * 阶乘数组
     */
    private int[] factorial;

    private int n;
    private int k;
    /**
     * 从根结点到叶子结点的路径
     */
    private List<Integer> path;

    public String getPermutation(int n, int k) {
        this.n = n;
        this.k = k;
        used = new boolean[n + 1];
        Arrays.fill(used, false);

        // 计算阶乘数组
        factorial = new int[n + 1];
        factorial[0] = 1;
        for (int i = 1; i <= n; i++) {
            factorial[i] = factorial[i - 1] * i;
        }

        path = new ArrayList<>(n);
        dfs(0);

        StringBuilder stringBuilder = new StringBuilder();
        for (Integer c : path) {
            stringBuilder.append(c);
        }
        return stringBuilder.toString();
    }

    /**
     * @param index 在这一步之前已经选择了几个数字，其值恰好等于这一步需要确定的索引位置
     * @return
     */
    private void dfs(int index) {
        if (index == n) {
            return;
        }

        // 还未确定的数字的全排列的个数，第 1 次进入的时候是 n - 1
        int cnt = factorial[n - 1 - index];
        for (int i = 1; i <= n; i++) {
            if (used[i]) {
                continue;
            }
            if (cnt < k) {
                k -= cnt;
                continue;
            }
            path.add(i);
            used[i] = true;
            dfs(index + 1);
        }
    }
}
```
> 复杂度分析：       
时间复杂度：O(N^2)，最坏肯定是要找到第N！个，但是第1层只需要比较N-1次，第2层比较N-2次，以此类推，所以最坏是O(N^2)。       
空间复杂度：O(N)，nums、used、pre 都与 N 等长，factorial 数组就 10 个数，是常数级别的。        

> 方法二：双链表模拟      
事实上，上面的过程也可以循环实现，只不过需要借助一个列表，每次选出一个数，就将这个数从列表里面拿出。因为这个列表要支持频繁的删除操作，因此使用双链表，在 Java 中 LinkedList 就是使用双链表实现的。  
```
import java.util.LinkedList;
import java.util.List;

public class Solution {

    public String getPermutation(int n, int k) {
        // 注意：相当于在 n 个数字的全排列中找到索引为 k - 1 的那个数，因此 k 先减 1
        k --;

        int[] factorial = new int[n];
        factorial[0] = 1;
        // 先算出所有的阶乘值
				//  {0,1,2,6,24,120,720,5040,40320,362880,3628800};
        for (int i = 1; i < n; i++) {
            factorial[i] = factorial[i - 1] * i;
        }

        // 因为要频繁做删除，使用链表
        List<Integer> nums = new LinkedList<>();
        for (int i = 1; i <= n; i++) {
            nums.add(i);
        }

        StringBuilder stringBuilder = new StringBuilder();

        // i 表示剩余的数字个数，初始化为 n - 1
        for (int i = n - 1; i >= 0; i--) {
            int index = k / factorial[i] ;
            stringBuilder.append(nums.remove(index));
            k -= index * factorial[i];
        }
        return stringBuilder.toString();
    }
}
```

贴一个C++版
```
class Solution {
    static const vector<int> fac;
public:
    string getPermutation(int n, int k) {
        string res;
        string s = string("123456789").substr(0, n);
        --k;
        while(k > 0)
        {
            size_t i = k/fac[n - 1];
            res.push_back(s[i]);
            s.erase(s.begin() + i);
            k %= fac[n - 1];
            --n;
        }
        return res + s;
    }
};
const vector<int> Solution::fac = {0,1,2,6,24,120,720,5040,40320,362880,3628800};
```

