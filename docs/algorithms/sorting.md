#### 选择排序

选择排序最好理解，每次迭代都从子数组里寻找最小值即可，第k次迭代抽出来的即是第k小值

```py
def swap(self, arr, i, j):
    temp = arr[j]
    arr[j] = arr[i]
    arr[i] = temp


def selectSort(self, arr):
    l = len(arr)
    for i in range(0, l):
        min = 2 ** 31
        m = 0
        for j in range(i, l):
            if arr[j] < min:
                min = arr[j]
                m = j
        self.swap(arr, i, m)
    return arr
```

#### 插入排序

插入排序的话需要假设前面第i-1个数字是排好序的，然后把第i个数字插到已经排好序的数组里的恰当位置；既然是插入那么其实用链表比用数组要经济

```py
def insertSort(self, arr):
    l = len(arr)
    for i in range(1, l):
        temp = arr[i]
        j = i - 1
        while j >= 0 and arr[j] > temp:
            arr[j + 1] = arr[j]
            j -= 1
        arr[j + 1] = temp
    return arr
```

#### 合并排序

合并排序很容易理解，要点是每排完一个数组就把另一个数组剩余值贴后面

```py
def merge(self, arr1, arr2):
    arr = []
    [l1, l2] = [len(arr1), len(arr2)]
    [i1, i2] = [0, 0]
    while i1 < l1 and i2 < l2:
        if arr1[i1] < arr2[i2]:
            arr.append(arr1[i1])
            i1 += 1
        else:
            arr.append(arr2[i2])
            i2 += 1
    if i1 == l1:
        arr += arr2[i2:]
    else:
        arr += arr1[i1:]
    return arr

def mergeSort(self, arr):
    l = len(arr)
    if l == 1:
        return arr
    elif l == 2:
        if arr[0] > arr[1]:
            self.swap(arr, 0, 1)
        return arr
    else:
        l1 = l // 2
        m1 = self.mergeSort(arr[0:l1])
        m2 = self.mergeSort(arr[l1:])
        return self.merge(m1, m2)
```

#### 快速排序

快速排序没什么好说的

```py
def quickSort(self, arr):
    l = len(arr)
    if l <= 1:
        return arr
    pivot = arr[int(random.random() * 200) % l]
    a = []
    b = []
    c = []
    for i in arr:
        if i < pivot:
            a.append(i)
        elif i == pivot:
            b.append(i)
        else:
            c.append(i)
    return self.quickSort(a) + b + self.quickSort(c)
```

### Quick-select

这个只要体会了思想，题目倒并不是很难，但如果不理解这个思想的话有的题目是完全无从措手的

相关leetcode题目：4. Median of Two Sorted Arrays，这道题因为是两个已经排好序的数列，可以略去一些选择的步骤

```py
def quickselect(self, arr, k):
    print(arr)
    print(k)
    if k == 0:
        return arr[0]
    else:
        l = len(arr)
        r = int(random.random() * 200) % l
        temp = arr[r]
        [a, b, c] = [[], [], []]
        for i in arr:
            if i < temp:
                a.append(i)
            elif i == temp:
                b.append(i)
            else:
                c.append(i)
        if len(a) < k <= len(a) + len(b):
            return temp
        elif k <= len(a):
            return self.quickselect(a, k)
        else:
            return self.quickselect(c, k - len(a) - len(b))
```

#### 4. Median of Two Sorted Arrays

There are two sorted arrays nums1 and nums2 of size m and n respectively. Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

本题就是采用的Quick-select的思想，把求两个有序数列中位数，转化成求两个有序数列中第k大的数。采取减治法：将两个数列各自分成两段，两数列前半段共有k个数字；如果两数列前半段的末尾数字相等，那即为所求；否则的话，尾数较小的一个前半段中是不可能出现这个第k大数字的，下次递归时略去即可。

```java
public double findMedianSortedArrays(int[] nums1, int[] nums2) {
    int l = nums1.length + nums2.length;
    if (l % 2 == 1) return findKth(nums1, nums2, (l / 2 + 1));
    else return (findKth(nums1, nums2, l / 2) + findKth(nums1, nums2, l / 2 + 1)) / 2.0;
}

public double findKth(int[] nums1, int[] nums2, int k){
    int l = k / 2, l1 = nums1.length, l2 = nums2.length;
    if (l1 > l2) return findKth(nums2, nums1, k);
    if (l1 == 0) return nums2[k‐1];
    if (k == 1) return Math.min(nums1[0], nums2[0]);
    int pa = Math.min(l, l1), pb = k ‐ pa;
    if (nums1[pa‐1] == nums2[pb‐1]) return nums1[pa‐1];
    else if (nums1[pa‐1] < nums2[pb‐1]){
        int[] temp = Arrays.copyOfRange(nums1, pa, l1);
        return findKth(temp, nums2, k ‐ pa);
    } else {
        int[] temp = Arrays.copyOfRange(nums2, pb, l2);
        return findKth(nums1, temp, k ‐ pb);
    }
}
```

#### 215. Kth Largest Element in an Array

注意这题跟第4题其实是不一样的，这题目数组是未排序的。实际上是Quick sort变形：

1. 每次随机选出一个中值
2. 把所有数字按照中值筛到左边或者右边
3. 使用分治，如果中值的位置大于K就只处理左边，否则只处理右边

```java
public int findKthLargest(int[] nums, int k) {
    if (nums == null || nums.length == 0) {
        return -1;
    }
    return quickSelect(nums, 0, nums.length - 1, k - 1);
}
private int quickSelect(int[] nums, int start, int end, int k) {
    if (start >= end) {
        return nums[start];
    }
    int left = start;
    int right = end;
    int pivot = nums[(start + end) / 2];
    while (left <= right) {
        while (left <= right && nums[left] > pivot) {
            left++;
        }
        while (left <= right && nums[right] < pivot) {
            right--;
        }
        if (left <= right) {
            int temp = nums[left];
            nums[left] = nums[right];
            nums[right] = temp;
            left++;
            right--;
        }
    }
    if (right >= k && right >= start) {
        return quickSelect(nums, start, right, k);
    } else if (left <= k && left <= end) {
        return quickSelect(nums, left, end, k);
    }
    return nums[k];
}
```