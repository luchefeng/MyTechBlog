## 1_Q12_198. 打家劫舍

```cpp
class Solution{
public:
	int rob(vector<int> & nums){
		// 边界考虑
		if (nums.size() == 0) return 0;
		if (nums.size() == 1) return nums[0];
		// dp数组含义：考虑下标i，能偷的最大钱币数为dp[i]
		vector<int> dp(nums.size(), 0);
		// dp[0],dp[1]的初始化
		dp[0] = nums[0];
		dp[1] = max(nums[0], nums[1]);
		// 遍历顺序（从左到右）及递推公式
		for (int i = 2; i < nums.size(); i++){
			dp[i] = max(nums[i] + dp[i-2], dp[i-1]);
		}
		return dp[nums.size() - 1];
	}
};
```

## 2_Q2_236. 二叉树的公共祖先

```cpp
class Solution{
public: 
	TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
		// 递归终止条件
		if (root == NULL || root == p || root == q) return root;
		// 后序遍历
		TreeNode* left = lowestCommonAncestor(root->left, p, q);
		TreeNode* right = lowestCommonAncestor(root->right, p, q);
		//处理“中”的逻辑
		if (left && right) return root;
		else if (left && !right) return left;
		else if (!left && right) return right;
		else return NULL;
    }
};
```

## 3_Q8_208. 实现Trie（前缀树）

```cpp
class Trie {
private:
	bool isEnd;
	Trie* children[26];
public:

    Trie() {
		isEnd = false;
		memset(children, 0, sizeof(children));
    }

    void insert(string word) {
		Trie* node = this;
		for (char c : word){
			int idx = c - 'a';
			if (node->children[idx] == NULL){
				node->children[idx] = new Trie();
			}
			node = node->children[idx]; 
		}
		node->isEnd = true;
    }

    bool search(string word) {
		Trie* node = this;
		for (char c : word){
			int idx = c - 'a';
			if (node->children[idx] == NULL)
				return false;
			node = node->children[idx];
		}
		return node->isEnd;
    }

    bool startsWith(string prefix) {
		Trie* node = this;
		for (char c : prefix){
			int idx = c - 'a';
			if (node->children[idx] == NULL)
				return false;
			node = node->children[idx];
		}
		return true;
    }

};
```

## 4_Q7_215. 数组中的第K个最大元素

### 堆排序法
```cpp
class Solution {

public:

    int findKthLargest(vector<int>& nums, int k) {
        int heapSize = nums.size();
        buildMaxHeap(nums, heapSize);
        // k-1 delete
        for (int i = nums.size() - 1; i >= nums.size() - k + 1; i--){
            swap(nums[i], nums[0]);
            --heapSize;
            maxHeapify(nums, 0, heapSize);
        }
        return nums[0];
    }

    void buildMaxHeap(vector<int>& nums, int heapSize){
        for (int i = nums.size() - 1;i >= 0; i--){
            maxHeapify(nums, i, heapSize);
        }
    }

    void maxHeapify(vector<int>& nums, int i, int heapSize){
        int left = 2 * i +1;
        int right = 2 * i +2;
        int largest = i;
        if (left < heapSize && nums[left] > nums[largest]) largest = left;
        if (right < heapSize && nums[right] > nums[largest]) largest = right;
        if (largest != i){
            swap(nums[i], nums[largest]);
            maxHeapify(nums, largest, heapSize);
        }
    }
};
```

### 快速选择法
```cpp
class Solution {

public:

    int findKthLargest(vector<int>& nums, int k) {
        int n = nums.size();
        return quickselect(nums, 0, n- 1,n - k);
    }

    int quickselect(vector<int>& nums, int l, int r, int k){
        // 递归终止条件
        if (l == r) return nums[k];
        int pivot = nums[l];
        int i = l - 1;
        int j = r + 1;
        while (i < j){
            do i++; while (nums[i] < pivot);
            do j--; while (nums[j] > pivot);
            if (i < j){
                swap(nums[i], nums[j]);
            }
        }
        if (k <= j) return quickselect(nums, l, j, k);
        else return quickselect(nums, j + 1, r, k);
    }
};
```

## 5_Q6_221. 最大正方形

