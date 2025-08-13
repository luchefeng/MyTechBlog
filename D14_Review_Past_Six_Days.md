
## 1_Q16_152. 乘积最大子数组

```cpp
class Solution {

public:

    int maxProduct(vector<int>& nums) {
        // dp数组
        int n = nums.size();
        vector<int> dp_max(n, 1);
        vector<int> dp_min(n, 1);
        int max_product = nums[0];
        // dp数组初始化
        dp_max[0] = nums[0];
        dp_min[0] = nums[0];
        // 遍历顺序及递推公式
        for (int i = 1; i < n; i++){
            if (nums[i] > 0){ // nums[i]为正数
                dp_max[i] = max(dp_max[i-1] * nums[i], nums[i]);
                dp_min[i] = min(dp_min[i-1] * nums[i], nums[i]);
            }else{
                dp_max[i] = max(dp_min[i-1] * nums[i], nums[i]);
                dp_min[i] = min(dp_max[i-1] * nums[i], nums[i]);
            }
            max_product = max(max_product, dp_max[i]);
        }
        return max_product;
    }
};
```

- `dp_max`和`dp_min`都要一起更新
- 还有初始化要注意，初始化成`nums[0]`最稳妥

---
## 2_Q14_238. 除自身以外数组的乘积

```cpp
class Solution {

public:
    vector<int> productExceptSelf(vector<int>& nums) {
        int n = nums.size();
        vector<int> answer(n, 1);
        // 前缀积
        int prefix = 1;
        for (int i = 0; i < n; i++){
            answer[i] = prefix;
            prefix *= nums[i];
        }
        // 后缀积
        int suffix = 1;
        for (int i = n-1; i >= 0; i--){
            answer[i] *= suffix;
            suffix *= nums[i];
        }
        return answer;
    }
};
```

- 倒序是`i--`哈

---
## 3_Q18_146. LRU缓存

```cpp
struct DLinkNode {
    int key;
    int val;
    DLinkNode* prev;
    DLinkNode* next;
    DLinkNode() : key(0), val(0), prev(nullptr), next(nullptr) {}
    DLinkNode(int _key, int _val) : key(_key), val(_val),  prev(nullptr), next(nullptr) {}
};

class LRUCache {
private:
    unordered_map<int, DLinkNode*> cache;
    DLinkNode* head;
    DLinkNode* tail;
    int size;
    int capacity;
  
public:
    LRUCache(int _capacity): capacity(_capacity), size(0) {
        head = new DLinkNode();
        tail = new DLinkNode();
        head->next = tail;
        tail->prev = head;
    }
    int get(int key) {
        if (cache.find(key) == cache.end()){
            return -1;
        } else{
            DLinkNode* node = cache[key];
            moveToHead(node);
            return node->val;
        }
    }

    void put(int key, int value) {
        if (cache.find(key) == cache.end()){
            // 不在里面，插入新的
            DLinkNode* node = new DLinkNode(key, value);
            cache[key] = node;
            addToHead(node);
            ++size;
            if (size > capacity){
                DLinkNode* removed = removeTail();
                cache.erase(removed->key);
                delete removed;
                --size;
            }
        } else{
            DLinkNode* node = cache[key];
            node->val = value;
            moveToHead(node);
        }
    }

    void addToHead(DLinkNode* node){
        node->prev = head;
        node->next = head->next;
        node->next->prev = node;
        head->next = node;
    }

    void removeNode(DLinkNode* node){
        node->prev->next = node->next;
        node->next->prev = node->prev;
    }

    void moveToHead(DLinkNode* node){
        removeNode(node);
        addToHead(node);
    }

    DLinkNode* removeTail(){
        DLinkNode* node = tail->prev;
        removeNode(node);
        return node;
    }
};
```

- 如果你想检查 `key` 是否在 `cache` 里，可以直接用：
```cpp
if (cache.find(key) == cache.end()) { ... }
```
或者：
```cpp
if (!cache.count(key)) { ... }
```
但 **不能** 写成 `if (cache.count(key) == cache.end())`，因为 `count()` 返回的是 `0` 或 `1`，而 `end()` 返回的是迭代器。

---
## 4_Q13_169. 多数元素

```cpp
class Solution {

public:

    int majorityElement(vector<int>& nums) {
        // key:元素 value: 元素出现的次数
        unordered_map<int, int> map;
        int result = 0;
        int n = nums.size();
        for (int i = 0; i < n; i++){
            map[nums[i]]++;
            if (map[nums[i]] > n/2)
                result = nums[i];
        }
        return result;
    }
};
```

- 得有个变量来存题目要的结果，因为函数本身有返回类型`int`

## 5_Q15_155. 最小栈

```cpp
class MinStack {
private:
    std::stack<int> main_stack;
    std::stack<int> min_stack;
    
public:
    MinStack() {
        // 构造函数无需特别初始化
    }
    void push(int val) {
        main_stack.push(val);
        if (min_stack.empty() || min_stack.top() >= val){
            min_stack.push(val);
        }
    }
    
    void pop() {
        if (min_stack.top() == main_stack.top()){
            min_stack.pop();
        }
        main_stack.pop();
    }
    
    int top() {
        return main_stack.top();
    }
    
    int getMin() {
        return min_stack.top();
    }
};
```

- **我写的时候，在`push()`这段问题比较大**：
```cpp
void push(int val) {
        main_stack.push(val);
        if (min_stack.top() > val){
            min_stack.push(val);
        }
    }
```

	- 1. **初始状态**：`min_stack` 是空的，第一次 `push(val)` 时，`min_stack.top()` 会崩溃。
    
	- 2. **逻辑错误**：即使 `min_stack` 非空，如果 `val` **等于** 当前最小值，也应该 `push(val)`，否则 `pop()` 时可能会提前移除最小值。

---
## 6_

```cpp
/**

 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* sortList(ListNode* head) {
        // 递归终止条件
        if (head == NULL || head->next == NULL) return head;
        // 快慢指针找链表中部
        ListNode* slow = head;
        ListNode* fast = head;
        while (fast->next && fast->next->next){
            slow = slow->next;
            fast = fast->next->next;
        }
        
        ListNode* mid = slow->next;
        slow->next = nullptr;
        ListNode* right = sortList(mid);
        ListNode* left = sortList(head);
        
        return merge(left, right);

    }

    ListNode* merge(ListNode* L1, ListNode* L2){
        ListNode dummy(0);
        ListNode* tail = &dummy;
  
        while (L1 && L2){
            if (L1->val < L2->val){
                tail->next = L1;
                L1 = L1->next;
            } else{
                tail->next = L2;
                L2 = L2->next;
            }
            tail = tail->next;
        }

        if (L1){
            tail->next = L1;
        } else{
            tail->next = L2;
        }
        return dummy.next;
    }
};
```

- 又忘了写终止递归条件，嘿嘿