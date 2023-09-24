#### 正则表达式和各种String

##### 8. String to Integer (atoi)

1. 用trim()函数删除左右的空格，如果剩余字符串为空则返回0；
2. 处理首位的正负号；
3. 处理数字，一旦遇到非数字字符则退出循环，处理溢出。

##### 65. Valid Number

这题目纠结各种情况就是自讨苦吃，本质仅仅是一道正则表达式练习题。还有人用自动机做，我只想说真的是吃饱了撑的……

```py
def isNumber(self, s: str) -> bool:
    import re
    regex = re.compile(r'^[+-]?(\d+\.?\d*|\.\d+)([eE][+-]?\d+)?$')
    return bool(regex.match(s.strip()))
```

##### 179. Largest Number

这题的逻辑很浅显，然而限于我python力太弱，python3取消cmp之后的语法我也不会写……

这题的逻辑就是按照两个字串连接之后的数字大小来排序，不难

```py
def largestNumber(self, num):
    num = [str(x) for x in num]
    num.sort(cmp=lambda x, y: cmp(y+x, x+y))
    return ''.join(num).lstrip('0') or '0'
```

#### Heap

##### 347. Top K Frequent Elements

这个题不难，hash map+heap queue，需要注意的就是heapq默认是最小堆，改成最大堆只需要把key变成负的就可以呀

```py
def topKFrequent(self, nums, k):
    freq, h, ret = {}, [], []
    for i in nums:
        if i in freq: freq[i] += 1
        else: freq[i] = 1
    for i in freq:
        heapq.heappush(h, (-freq[i], i))
    for i in range(0, k):
        temp = heapq.heappop(h)
        ret.append(temp[1])
    return ret
```

##### Given unsorted array of int, each element is the length of a rod, return the minimum total cost of combining all rods

难度2/5，这题目最简单的办法很显然是最小堆，不证明了，每次从最小堆里提出来两根棍子接一起，然后把接起来的新棍子丢最小堆里，每次操作的复杂度是O(logn)，所以总时间复杂度就是O(nlogn)，空间复杂度很显然是O(n)

```java
int minCost(int len[], int n) {
    int cost = 0;
    struct MinHeap* minHeap = createAndBuildMinHeap(len, n);
    while (!isSizeOne(minHeap)) {
        int min     = extractMin(minHeap);
        int sec_min = extractMin(minHeap);
        cost += (min + sec_min);
        insertMinHeap(minHeap, min+sec_min);
    }
    return cost;
}
```

#### 取余操作

##### Fast power

```py
(a * b) % p = ((a % p) * (b % p)) % p
```

#### Boyer-Moore 投票算法

##### 169 & 229. Majority Element I & II

这题目里Majority元素必然出现超过半数次数，那么我们可以用一个逆向逻辑去考虑：

1. 一个非majority元素的话，必然有多于他出现次数的其他元素
2. 那么如果所有其他元素的出现次数都少于他，那么他就是majority元素
3. 要点是，数组中从candidate被赋值到count减到0的那一段可以被去除，余下部分的多数元素依然是原数组的多数元素。

229的逻辑和169类似，就不赘述了

```java
public int majorityElement(int[] num) {
    int major=num[0], count = 1;
    for(int i=1; i<num.length;i++) {
        if (count==0) {
            count++;
            major=num[i];
        } else if(major==num[i]) {
            count++;
        } else count--;
    }
    return major;
}
```

```py
def majorityElement(self, nums):
    if not nums:
        return []
    count1, count2, candidate1, candidate2 = 0, 0, 0, 1
    for n in nums:
        if n == candidate1:
            count1 += 1
        elif n == candidate2:
            count2 += 1
        elif count1 == 0:
            candidate1, count1 = n, 1
        elif count2 == 0:
            candidate2, count2 = n, 1
        else:
            count1, count2 = count1 - 1, count2 - 1
    return [n for n in (candidate1, candidate2)
                    if nums.count(n) > len(nums) // 3]
```

#### Interval问题

##### 56 & 57. Merge / Insert Intervals

这俩题目大同小异，无非是先排序，再判断边界条件而已

