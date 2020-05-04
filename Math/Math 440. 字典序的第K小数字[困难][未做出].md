题目描述： 
> 给定整数 n 和 k，找到 1 到 n 中字典序第 k 小的数字。        
注意：1 ≤ k ≤ n ≤ 10^9。      
示例 :       
输入:       
n: 13   k: 2       
输出:      
10       
解释:         
字典序的排列是 [1, 10, 11, 12, 13, 2, 3, 4, 5, 6, 7, 8, 9]，所以第二小的数字是 10。        
来源：力扣（LeetCode）       
链接：https://leetcode-cn.com/problems/k-th-smallest-in-lexicographical-order       
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。      

这道题非常明显明显也是找规律的
更像是string的顺序吧，但是肯定不可能列出所有可能性再取值的

很明显是需要找规律的
首先判断n的大小，看看能达到哪个级别
比如个位数有1个，十位数有10个，百位数有100

然后每一层加入相应的级别

突然发现我想得太简单了，比如100时跟在10后面而不是19后面的！以此类推，101、1000等都是跟在100后面的

实在是没啥想法，直接看下题解

核心思想就是十叉树的查找，然后利用前缀和子树的数量之间的对应关系

实现需要注意的点：
1. count()可能会使得int溢出，需要使用long
2. ans递增的时候可能会超出n， 所以需要和(n + 1)进行判断，+1是因为算上边界
3. 注意判断条件的设置

```
class Solution {
public:
    int findKthNumber(int n, int k) 
    {
        int prefix = 1;
        int locate = 1;
        int num =  0;
        while(locate < k)
        {
            num = count(prefix, n);
            if(num + locate <= k)
            {
                ++prefix;
                locate += num;
            }
            else
            {
                prefix *= 10;
                ++locate;
            }
        }
        return prefix;
    }
    
    int count(int prefix, int n)
    {
        int ans = 0;
        long cur = prefix;
        long next = cur + 1;
        while(cur <= n)
        {
            ans += min(static_cast<long>(n + 1), next) - cur;
            cur *= 10;
            next *= 10;
        }
        return ans;
    }
   
};
```

网友题解：
十叉树每一个节点都拥有 10 个孩子节点，因为作为一个前缀 ，它后面可以接 0~9 这十个数字。而且你可以非常容易地发现，整个字典序排列也就是对十叉树进行先序遍历。1, 10, 100, 101, ... 11, 110 ...  
回到题目的意思，我们需要找到排在第k位的数。找到他的排位，需要搞清楚三件事情:          
怎么确定一个前缀下所有子节点的个数？       
如果第 k 个数在当前的前缀下，怎么继续往下面的子节点找？         
如果第 k 个数不在当前的前缀，即当前的前缀比较小，如何扩大前缀，增大寻找的范围？      
接下来 ，我们一一拆解这些问题。        
理顺思路：       
1.确定指定前缀下所有子节点数             
现在的任务就是给定一个前缀，返回下面子节点总数。         
我们现在的思路就是用下一个前缀的起点减去当前前缀的起点，那么就是当前前缀下的所有子节点数总和啦。       
```
//prefix是前缀，n是上界
var getCount = (prefix, n) => {
    let cur = prefix;
    let next = prefix + 1;//下一个前缀
    let count = 0;
    //当前的前缀当然不能大于上界
    while(cur <= n) {
        count += next - cur;//下一个前缀的起点减去当前前缀的起点
        cur *= 10; 
        next *= 10;
        // 如果说刚刚prefix是1，next是2，那么现在分别变成10和20
        // 1为前缀的子节点增加10个，十叉树增加一层, 变成了两层
        
        // 如果说现在prefix是10，next是20，那么现在分别变成100和200，
        // 1为前缀的子节点增加100个，十叉树又增加了一层，变成了三层
    }
    return count;//把当前前缀下的子节点和返回去。
}
```
当然，不知道大家发现一个问题没有，当 next 的值大于上界的时候，那以这个前缀为根节点的十叉树就不是满十叉树了啊，应该到上界那里，后面都不再有子节点。因此，count+=next−cur 还是有些问题的，我们来修正这个  问题:
```
count += Math.min(n+1, next) - cur;
```
你可能会问:咦？怎么是 n+1 ,而不是 n 呢？不是说好了 n 是上界吗？          
我举个例子，假若现在上界n为 12，算出以 1 为前缀的子节点数，首先 1 本身是一个节点，接下来要算下面 10，11，12，一共有 4 个子节点。          
那么如果用 Math.min(n,next)−cur 会怎么样？       
这时候算出来会少一个，12 - 10 加上根节点，最后只有 3 个。因此我们务必要写 n+1。         
现在，我们搞定了前缀的子节点数问题。        
2.第k个数在当前前缀下       
现在无非就是往子树里面去看。       
prefix这样处理就可以了。     
```
prefix *= 10
```
3.第k个数不在当前前缀下        
说白了，当前的前缀小了嘛，我们扩大前缀。          
```
prefix ++;
```
```
let findKthNumber = function(n, k) {
  let p = 1;//作为一个指针，指向当前所在位置，当p==k时，也就是到了排位第k的数
  let prefix = 1;//前缀
  while(p < k) {
    let count = getNumber(prefix, n);//获得当前前缀下所有子节点的和
    if(p + count > k) { //第k个数在当前前缀下
      prefix *= 10;
      p++; //把指针指向了第一个子节点的位置，比如11乘10后变成110，指针从11指向了110
    } else if(p + count <= k) { //第k个数不在当前前缀下
      prefix ++;
      p += count;//注意这里的操作，把指针指向了下一前缀的起点
    }
  }
  return prefix;
};
```
```
/**
 * @param {number} n
 * @param {number} k
 * @return {number}
 */
var findKthNumber = function(n, k) {
  let getCount = (prefix, n) => {
    let count =  0;
    for(let cur = prefix, next = prefix + 1; cur <= n; cur *= 10, next *= 10) 
      count += Math.min(next, n+1) - cur;
    return count;
  }
  let p = 1;
  let prefix = 1;
  while(p < k) {
    let count = getCount(prefix, n);
    if(p + count > k) {
      prefix *= 10;
      p++;
    } else if(p + count <= k) {
      prefix ++;
      p += count;
    }
  }
  return prefix;
};
```
