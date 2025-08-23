## 1_Q24_322. 零钱兑换

```cpp
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
	    // dp 数组及其初始化
	    vector<int> dp(amount + 1, INT_MAX);
	    dp[0] = 0;
	    // 遍历顺序及递推公式
	    for (int i = 0; i < coins.size(); i++){ // 此处以先遍历物品为例，也可以先遍历背包
		    for (int j = coins[i]; j <= amount; j++){
			    if (dp[j - coins[i]] != INT_MAX)
				    dp[j] = min(dp[j - coins[i]] + 1, dp[j]);
		    }
	    }
	    if (dp[amount] == INT_MAX) return -1;
	    else return dp[amount];
	}
};
```
- **注意，dp 数组的大小 得是 amount + 1**
---
## 2_Q23_128. 最长连续序列

```cpp
class Solution {
public:
    int longestConsecutive(vector<int>& nums) {
        unordered_set<int> numSet;
        // 数组元素入集合，自动去重
        for (int num : nums){
            numSet.insert(num);
        }
        // 遍历集合，找可能为起点的元素，算每一次的连续子序列长度，更新最大值
        int longest = 0;
        for (int num : numSet){
            if (numSet.find(num - 1) == numSet.end()){ // nums - 1不在集合里，那 num 可以是起点
                int curNum = num; // 当前元素
                int curLen = 1; // 当前长度，已经有起点了，所以是 1

                while (numSet.find(curNum + 1) != numSet.end()){
                    curNum++;
                    curLen++;
                }
                longest = max(curLen, longest);
            }
        }
        return longest;
    }
};
```
- **要用一个临时变量 `curNum` 去记录当前元素 num**

---
## 3_Q22_647. 回文子串
### 动规法
```cpp
class Solution {
public:
    int countSubstrings(string s) {
        int count = 0;
        int n = s.size();
        // dp 数组及其初始化
        vector<vector<bool>> dp(n+1, vector<bool>(n+1, false));
        // 遍历顺序（从左到右，从下到上）及递推公式
        for (int i = n - 1; i >= 0; i--){
            for (int j = i; j < n; j++){
                if (s[i] == s[j]){
                    // 情况1
                    if (i == j){
                        dp[i][j] = true;
                        count++;
                    } else if (j - i == 1){ //情况2
                        dp[i][j] = true;
                        count++;
                    } else if (dp[i+1][j-1] == true){
                        dp[i][j] = true;
                        count++;
                    }
                }
            }
        }
        return count;
    }
};
```
- **注意三种情况都是怎么写的哟**

### 双指针法
```cpp
class Solution {
public:
    int countSubstrings(string s) {
        int result = 0;
        for (int i = 0; i < s.size(); i++){
            result += extend(s, i, i, s.size()); // 中心点为 1 个
            result += extend(s, i, i + 1, s.size()); // 中心点为两个
        }
        return result;
    }
  
    int extend(const string & s, int i, int j, int n){
        int res = 0;
        while (i >= 0 && j < n && s[i] == s[j]){
            i--;
            j++;
            res++;
        }
        return res;
    }
};
```

---
## 4_Q21_136. 只出现一次的数字

```cpp
class Solution {
public:
    int singleNumber(vector<int>& nums) {
        int result = 0; // 初始化为0， 因为 0 和任何数 异或 都是 0
        for (int num : nums){
            result ^= num;
        }
        return result;
    }
};
```
- **这题就考一个 异或**

---
## 5_Q20_141. 环形链表

```cpp
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast != NULL && fast->next != NULL){
            fast = fast->next->next;
            slow = slow->next;
            if (slow == fast)
                return true;
        }
        return false;
    }
};
```

---
## 6_Q19_142. 环形链表2

```cpp
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast != NULL && fast->next != NULL){
            fast = fast->next->next;
            slow = slow->next;
            if (slow == fast){
                slow = head;
                while (slow != fast){
                    fast = fast->next;
                    slow = slow->next;
                }
                return slow;
            }
        }
        return NULL;
    }
};
```