### 注意事项
- 第一，递推公式里那个1是无论如何都要加上的，小心括号别把它包进去了
- 遍历顺序的时候，`i`, `j`是从1开始，避免越界
- 别忘了边界条件
```cpp
class Solution {

public:

    int maximalSquare(vector<vector<char>>& matrix) {
        if (matrix.size() == 0 || matrix[0].size() == 0) return 0;
        // dp数组含义：以右下角为正方形的最大边长，这个正方形得全是1
        int row = matrix.size();
        int column = matrix[0].size();
        vector<vector<int>> dp(row, vector<int>(column, 0));
        int maxSize = 0;
        // 初始化
        for (int i = 0; i < row; i++){
            dp[i][0] = matrix[i][0] - '0';
            maxSize = max(maxSize, dp[i][0]);
        }
        for (int j = 0; j < column; j++){
            dp[0][j] = matrix[0][j] - '0';
            maxSize = max(maxSize, dp[0][j]);
        }
        // 遍历顺序及递推公式
        for (int i = 1; i < row; i++){
            for (int j = 1; j < column; j++){
                if (matrix[i][j] == '1'){
                    dp[i][j] = min(min(dp[i][j-1], dp[i-1][j]), dp[i-1][j-1])+ 1;
                    maxSize = max(maxSize, dp[i][j]);
                }
            }
        }
        return maxSize * maxSize;
    }
};
```

## 6_Q11_200. 岛屿数量

### 注意事项
- 入队立马标记，记得把人家的`true`补完好吧（掩面）。还有，是赋值`=`，不是等于`==`!
- 那个`grid`，注意一下人家是`char`还是`int`类型
```cpp
class Solution {

public:

    int numIslands(vector<vector<char>>& grid) {
        int n = grid.size();
        int m = grid[0].size();
        vector<vector<bool>> visited(n, vector<bool>(m, false));
        int result = 0;
        for (int i = 0; i < n; i++){
            for (int j = 0; j < m; j++){
                if (!visited[i][j] && grid[i][j] == '1'){
                    result++;
                    visited[i][j] = true;
                    bfs(grid, visited, i, j);
                }
            }
        }
        return result;
    }
    
    int dir[4][2] = {1, 0, 0, 1, -1, 0, 0, -1};
    void bfs(vector<vector<char>>& grid, vector<vector<bool>>& visited, int x, int y){
        queue<pair<int, int>> que;
        que.push({x, y});
        visited[x][y] = true;
        while(!que.empty()){
            pair<int, int> cur = que.front(); que.pop();
            int curx = cur.first;
            int cury = cur.second;
            for (int i = 0; i < 4; i++){
                int nextx = curx + dir[i][0];
                int nexty = cury + dir[i][1];
                if (nextx < 0 || nextx >= grid.size() || nexty < 0 || nexty >= grid[0].size())
                continue;
                if (!visited[nextx][nexty] && grid[nextx][nexty] == '1'){
                    que.push({nextx, nexty});
                    visited[nextx][nexty] = true;
                }
            }
        }
    }
};
```

## 7_Q3_234. 回文链表

```cpp
class Solution {

public:

    bool isPalindrome(ListNode* head) {
        // 边界条件
        if (head == NULL || head->next == NULL) return true;
        // 找中间节点
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast->next && fast->next->next){
            slow = slow->next;
            fast = fast->next->next;
        }
        // 从中间节点反转后半链表
        ListNode* secondHalf = reverse(slow->next);
        ListNode* firstHalf = head;
        // 比较前后两部分链表是否相同
        while (secondHalf){
            if (firstHalf->val != secondHalf->val)
                return false;
            secondHalf = secondHalf->next;
            firstHalf = firstHalf->next;
        }
        return true;
    }

    ListNode* reverse(ListNode* head){
        ListNode* prev = NULL;
        ListNode* cur = head;
        while (cur != NULL){
            ListNode* temp = cur->next;
            cur->next = prev;
            prev = cur;
            cur = temp;
        }
        return prev;
    }
};
```

## 8_Q10_206. 反转链表

### 迭代法（双指针法）
```cpp
class Solution {

public:

    ListNode* reverseList(ListNode* head) {
        ListNode* prev = NULL;
        ListNode* cur = head;
        while (cur != NULL){
            ListNode* temp = cur->next;
            cur->next = prev;
            prev = cur;
            cur = temp;
        }
        return prev;
    }
};
```

### 递归
```cpp
class Solution {

public:
    ListNode* reverseList(ListNode* head) {
        return reverse(head, NULL);
    }
    
    ListNode* reverse(ListNode* cur, ListNode* prev){
        // 递归终止条件
        if (cur == NULL) return prev;
        ListNode* temp = cur->next;
        cur->next = prev;
        return reverse(temp ,cur);
    }
};
```

