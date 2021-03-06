> 运用你所掌握的数据结构，设计和实现一个  LRU (最近最少使用) 缓存机制。它应该支持以下操作： 获取数据 get 和 写入数据 put 。  
获取数据 get(key) - 如果密钥 (key) 存在于缓存中，则获取密钥的值（总是正数），否则返回 -1。  
写入数据 put(key, value) - 如果密钥不存在，则写入其数据值。当缓存容量达到上限时，它应该在写入新数据之前删除最近最少使用的数据值，从而为新的数据值留出空间。  
进阶:  
你是否可以在 O(1) 时间复杂度内完成这两种操作？  
示例:  
LRUCache cache = new LRUCache( 2 /* 缓存容量 */ );  
cache.put(1, 1);  
cache.put(2, 2);  
cache.get(1);       // 返回  1  
cache.put(3, 3);    // 该操作会使得密钥 2 作废  
cache.get(2);       // 返回 -1 (未找到)  
cache.put(4, 4);    // 该操作会使得密钥 1 作废  
cache.get(1);       // 返回 -1 (未找到)  
cache.get(3);       // 返回  3  
cache.get(4);       // 返回  4  
来源：力扣（LeetCode）  
链接：https://leetcode-cn.com/problems/lru-cache  
著作权归领扣网络所有。商业转载请联系官方授权，非商业转载请注明出处。  

【未做出】  
最难的肯定就是选定一个基础数据结构了  
1.首先必须存储一对pair，然后最重要的是必须得存储某个值或者保持某个顺序，使得容量达到上限时能够删除最少使用的数据  
2.然后看进阶，难点在于两个操作都要常数时间，常数时间的查找只能用哈希表，然后难点就在于常数时间的插入，这个似乎也只能用哈希表  
3.对于最少使用这个问题，我打算先使用两个哈希表来初步解决这个问题，一个属于历史使用表，一个属于插入表。优先牺牲插入表的元素，但是我没想好全部都在历史使用表后怎么判断最少使用的元素。  
艹了，理解错了，是删除掉最老的元素？  

看题解：  
1.其实我实在是很疑惑示例中为何4插入时为何删除1而不是删除3？  
2.我忘了判断key是否存在了，已存在的话需要删除再重新添加  
3.list是双向链表，forward_list才是单向链表  
4.理解erase很重要，不需要自己接驳前后元素，函数会自动删除元素后接驳  
5.erase等函数只接受迭代器，主要list等链表的迭代器不会失效，但是vector、deque的会失效，原因就在于是否是连续存储，所以这道题里很适合用list的迭代器  

```
class LRUCache {
public:
    int cap;
    list<pair<int,int>> cache;
    unordered_map<int,list<pair<int,int>>::iterator> cache_map;
    LRUCache(int capacity) {
        this->cap=capacity;
    }
    
    int get(int key) {
        if(cache_map.count(key)==0)
            return -1;
        auto temp=*cache_map[key];
        cache.erase(cache_map[key]);
        cache.push_front(temp);
        cache_map[key]=cache.begin();
        return temp.second;
    }
    
    void put(int key, int value) {
        //注意旧有数据也必须先判断是否存在，存在的话要提到链表链首
        if(cache_map.count(key)){   
            cache.erase(cache_map[key]);
            cache_map.erase(key);
        }
        //先判断容量
        if(getSize()>=cap){
            auto del=cache.back();
            cache_map.erase(del.first);
            cache.pop_back();
        }
        cache.push_front({key,value});
        cache_map[key]=cache.begin();
        return;      
    }
    
    int getSize(){
       return cache.size(); 
    }
};
```

