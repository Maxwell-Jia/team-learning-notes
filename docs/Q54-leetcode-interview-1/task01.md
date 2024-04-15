# Task01: 第 001~012 题

[项目地址](https://datawhalechina.github.io/leetcode-notes/#/ch06/index)

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


## 7. [Leetcode 136 只出现一次的数字](https://leetcode.cn/problems/single-number/description/)

**思路**

与多数元素一样，可以使用哈希表来存储每个元素出现的次数，遍历哈希表返回次数为1的键。但是这种空间复杂度为 O(N) 的方法不满足题目要求，需要使用空间复杂度为 O(1) 的方法。

可以使用位运算。本题可以使用异或运算，疑惑运算有以下三个性质：

1. 任何数和 0 做异或运算，结果仍然是原来的数，即 a ^ 0 = a
2. 任何数和其自身做异或运算，结果是 0，即 a ^ a = 0
3. 异或运算满足交换律和结合律，即 a ^ b ^ c = a ^ (b ^ c) = (a ^ b) ^ c

用于求解只出现一次的数字，可以先对整个数组进行异或运算，得到的结果就是那个只出现一次的数字。

**代码-哈希表**

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        cnt = {}
        for num in nums:
            cnt[num] = cnt.get(num, 0) + 1
        for k, v in cnt.items():
            if v == 1:
                return k
```

**代码-异或**

```python
class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        flag = 0
        for num in nums:
            flag ^= num
        return flag
```

## 8. [Leetcode 56 合并区间](https://leetcode.cn/problems/merge-intervals/description/)

**思路**

首先对数组进行排序，然后遍历排序后的数组，如果当前区间的起始位置大于前一个区间的结束位置，则说明两个区间不相交，将前一个区间加入结果数组，然后将当前区间加入结果数组。如果当前区间的起始位置小于等于前一个区间的结束位置，则说明两个区间相交，将前一个区间的结束位置更新为当前区间的结束位置，继续遍历下一个区间。最后，将最后一个区间加入结果数组。

**代码**

```python
class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals.sort(key=lambda x: x[0])
        res = []
        for interval in intervals:
            if not res or res[-1][1] < interval[0]:
                res.append(interval)
            else:
                res[-1][1] = max(interval[1], res[-1][1])
        return res
```


## 9. [Leetcode 179 最大数](https://leetcode.cn/problems/largest-number/description/)

**思路**

将输入的整数数组转换为字符串数组，然后对字符串数组进行排序。排序的规则是，如果两个字符串 s 和 t 拼接后得到 s+t 和 t+s，那么 s 应该排在 t 的前面。最后，将排序后的字符串数组拼接成一个字符串，并返回。

注意处理全 0 的情况。

```python
class Solution:
    def largestNumber(self, nums: List[int]) -> str:
        def compare(x: str, y: str):
            return int(y + x) - int(x + y) 
        
        nums = [str(num) for  num in nums]
        nums.sort(key=functools.cmp_to_key(compare))
        res = ''.join(nums)
        return '0' if res[0] == '0' else res
```


## 10. [Leetcode 704 二分查找](https://leetcode.cn/problems/binary-search/description/)

**思路**

经典的二分查找。注意右区间的开闭问题。

**代码**
```cpp
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] > target) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return -1;
    }
};
```


## 11. [Leetcode 34 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/description/)

**思路**

进行普通二分查找，找到目标值后，分别向左和向右查找第一个和最后一个位置。

**代码**

```cpp
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        int left = 0, right = nums.size() - 1;
        vector<int> ans;
        while (left <= right) {
            int mid = left + ((right - left) >> 1);
            if (nums[mid] == target) {
                // 向左查找
                int idx = mid;
                while (idx >= 0) {
                    if (nums[idx] != target) {
                        ans.push_back(idx + 1);
                        break;
                    }
                    idx--;
                }
                if (idx == -1) ans.push_back(0);
                // 向右查找
                idx = mid;
                while (idx < nums.size()) {
                    if (nums[idx] != target) {
                        ans.push_back(idx - 1);
                        break;
                    }
                    idx++;
                }
                if (idx == nums.size()) ans.push_back(nums.size() - 1);
                return ans;
            } else if (nums[mid] > target) {
                right = mid - 1;
            } else if (nums[mid] < target) {
                left = mid + 1;
            }
        }
        return vector<int>{-1, -1};
    }
};
```


## 12. [Leetcode 153 寻找旋转排序数组中的最小值](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/description/)

**思路**

二分查找，由于旋转数组的顺序性，有以下三种情况：
1. right > mid > left，最小值在左；
2. left > right > mid，最小值在左；
3. mid > left > right，最小值在右；
前两种统一成一个，这样的话 mid 可以直接和 right 比，小于 right 收缩右边界，大于 right 收缩左边界。
注意如果和 left 比的话是没办法比出来的。

**代码**

```cpp
class Solution {
public:
    int findMin(vector<int>& nums) {
        int left = 0;
        int right = nums.size() - 1;                
        while (left < right) {                      
            int mid = left + ((right - left) >> 1);   
            if (nums[mid] > nums[right]) {          
                left = mid + 1;                     
            } else if (nums[mid] < nums[right]) {   
                right = mid;                        
            }
        }
        return nums[left];  // left == right
    }
};
```