> 题目描述    
定义栈的数据结构，请在该类型中实现一个能够得到栈中所含最小元素的min函数（时间复杂度应为O（1））。      
注意：保证测试中不会当栈为空的时候，对栈调用pop()或者min()或者top()方法。       
     
```
class Solution {
public:
    void push(int value) {
        norm_stack.push(value);
        if(min_stack.empty()||value<=min_stack.top())
            min_stack.push(value);
        else if(value>min_stack.top())
            min_stack.push(min_stack.top());
    }
    void pop() {
        if(norm_stack.empty())
            return;
        norm_stack.pop();
        min_stack.pop();
    }
    int top() {
        if(norm_stack.empty())
            return -1;
        return norm_stack.top();
    }
    int min() {
        if(min_stack.empty())
            return -1;
        return min_stack.top();
    }

private:
    stack<int> norm_stack;
    stack<int> min_stack;
};
```

书本题解：  
> 看到这个问题，我们的第一反应可能是每次压入一个新元素进栈时，将栈里的所有元素排序，让最小的元素位于栈顶，这样就能在0(1)时间内得到 小元素了。但这种思路不能保证最后压入栈的元素能够最先出栈，因此这个数据结构已经不是栈了。  
我们接着想到在栈里添加一个成员变量存放最小的元素。每次压入一个新元素进栈的时候，如果该元素比当前最小的元素还要小，则更新最小元素。但如果当前最小的元素被弹出栈了，那么如何得到下一个最小的元素呢？  
分析到这里我们发现，仅仅添加一个成员变量存放最小元素是不够的， 也就是说当最小元素被弹出栈的时候，我们希望能够得到次小元素。因此, 在压入这个最小元素之前，我们要把次小元素保存起来。  
所以可以把每次的最小元素（之前的最小元素和新压入栈的元素两者的较小值）都保存起来放到另外一个辅助栈里  
  
```
template <typename T> class StackWithMin
{
public:
    StackWithMin() {}
    virtual ~StackWithMin() {}

    T& top();
    const T& top() const;

    void push(const T& value);
    void pop();

    const T& min() const;

    bool empty() const;
    size_t size() const;

private:
    std::stack<T>   m_data;     // 数据栈，存放栈的所有元素
    std::stack<T>   m_min;      // 辅助栈，存放栈的最小元素
};

template <typename T> void StackWithMin<T>::push(const T& value)
{
    // 把新元素添加到辅助栈
    m_data.push(value);

    // 当新元素比之前的最小元素小时，把新元素插入辅助栈里；
    // 否则把之前的最小元素重复插入辅助栈里
    if(m_min.size() == 0 || value < m_min.top())
        m_min.push(value);
    else
        m_min.push(m_min.top());
}

template <typename T> void StackWithMin<T>::pop()
{
    assert(m_data.size() > 0 && m_min.size() > 0);

    m_data.pop();
    m_min.pop();
}


template <typename T> const T& StackWithMin<T>::min() const
{
    assert(m_data.size() > 0 && m_min.size() > 0);

    return m_min.top();
}

template <typename T> T& StackWithMin<T>::top()
{
    return m_data.top();
}

template <typename T> const T& StackWithMin<T>::top() const
{
    return m_data.top();
}

template <typename T> bool StackWithMin<T>::empty() const
{
    return m_data.empty();
}

template <typename T> size_t StackWithMin<T>::size() const
{
    return m_data.size();
}
```
