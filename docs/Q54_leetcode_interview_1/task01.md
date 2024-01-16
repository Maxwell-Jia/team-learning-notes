# Task01: 第 001~012 题

## 1. [Leetcode 54 螺旋矩阵](https://leetcode.cn/problems/spiral-matrix/)

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


## 2. [Leetcode 48 旋转图像](https://leetcode.cn/problems/rotate-image/)

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


## 3. [Leetcode 215 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

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


## 4. [Leetcode 912 排序数组](https://leetcode.cn/problems/sort-an-array/description/)

**思路**

使用快排对数组进行排序，普通快排的步骤：

1. 选择一个 pivot （通常是数组最后一个值）
2. 挨个比较，小于 pivot 的放前面，大于的放后面
3. pivot 放在对应的位置，然后对前后的字数组递归排序

上面的办法无法通过测试，发现测试点中有大规模的本来就有序的数组，这样选择最后一个数作为 pivot 就会带来巨大开销。改进思路：随机选择一个数作为 pivot。

改进之后的方法仍然不能通过测试，发现原因是测试点中有大量重复数字的数组。改进思路：三路快排，在原来仅仅分成大于小于两组的基础上，增加一个等于分组，这样与 pivot 相等的所有的数都不会再参与后面的递归排序，大大降低开销。

**代码-一般快排**
```cpp
class Solution {
public:
    int partition(vector<int>& nums, int low, int high) {
        int pivot = nums[high];
        int i = low - 1;
        for (int j = low; j < high; ++j) {
            if (nums[j] <= pivot) {
                i++;
                swap(nums[j], nums[i]);
            }
        }
        swap(nums[i+1], nums[high]);
        return i + 1;
    }

    void quickSort(vector<int>& nums, int low, int high) {
        if (low < high) {
            int pi = partition(nums, low, high);
            quickSort(nums, low, pi - 1);
            quickSort(nums, pi + 1, high);
        }
    }

    vector<int> sortArray(vector<int>& nums) {
        quickSort(nums, 0, nums.size() - 1);
        return nums;
    }
};
```

**代码-随机 pivot**
```python
class Solution:
    def partition(self, nums: List[int], low: int, high: int):
        pivot_index = random.randint(low, high)  # Random pivot selection
        nums[pivot_index], nums[high] = nums[high], nums[pivot_index]
        
        pivot = nums[high]
        i = low - 1

        for j in range(low, high):
            if nums[j] <= pivot:
                i += 1
                nums[i], nums[j] = nums[j], nums[i]
        
        nums[i+1], nums[high] = nums[high], nums[i+1]

        return i + 1

    def quickSort(self, nums: List[int], low: int, high: int):
        if low < high:
            pi = self.partition(nums, low, high)
            self.quickSort(nums, low, pi - 1)
            self.quickSort(nums, pi + 1, high)
        
        return nums

    def sortArray(self, nums: List[int]) -> List[int]:
        return self.quickSort(nums, 0, len(nums) - 1)
```

**代码-三路快排**

```python
class Solution:
    def sortArray(self, nums: List[int]) -> List[int]:
        def quickSort(arr: List[int]) -> List[int]:
            if len(arr) == 0:
                return []
            pivot = random.choice(arr)
            small, eq, big = [], [], []
            for x in arr:
                if x < pivot:
                    small.append(x)
                elif x == pivot:
                    eq.append(x)
                else:
                    big.append(x)
            return quickSort(small) + eq + quickSort(big)

        return quickSort(nums)
```


## 5. [Leetcode 88 合并两个有序数组](https://leetcode.cn/problems/merge-sorted-array/description/)

**思路**

正向直接对比进行合并的话，需要涉及到 nums1 后面所有的元素后移，复杂度较高。

由于 nums1 后半部分是空的，因此可以两个数组从后向前合并。

**代码**

```python
class Solution:
    def merge(self, nums1: List[int], m: int, nums2: List[int], n: int) -> None:
        """
        Do not return anything, modify nums1 in-place instead.
        """
        p1, p2 = m-1, n-1
        idx = m+n-1
        while p1 >= 0 and p2 >= 0:
            if nums1[p1] < nums2[p2]:
                print(idx, p2)
                nums1[idx] = nums2[p2]
                idx -= 1
                p2 -= 1
            else:
                nums1[idx] = nums1[p1]
                idx -= 1
                p1 -= 1

        while p2 >= 0:
            nums1[idx] = nums2[p2]
            idx -= 1
            p2 -= 1   
```

## 6. [Leetcode 169 多数元素](https://leetcode.cn/problems/majority-element/description/)

**思路**

使用哈希表/字典来记录每个元素出现的次数，最终返回值最大的键。

注意：没实现 O(1) 的空间复杂度，当前空间复杂度为 O(N)。

**代码**

```python
class Solution:
    def majorityElement(self, nums: List[int]) -> int:
        cnt = {}
        for num in nums:
            cnt[num] = cnt.get(num, 0) + 1
        return max(cnt, key=lambda k: cnt[k])
```
