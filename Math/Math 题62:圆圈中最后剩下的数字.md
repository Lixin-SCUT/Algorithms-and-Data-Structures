题目描述：
> 0, 1, …, n-1这n个数字排成一个圆圈，从数字0开始每次从这个圆圈里
删除第m个数字。求出这个圆圈里剩下的最后一个数字。
（注意：删除后从下一个数字开始计算第m个数）

&emsp;&emsp;第一个想法就是环型链表。建立一个环形链表，然后不断地在里面循环直到只剩下一个节点的next指向自己
遇到的问题：
1. 把n和m的意义搞混了
2. 一开始建立树的时候想返回头节点，但是后来发现需要前一个节点，所以先返回尾节点
3. 一定要理解为啥count是m-1而不是m，最好的例子就是m=1的时候，是不需要进入while(count > 0)循环的，就是要删除当前节点

&emsp;&emsp;然后就是数学解法了
其实就是变量替代法和公式带入替换，比较简单但是很考验逻辑，很值得看一下

```
class Solution {
public:
    int LastRemaining_Solution(int n, int m)
    {
        if(n <= 0 || m <= 0)
            return -1;
        if(n == 1)
            return 0; 
        else
            return (LastRemaining_Solution(n-1,m) + m) % n;
    }
    /*
    struct TreeNode
    {
        int value;
        TreeNode *next;
    };
    
    int LastRemaining_Solution(int n, int m)
    {
        if(n <= 0 || m <= 0)
            return -1;
        TreeNode *preNode = buildTree(n); //别搞混n和m的意义了
        TreeNode *curNode = preNode->next;
        while(curNode->next != curNode)
        {
            int count = m - 1; //这里有点奇葩，注意理解
            while(count > 0)
            {
                preNode = curNode;
                curNode = curNode->next;
                --count;
            }
            preNode->next = curNode->next;
            delete(curNode);
            curNode = preNode->next;
        }
        int value = curNode->value;
        delete(curNode);
        return value;
    }
    
    TreeNode* buildTree(int n)
    {
        int num = 0;
        TreeNode *headNode = new TreeNode;
        headNode->value = num;
        ++num;
        TreeNode *preNode = headNode;
        TreeNode *curNode;
        while(num < n)
        {
            curNode = new TreeNode;
            curNode->value = num;
            preNode->next = curNode;
            preNode = curNode;
            ++num;
        }
        preNode->next = headNode;
        return preNode;
    }
    */
};
```

书本题解：
> 题就是有名的约瑟夫(Josephuse)环问题。我们介绍两种解题方法: 一种方法 是用环形链表模拟圆圈的经典解法;第二种方法是分析每次被删除的数字的规律并直接计算出圆圈中最后剩下的数字。

> 经典的解法，用环形链表模拟圆圈
既然题目中有一个数字圆圈，很自然的想法就是用一个数据结构来模 拟这个圆 圈。在常用的数据结构中，我们很容易想到环形链表。我们可以创 建一个共有 n 个节点 的环形链表，然后每次在这个链表中删除第 m 个节点。
可以用模板库中的std::list 来模拟一个环形链表。由于std::list本身并不是一个环形结构，因此每当迭代器 (Iterator)扫描到链表末尾的时候，我们要记得把迭代器移到链表的头部，这样就相当于按照顺序在一个圆圈里遍历了。
```
int LastRemaining_Solution1(unsigned int n, unsigned int m)

{
    if(n < 1 || m < 1)
        return -1;

    unsigned int i = 0;

    list<int> numbers;
    for(i = 0; i < n; ++ i)
        numbers.push_back(i);

    list<int>::iterator current = numbers.begin();
    while(numbers.size() > 1)
    {
        for(int i = 1; i < m; ++ i)
        {
            current ++;
            if(current == numbers.end())
                current = numbers.begin();
        }

        list<int>::iterator next = ++ current;
        if(next == numbers.end())
            next = numbers.begin();

        -- current;
        numbers.erase(current);
        current = next;
    }

    return *(current);
}
```
> 实际上需要在环形链表里重复遍历很多遍。重复的遍历当然对时间效率有负面的影响。这种方法每删除一个数字需要也步运算，共有n个数字，因此总的时间复杂度是O(n)。 同时这种思路还需要一个辅助链表来模拟圆圈，其空间复杂度是O(n)。

![](file:///Users/lixin/Documents/Gridea/post-images/1584058194761.png)
![](file:///Users/lixin/Documents/Gridea/post-images/1584058211696.png)
```
// ====================方法2====================
int LastRemaining_Solution2(unsigned int n, unsigned int m)
{
    if(n < 1 || m < 1)
        return -1;

    int last = 0;
    for (int i = 2; i <= n; i ++) 
        last = (last + m) % i;

    return last;
}
```
> 这种算法的时间复杂度是O(n)， 空间复杂度是O(1),因此，无论是在时间效率还是在空间效率上都优于第一种方法。