## 9_Q9_207. 课程表

```cpp
class Solution {

public:

    bool canFinish(int numCourses, vector<vector<int>>& prerequisites) {
        vector<int> result;
        vector<int> inDegree(numCourses, 0);
        vector<vector<int>> adj(numCourses);
        // 构建文件依赖关系
        for (int i = 0; i < prerequisites.size(); i++){
            int from = prerequisites[i][1];
            int to = prerequisites[i][0];
            adj[from].push_back(to);
            inDegree[to]++;
        }
        // 记录初始情况下入度为0的节点
        queue<int> que;
        for (int i = 0; i < numCourses; i++){
            if (inDegree[i] == 0) que.push(i);
        }  
        // 找到入度为0的节点并放入结果集
        while (!que.empty()){
            int cur = que.front(); que.pop();
            result.push_back(cur);
            vector<int> files = adj[cur];
            for (int i = 0; i < files.size(); i++){
                inDegree[files[i]]--;
                if (inDegree[files[i]] == 0) que.push(files[i]);
            }
        }
        return result.size() == numCourses;
    }
};
```

## 10_Q5_226. 翻转二叉树

### 前序遍历
```cpp
class Solution {

public:

    TreeNode* invertTree(TreeNode* root) {
        // 递归终止条件
        if (root == NULL) return root;
        // 前序遍历中，“中”的处理：交换
        TreeNode* temp = root->left;
        root->left = root->right;
        root->right = temp;
        invertTree(root->left);
        invertTree(root->right);
        
        return root;
    }
};
```

### 后序遍历
```cpp
class Solution {

public:

    TreeNode* invertTree(TreeNode* root) {
        if (root == NULL) return root;
        invertTree(root->left);
        invertTree(root->right);
        TreeNode* temp = root->left;
        root->left = root->right;
        root->right = temp;

        return root;
    }
};
```

### 中序遍历
```cpp
class Solution {

public:

    TreeNode* invertTree(TreeNode* root) {
        if (root == NULL) return root;
        invertTree(root->left);
        TreeNode* temp = root->left;
        root->left = root->right;
        root->right = temp;
        invertTree(root->left);

        return root;
    }
};
```

### 层序遍历
```cpp
class Solution {

public:

    TreeNode* invertTree(TreeNode* root) {
        queue<TreeNode*> que;
        if (root != NULL){
            que.push(root);
        }
        while (!que.empty()){
            int size = que.size();
            for (int i = 0; i < size;i++){
                TreeNode* node = que.front(); que.pop();
                swap(node->left, node->right);
                if (node->left) que.push(node->left);
                if (node->right) que.push(node->right);
            }
        }
        return root;
    }
};
```

## 11_Q1_160. 相交链表

### 注意事项
- 先把两个指针`curA`和`curB`定义出来，别改了人家原本的数据结构！

```cpp
class Solution {

public:

    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        ListNode* curA = headA;
        ListNode* curB = headB;
        // A 表的长度
        int lenA = 0;
        while (curA != NULL){
            lenA++;
            curA = curA->next;
        }
        // B 表长度
        int lenB = 0;
        while (curB != NULL){
            lenB++;
            curB = curB->next;
        }
        // 重新指向头部
        curA = headA;
        curB = headB;
        if (lenA < lenB){
            swap(lenA, lenB);
            swap(curA, curB);
        }
        // 求长度差
        int gap = lenA - lenB;
        while (gap--){
            curA = curA->next;
        }
        while (curA != NULL){
            if (curA == curB) return curA;
            curA = curA->next;
            curB = curB->next;
        }
        return NULL;
    }
};
```

## 12_Q4_739. 每日温度

```cpp
class Solution {

public:

    vector<int> dailyTemperatures(vector<int>& temperatures) {
        vector<int> result(temperatures.size(), 0);
        stack<int> st;
        st.push(0);
        for (int i = 0; i < temperatures.size(); i++){
            // 三种情况
            if (temperatures[i] <= temperatures[st.top()])
                st.push(i);
            else{
                while (!st.empty() && temperatures[i] > temperatures[st.top()]){
                    result[st.top()] = i - st.top();
                    st.pop();
                }
                st.push(i);
            }
        }
        return result;
    }
};
```