来看看网友题解详解：  
> 三、LRU 算法设计  
分析上面的操作过程，要让 put 和 get 方法的时间复杂度为 O(1)，我们可以总结出 cache 这个数据结构必要的条件：查找快，插入快，删除快，有顺序之分。  
因为显然 cache 必须有顺序之分，以区分最近使用的和久未使用的数据；而且我们要在 cache 中查找键是否已存在；如果容量满了要删除最后一个数据；每次访问还要把数据插入到队头。
那么，什么数据结构同时符合上述条件呢？哈希表查找快，但是数据无固定顺序；链表有顺序之分，插入删除快，但是查找慢。所以结合一下，形成一种新的数据结构：哈希链表。
LRU 缓存算法的核心数据结构就是哈希链表，双向链表和哈希表的结合体。这个数据结构长这样：  
![](file://C:/Users/lab509/Documents/Gridea/post-images/1578878403164.png)  
思想很简单，就是借助哈希表赋予了链表快速查找的特性嘛：可以快速查找某个 key 是否存在缓存（链表）中，同时可以快速删除、添加节点。回想刚才的例子，这种数据结构是不是完美解决了 LRU 缓存的需求
也许读者会问，为什么要是双向链表，单链表行不行？另外，既然哈希表中已经存了 key，为什么链表中还要存键值对呢，只存值不就行了  
想的时候都是问题，只有做的时候才有答案。这样设计的原因，必须等我们亲自实现 LRU 算法之后才能理解，所以我们开始看代码吧  
四、代码实现  
很多编程语言都有内置的哈希链表或者类似 LRU 功能的库函数，但是为了帮大家理解算法的细节，我们用 Java 自己造轮子实现一遍 LRU 算法。  
首先，我们把双链表的节点类写出来，为了简化，key 和 val 都认为是 int 类型：  
```
Java
class Node {
    public int key, val;
    public Node next, prev;
    public Node(int k, int v) {
        this.key = k;
        this.val = v;
    }
}
```
> 然后依靠我们的 Node 类型构建一个双链表，实现几个需要的 API（这些操作的时间复杂度均为 O(1))：  
```
class DoubleList {  
    private Node head, tail; // 头尾虚节点
    private int size; // 链表元素数

    public DoubleList() {
        head = new Node(0, 0);
        tail = new Node(0, 0);
        head.next = tail;
        tail.prev = head;
        size = 0;
    }

    // 在链表头部添加节点 x
    public void addFirst(Node x) {
        x.next = head.next;
        x.prev = head;
        head.next.prev = x;
        head.next = x;
        size++;
    }

    // 删除链表中的 x 节点（x 一定存在）
    public void remove(Node x) {
        x.prev.next = x.next;
        x.next.prev = x.prev;
        size--;
    }
    
    // 删除链表中最后一个节点，并返回该节点
    public Node removeLast() {
        if (tail.prev == head)
            return null;
        Node last = tail.prev;
        remove(last);
        return last;
    }
    
    // 返回链表长度
    public int size() { return size; }
}
```
> 到这里就能回答刚才“为什么必须要用双向链表”的问题了，因为我们需要删除操作。删除一个节点不光要得到该节点本身的指针，也需要操作其前驱节点的指针，而双向链表才能支持直接查找前驱，保证操作的时间复杂度 O(1)。  
有了双向链表的实现，我们只需要在 LRU 算法中把它和哈希表结合起来即可。我们先把逻辑理清楚：  
```
// key 映射到 Node(key, val)
HashMap<Integer, Node> map;
// Node(k1, v1) <-> Node(k2, v2)...
DoubleList cache;

int get(int key) {
    if (key 不存在) {
        return -1;
    } else {        
        将数据 (key, val) 提到开头；
        return val;
    }
}

void put(int key, int val) {
    Node x = new Node(key, val);
    if (key 已存在) {
        把旧的数据删除；
        将新节点 x 插入到开头；
    } else {
        if (cache 已满) {
            删除链表的最后一个数据腾位置；
            删除 map 中映射到该数据的键；
        } 
        将新节点 x 插入到开头；
        map 中新建 key 对新节点 x 的映射；
    }
}
```
> 如果能够看懂上述逻辑，翻译成代码就很容易理解了：  
```
class LRUCache {
    // key -> Node(key, val)
    private HashMap<Integer, Node> map;
    // Node(k1, v1) <-> Node(k2, v2)...
    private DoubleList cache;
    // 最大容量
    private int cap;
    
    public LRUCache(int capacity) {
        this.cap = capacity;
        map = new HashMap<>();
        cache = new DoubleList();
    }
    
    public int get(int key) {
        if (!map.containsKey(key))
            return -1;
        int val = map.get(key).val;
        // 利用 put 方法把该数据提前
        put(key, val);
        return val;
    }
    
    public void put(int key, int val) {
        // 先把新节点 x 做出来
        Node x = new Node(key, val);
        
        if (map.containsKey(key)) {
            // 删除旧的节点，新的插到头部
            cache.remove(map.get(key));
            cache.addFirst(x);
            // 更新 map 中对应的数据
            map.put(key, x);
        } else {
            if (cap == cache.size()) {
                // 删除链表最后一个数据
                Node last = cache.removeLast();
                map.remove(last.key);
            }
            // 直接添加到头部
            cache.addFirst(x);
            map.put(key, x);
        }
    }
}
```
> 这里就能回答之前的问答题“为什么要在链表中同时存储 key 和 val，而不是只存储 val”，注意这段代码：    
```

if (cap == cache.size()) {
    // 删除链表最后一个数据
    Node last = cache.removeLast();
    map.remove(last.key);
}
```
> 当缓存容量已满，我们不仅仅要删除最后一个 Node 节点，还要把 map 中映射到该节点的 key 同时删除，而这个 key 只能由 Node 得到。如果 Node 结构中只存储 val，那么我们就无法得知 key 是什么，就无法删除 map 中的键，造成错误。    

> 另外，C++ 可以利用迭代器更方便实现 LRU 算法，有兴趣的读者可以看看代码：    
```
class LRUCache {
private:
    int cap;
    // 双链表：装着 (key, value) 元组
    list<pair<int, int>> cache;
    // 哈希表：key 映射到 (key, value) 在 cache 中的位置
    unordered_map<int, list<pair<int, int>>::iterator> map;
public:
    LRUCache(int capacity) {
        this->cap = capacity; 
    }
    
    int get(int key) {
        auto it = map.find(key);
        // 访问的 key 不存在
        if (it == map.end()) return -1;
        // key 存在，把 (k, v) 换到队头
        pair<int, int> kv = *map[key];
        cache.erase(map[key]);
        cache.push_front(kv);
        // 更新 (key, value) 在 cache 中的位置
        map[key] = cache.begin();
        return kv.second; // value
    }
    
    void put(int key, int value) {

        /* 要先判断 key 是否已经存在 */ 
        auto it = map.find(key);
        if (it == map.end()) {
            /* key 不存在，判断 cache 是否已满 */ 
            if (cache.size() == cap) {
                // cache 已满，删除尾部的键值对腾位置
                // cache 和 map 中的数据都要删除
                auto lastPair = cache.back();
                int lastKey = lastPair.first;
                map.erase(lastKey);
                cache.pop_back();
            }
            // cache 没满，可以直接添加
            cache.push_front(make_pair(key, value));
            map[key] = cache.begin();
        } else {
            /* key 存在，更改 value 并换到队头 */
            cache.erase(map[key]);
            cache.push_front(make_pair(key, value));
            map[key] = cache.begin();
        }
    }
};
```
> 至此，你应该已经掌握 LRU 算法的思想和实现了，很容易犯错的一点是：处理链表节点的同时不要忘了更新哈希表中对节点的映射。  
