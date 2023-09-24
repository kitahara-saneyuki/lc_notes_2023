### 从句 （list comprehension）

#### for

```py
new_list = [expression(i) for i in old_list if filter(i)]
```

等价于

```py
new_list = []
for i in old_list:
    if filter(i):
        new_list.append(expressions(i))
```

同理

```
dic = {x: idx for idx, x in enumerate(nums)}
```

#### if 三元运算符

```py
condition_is_true if condition else condition_is_false
```

例子：

```py
is_fat = True
state = "fat" if is_fat else "not fat"
```

### 函数的各种奇技淫巧

#### 高阶函数（Higher-Order Functions）

高阶函数可以以函数为参数

```py
def cube(k):
    return pow(k, 3)

def summation(n, term):
    """Sum the first n terms of a sequence.
    >>> summation(5, cube)
    225
    """
    total, k = 0, 1
    while k <= n:
        total, k = total + term(k), k + 1
    return total
```

也可以返回一个函数

```py
def make_adder(n):
    """Return a function that takes one argument k and returns k + n.
    >>> add_three = make_adder(3)
    >>> add_three(4)
    7
    """
    def adder(k):
        return k + n
    return adder
```

Currying

```py
def make_adder(n):
    """
    >>> make_adder(2)(3)
    5
    """
    return lambda k: n + k
```

#### 匿名函数 lambda & sort cmp / key

```py
lambda [arg1 [, agr2,.....argn]] : expression
```

返回的是一个函数，举例：

```py
num.sort(cmp=lambda x, y: cmp(y+x, x+y))  # python 2 only
student_tuples = [
    ('john', 'A', 15),
    ('jane', 'B', 12),
    ('dave', 'B', 10),
]
sorted(student_tuples, key=lambda student: student[2])
```

#### Decorator

```py
# Python code to illustrate
# Decorators with parameters in Python  (Multi-level Decorators)
 
 
def decodecorator(dataType, message1, message2):
    def decorator(fun):
        print(message1)
        def wrapper(*args, **kwargs):
            print(message2)
            if all([type(arg) == dataType for arg in args]):
                return fun(*args, **kwargs)
            return "Invalid Input"
        return wrapper
    return decorator
 
 
@decodecorator(str, "Decorator for 'stringJoin'", "stringJoin started ...")
def stringJoin(*args):
    st = ''
    for i in args:
        st += i
    return st


print(stringJoin("I ", 'like ', "Geeks", 'for', "geeks"))
```

```
Decorator for 'stringJoin'

stringJoin started ...
I like Geeksforgeeks
```

### 字典和list的特殊用法

#### Slicing

```py
>>> 'hello world'[::-1]
'dlrow olleh'
```

Slice notation takes the form `[start:stop:step]`. In this case, we omit the `start` and `stop` positions since we want the whole string. We also use `step = -1`, which means, "repeatedly step from right to left by 1 character".

#### `*`和`**`

The *args will give you all function parameters as a tuple:

```py
def foo(*args):
    for a in args:
        print(a)
```

The **kwargs will give you all keyword arguments except for those corresponding to a formal parameter as a dictionary.

```py
def bar(**kwargs):
    for a in kwargs:
        print(a, kwargs[a])  
```

#### filter, map, reduce

注意python 3调整了filter和map的用法，从返回一个数组变为返回一个iterator

1. filter(fun, list)：对list里每一个entry执行fun，只保留返回True的值
2. map(fun, list)：对list里每一个entry执行fun

```py
print([i for i in filter(lambda x: x % 2 != 0 and x % 3 != 0, range(2, 25))])
[5, 7, 11, 13, 17, 19, 23]
print([i for i in map(lambda x: x ** 2, range(2, 10))])
[4, 9, 16, 25, 36, 49, 64, 81]
```

#### zip, enumerate

1. enumerate(list)：返回下标和内部值
2. zip(list1, list2)：返回list1和list2的值

