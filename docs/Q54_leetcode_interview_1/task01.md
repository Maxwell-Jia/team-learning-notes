# Task01: 第 001~012 题

## 1. [0054 螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)

**思路**

1. 设定上下左右四个边界。
2. 依次记录 up 行，up 下移，如果 up 和 down 交错，退出循环。
3. 依次记录 right 列，right 左移，如果 left 和 right 交错，退出循环。
4. 依次记录 down 行，down 上移，如果 down 和 up 交错，退出循环。
5. 依次记录 left 列，left 右移，如果 left 和 right 交错，退出循环。


**代码**

```cpp
class Solution {
public:
    vector<int> spiralOrder(vector<vector<int>>& matrix) {
        vector<int> ans;
        int up = 0, down = matrix.size() - 1, left = 0, right = matrix[0].size() - 1;
        while (true) {
            for (int j = left; j <= right; ++j) ans.push_back(matrix[up][j]);
            if (++up > down) break;
            for (int i = up; i <= down; ++i) ans.push_back(matrix[i][right]);
            if (--right < left) break;
            for (int j = right; j >= left; --j) ans.push_back(matrix[down][j]);
            if (--down < up) break;
            for (int i = down; i >= up; --i) ans.push_back(matrix[i][left]);
            if (++left > right) break;
        }
        return ans;
    }
};
```


## 2. [0048 旋转图像](https://leetcode.cn/problems/rotate-image/)

**思路**

1. 矩阵旋转 90 度，相当于矩阵转置再每行进行逆序。
2. 矩阵转置相当于按列压缩，可以通过 zip 函数实现。

使用 Python 一行代码即可。

**代码**

```python
class Solution:
    def rotate(self, matrix: List[List[int]]) -> None:
        """
        Do not return anything, modify matrix in-place instead.
        """
        matrix[:] = [row[::-1] for row in zip(*matrix)]
```


## 3. [0215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

**思路**

维护一个大顶堆并遍历数组，最后从堆中 pop k 次即可。
C++ 中堆可以借助 priority_queue 实现。

注意：并没有达到 O(N) 的复杂度，因为入堆的时候排序需要 O(logN)。所以总的时间复杂度应该是 O(NlogN)。

**代码**

```cpp
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        priority_queue<int> pq;
        for (int num : nums) {
            pq.push(num);
        }
        while (--k) {
            pq.pop();
        }
        return pq.top();
    }
};
```