```py
def merge(self, intervals):
    out = []
    for i in sorted(intervals, key=lambda i: i.start):
        if out and i.start <= out[-1].end:
            out[-1].end = max(out[-1].end, i.end)
        else:
            out += i,
    return out
```

```py
def insert(self, intervals, newInterval):
    out = []
    intervals.append(newInterval)
    for i in sorted(intervals, key=lambda i: i.start):
        if out and i.start <= out[-1].end:
            out[-1].end = max(out[-1].end, i.end)
        else:
            out += i,
    return out
```

#### 奇怪的排序

##### 220. Contains Duplicate III

坐标差不能大于k，值差不能大于t

如果t=0的话其实和LC219是同一个题目，加入t的话就是利用桶排序，每个桶大小为t+1，每经历过一轮k，就删除bucket里i-k所对应的值，这样就可以避免坐标差超过k的情况出现，而值差则是利用桶排序的性质，每一轮中在m桶里的数字很显然值差小于t，两侧桶里的数字经过恰当的测试值差也会小于t

```py
def containsNearbyAlmostDuplicate(self, nums, k, t):
    bucket = {}
    if t < 0: return False
    w, n = t + 1, len(nums)
    for i in range(0, n):
        m = nums[i] // w
        if m in bucket:
            return True
        if m - 1 in bucket and abs(nums[i] - bucket[m - 1]) < w:
            return True
        if m + 1 in bucket and abs(nums[i] - bucket[m + 1]) < w:
            return True
        bucket[m] = nums[i]
        if i >= k:
            del bucket[nums[i-k] // w]
    return False
```

#### 无法归类的各种怪题

##### 189. Rotate Array

这个题不难，ABCD要旋转成CDAB的话，可以先翻转ABCD到DCBA，然后分别翻转两半为CDAB即可

```python
def rotate(self, nums: List[int], k: int) -> None:
    n = len(nums)
    k %= n
    nums[:n-k]=nums[:n-k][::-1]
    nums[n-k:]=nums[n-k:][::-1]
    nums[:]=nums[::-1]
```

##### 172. Factorial Trailing Zeroes

这个题不难，首先要构成一个0的尾数就必须包含5，其次呢如果这个n的系数里包含不止一个5，需要处理完现在这个再去除以5，处理下一个

```py
def trailingZeroes(self, n: int) -> int:
    ret = 0
    while n > 0:
        ret += n // 5
        n //= 5
    return ret
```

##### 208. Implement Trie (Prefix Tree)

Trie最好是不要用递归写法，太长，容易错，迭代其实不难解决

```py
class TrieNode:
    def __init__(self):
        self.word=False
        self.children={}

class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word):
        node=self.root
        for i in word:
            if i not in node.children:
                node.children[i]=TrieNode()
            node=node.children[i]
        node.word=True

    def search(self, word):
        node=self.root
        for i in word:
            if i not in node.children:
                return False
            node=node.children[i]
        return node.word

    def startsWith(self, prefix):
        node=self.root
        for i in prefix:
            if i not in node.children:
                return False
            node=node.children[i]
        return True
```

##### 204. Count Primes

埃拉托斯特尼筛法，不难，但这个例程可以学到不少python奇形怪状的语法

```py
def countPrimes(self, n):
    if n < 3: return 0
    primes = [True] * n
    primes[0] = primes[1] = False
    for i in range(2, int(n ** 0.5) + 1):
        if primes[i]:
            primes[i*i:n:i] = [False] * len(primes[i*i:n:i])
    return sum(primes)
```

##### 233. Number of Digit One

这题目超级蠢，主要考点在思考每一位上1出现次数的逻辑

```java
int countDigitOne(int n) {
    int countr = 0;
    for (long long i = 1; i <= n; i *= 10) {
        long long divider = i * 10;
        countr += (n / divider) * i + min(max(n % divider - i + 1, 0LL), i);
    }
    return countr;
}
```

##### 621. Task Scheduler

There is a non-negative cooling interval n that means between two same tasks, there must be at least n intervals that CPU are doing different tasks or just be idle.

这题的逻辑是这样的

1. 模块的次数为任务最大次数减1，模块的长度为n+1，
2. 最后加上的字母个数为出现次数最多的任务，可能有多个并列