```py
print([i for i in enumerate(range(1, 11))])
[(0, 1), (1, 2), (2, 3), (3, 4), (4, 5), (5, 6), (6, 7), (7, 8), (8, 9), (9, 10)]
print([i for i in zip(range(1, 11), [(i ** 2) for i in range(1, 11)])])
[(1, 1), (2, 4), (3, 9), (4, 16), (5, 25), (6, 36), (7, 49), (8, 64), (9, 81), (10, 100)]
```

#### items, keys, values for dict

```py
params = {
    "key1": "value1",
    "key2": "value2",
    "key3": "value3"
}
params.keys()
["key1","key2","key3"]
params.values()
["value1","value2","value3"]
params.items()
[("key1","value1"),("key2","value2"),("key3","value3")]
```

#### update for dict

```py
update([other])
```

Update the dictionary with the key/value pairs from other, overwriting existing keys. Return None.

### 内置数据结构

#### defaultdict

和dict有两个区别：

1. value允许一个默认函数，比如`defaultdict(set)`里所有的value初始值都是一个空的`set()`
2. 不需要检测key是否储存在defaultdict里

```py
next_level = collections.defaultdict(set)
next_level[n].add(node)
```

#### heapq

大名鼎鼎的priority queue在python里的实现

```py
import heapq
heapq.heapify(list) # in-place, linear time
heapq.heappush(heap, item)
heapq.heappop(heap)
```

如果要储存一个复杂对象的priority，使用tuples

```py
h = []
heapq.heappush(h, (5, 'write code'))
heapq.heappush(h, (7, 'release product'))
heapq.heappush(h, (1, 'write spec'))
heapq.heappush(h, (3, 'create tests'))
heapq.heappop(h)
(1, 'write spec')
```

heapq默认为最小堆，要改为最大堆就把tuple的priority改为负数

heapq默认提供从任意iterables里检索n个最大最小值的方法：

```py
nlargest(n, iterable, key=None)
nsmallest(n, iterable, key=None)
```

注意如果iterables里都是tuples，heapq默认把tuple第一个值视为priority

#### deque

我是没看出来他跟list有啥区别……

```py
d = deque(range(5))
print(len(d))
## 输出: 5
d.popleft()
## 输出: 0
d.pop()
## 输出: 4
print(d)
## 输出: deque([1, 2, 3])
d = deque([1,2,3,4,5])
d.extendleft([0])
d.extend([6,7,8])
print(d)
## 输出: deque([0, 1, 2, 3, 4, 5, 6, 7, 8])
```

#### Counter

这东西是极为好用啊，概率统计器

```py
c = Counter()                           # a new, empty counter
Counter()
c = Counter('gallahad')                 # a new counter from an iterable
Counter({'a': 3, 'l': 2, 'g': 1, 'h': 1, 'd': 1})
c = Counter({'red': 4, 'blue': 2})      # a new counter from a mapping
Counter({'red': 4, 'blue': 2})
c = Counter(cats=4, dogs=8)             # a new counter from keyword args
Counter({'dogs': 8, 'cats': 4})
```

### 生成器

生成器可以一边循环一边计算，避免了巨大的内存浪费。

最简单的把列表改成生成器的方式就是把方括号换成圆括号：

```py
>>> L = [x * x for x in range(10)]
>>> L
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
>>> g = (x * x for x in range(10))
>>> for n in g:
...     print(n)
```

```py
def fib(max):
    n, a, b = 0, 0, 1
    while n < max:
        yield b
        a, b = b, a + b
        n = n + 1
    return 'done'
```

这里，最难理解的就是generator函数和普通函数的执行流程不一样。普通函数是顺序执行，遇到return语句或者最后一行函数语句就返回。而变成generator的函数，在每次调用next()的时候执行，遇到yield语句返回，再次执行时从上次返回的yield语句处继续执行。

```py
>>> for n in fib(6):
...     print(n)
```

要理解generator的工作原理，它是在for循环的过程中不断计算出下一个元素，并在适当的条件结束for循环。对于函数改成的generator来说，遇到return语句或者执行到函数体最后一行语句，就是结束generator的指令，for循环随之结束。

### regex

按照大写字母分词

```py
re.findall(".[^A-Z]*")
```