```py
def leastInterval(self, tasks, N):
    task_counts = collections.Counter(tasks).values()
    M = max(task_counts)
    Mct = task_counts.count(M)
    return max(len(tasks), (M - 1) * (N + 1) + Mct)
```

#### Union-find

Quick-find: 确定两个点是否处在同一个连通集里：遍历所有的边(p, q)，每次都把id[p]改成id[q]

```py
class UF:
    id = []
    count = 0

    def __init__(self, N):
        self.count = N
        for i in range(0, N):
            self.id.append(i)

    def dump(self):
        temp = ""
        for i in range(0, self.count):
            temp += (" " + str(i))
        print(temp)
        temp = ""
        for i in self.id:
            temp += (" " + str(i))
        print(temp)
        print()

    def find(self, p):
        return self.id[p]

    def quick_find(self, arr):
        for i in arr:
            pID = self.find(i[0])
            qID = self.find(i[1])
            if pID == qID:
                continue
            else:
                print(pID, qID)
                for j in range(0, self.count):
                    if self.id[j] == pID:
                        self.id[j] = qID
                self.dump()
```
#### 矩阵翻转操作

##### 48. Rotate Image

You are given an n x n 2D matrix representing an image. Rotate the image by 90 degrees (clockwise).

顺时针旋转90度 = 逆时针转90度再水平翻转；逆时针转90度就是求转置矩阵

```java
public void rotate(int[][] matrix) {
    // transpose
    for (int i = 0; i<matrix.length; i++)
        for (int j = i; j<matrix[i].length; j++){
            int temp = matrix[i][j];
            matrix[i][j] = matrix[j][i];
            matrix[j][i] = temp;
        }
    // flip
    for (int i = 0; i<matrix.length; i++){
        for (int j = 0; j<matrix[i].length/2; j++){
            int temp = matrix[i][j];
            matrix[i][j] = matrix[i][matrix.length - 1 - j];
            matrix[i][matrix.length - 1 - j] = temp;
        }
    }
}
```

##### 54. Spiral Matrix

Given a matrix of m x n elements (m rows, n columns), return all elements of the matrix in spiral order.

这个题不要想太深，维持四个变量记录接下来的最大行数和列数，然后按顺序每计数完一行（列）就更新这四个变量即可。

```java
public List<Integer> spiralOrder(int[][] matrix) {
    List<Integer> res = new ArrayList<Integer>();
    if (matrix.length == 0)
        return res;
    int rowBegin = 0;
    int rowEnd = matrix.length-1;
    int colBegin = 0;
    int colEnd = matrix[0].length - 1;
    while (rowBegin <= rowEnd && colBegin <= colEnd) {
        for (int j = colBegin; j <= colEnd; j ++)
            res.add(matrix[rowBegin][j]);
        rowBegin++;
        for (int j = rowBegin; j <= rowEnd; j ++)
            res.add(matrix[j][colEnd]);
        colEnd--;
        if (rowBegin <= rowEnd)
            for (int j = colEnd; j >= colBegin; j --)
                res.add(matrix[rowEnd][j]);
        rowEnd--;
        if (colBegin <= colEnd)
            for (int j = rowEnd; j >= rowBegin; j --)
                res.add(matrix[j][colBegin]);
        colBegin++;
    }
    return res;
}
```

##### 498. Diagonal Traverse

这个题目需要考虑清楚边界情况，靠边的时候一共是四种情况，不靠边反而只有两种

```py
def findDiagonalOrder(self, matrix):
    """
    :type matrix: List[List[int]]
    :rtype: List[int]
    """
    direction, x, y = True, 0, 0
    #True means upper-right, vice versa
    if len(matrix) == 0:
        return []
    h, w = len(matrix), len(matrix[0])
    ret = []
    while x < w and y < h:
        ret.append(matrix[y][x])
        if y == 0 and direction == True and x != w - 1:
            x += 1
            direction = False
        elif x == 0 and direction == False and y != h - 1:
            y += 1
            direction = True
        elif x == w - 1 and direction == True:
            y += 1
            direction = False
        elif y == h - 1 and direction == False:
            x += 1
            direction = True
        else:
            if direction:
                x += 1
                y -= 1
            else:
                x -= 1
                y += 1
    return ret
```
