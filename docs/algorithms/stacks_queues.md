#### 堆栈与DFS和BFS

1. Stack一般用来模拟DFS，但不如回溯简洁
2. Queue一般用来模拟BFS

#### Infix Expression Evaluation

中缀到后缀转换：要点在于，为什么遇到计算符时要把优先级大于等于自己的运算符全部弹出？因为在后缀表达式里他们放在前面就等于先去计算他们啊；栈在这里实际上就是起到的一个保存计算顺序的作用，而“有控制地”从栈中弹出运算符可以达到把中缀式代换成语法树（也就是后缀式）的目的。

相关leetcode题目：150. Evaluate Reverse Polish Notation, 224. Basic Calculator

```py
def tokenize(self, str):
    newS = str.replace("(", " ( ")
    newS = newS.replace(")", " ) ")
    newS = newS.replace("+", " + ")
    newS = newS.replace("-", " - ")
    newS = newS.replace("*", " * ")
    newS = newS.replace("/", " / ")
    return newS.split(' ')


def optr(self, char):
    if char == '*' or char == '/':
        return 2
    elif char == '+' or char == '-':
        return 1
    else:
        return 0


def in2post(self, str):
    token = self.tokenize(str)
    stk = []
    ret = []
    for i in token:
        if i.isdecimal():
            ret.append(i)
        elif i == '(':
            stk.append(i)
        elif self.optr(i) > 0:
            if len(stk) == 0:
                stk.append(i)
            else:
                top = stk[len(stk) - 1]
                while self.optr(top) >= self.optr(i):
                    ret.append(top)
                    top = stk.pop()
                stk.append(i)
        elif i == ')':
            top = stk[len(stk) - 1]
            while top != '(':
                ret.append(top)
                top = stk.pop()
    while len(stk) > 0:
        ret.append(stk.pop())
    return ret
```

计算后缀式：这个不难，唯一需要注意的部分是两个操作数的弹出顺序。

```py
def cal(self, optr, opte1, opte2):
    opt1 = int(opte1)
    opt2 = int(opte2)
    if optr == '+':
        return opt1 + opt2
    elif optr == '-':
        return opt1 - opt2
    elif optr == '*':
        return opt1 * opt2
    elif optr == '/':
        return opt1 / opt2
    else:
        exit(1)


def eval(self, str):
    stk = []
    post = self.in2post(str)
    for i in post:
        if i.isdecimal():
            stk.append(i)
        else:
            opte2 = stk.pop()
            opte1 = stk.pop()
            stk.append(self.cal(i, opte1, opte2))
    return stk.pop()
```

##### 155. min stack

这个东西的难点就是如何记录【某一时刻的最小值】，那很简单，每次最小值变得更小，就往栈里连推两次，第一次推最小值，第二次推x；每次最小值变得更大的时候呢，从栈里连弹两次，第一次弹出来的是x，第二次弹出来的是最小值

```java
class MinStack {
    int min = Integer.MAX_VALUE;
    Stack<Integer> stack = new Stack<Integer>();
    public void push(int x) {
        if(x <= min){
            stack.push(min);
            min=x;
        }
        stack.push(x);
    }
    public void pop() {
        if(stack.pop() == min)
            min=stack.pop();
    }
    public int top() { return stack.peek();}
    public int getMin() { return min;}
}
```

#### Breadth-First Search

BFS思考起来比DFS要简单很多，而且对于很多问题是秒杀……尤其走迷宫BFS一般比较好写

#### 133. Clone graph

这个问题的题眼是，复制一个图，其中每个节点都只能创造一次，第二次调用它的时候你就得重复调用以前使用过的节点

其他的其实就是简单的BFS而已

```py
def cloneGraph(self, node):
    if not node:
        return
    nodeCopy = UndirectedGraphNode(node.label)
    dic = {node: nodeCopy}
    queue = collections.deque([node])
    while queue:
        node = queue.popleft()
        for neighbor in node.neighbors:
            if neighbor not in dic:
                # store copy
                neighborCopy = UndirectedGraphNode(neighbor.label)
                dic[neighbor] = neighborCopy
                dic[node].neighbors.append(neighborCopy)
                queue.append(neighbor)
            else:
                # we met this node before, no need to insert it to queue
                # but necessary to add that edge
                dic[node].neighbors.append(dic[neighbor])
    return nodeCopy
```

##### 207 \& 210. Course Schedule I \& II

这题目虽然是拓扑排序，BFS秒杀，然而仍然要注意，正向BFS是不如逆向BFS的，因为这题目的实质是寻找图里的环，含环图可以有个开端，但一定没有结尾

```py
def canFinish(self, numCourses, prerequisites):
    que, hashmap, deg, tot = [], {}, [0] * numCourses, 0
    # Prepare a list pre-req edges
    for i in prerequisites:
        if i[0] not in hashmap:
            hashmap[i[0]] = [i[1]]
        else:
            hashmap[i[0]].append(i[1])
        deg[i[1]] += 1
    # Topo-sort from the ends -- ends have deg of 0
    for i in range(0, numCourses):
        if deg[i] == 0:
            que.append(i)
            tot += 1
    ret = []
    # BFS to Topo-sort
    while len(que) > 0:
        cur, que = que[0], que[1:]
        ret.append(cur)
        if cur in hashmap:
            for i in hashmap[cur]:
                # only classes w/o following classes can be traversed
                deg[i] -= 1
                if deg[i] == 0:
                    que.append(i)
                    tot += 1
    ret.reverse()
    if tot == numCourses:
        return ret
    else:
        return []
```

##### 269. alien dictionary

There is a new alien language which uses the latin alphabet. However, the order among letters are unknown to you. You receive a list of words from the dictionary, where words are sorted lexicographically by the rules of this new language. Derive the order of letters in this language.

For example, Given the following words in dictionary,

```py
[
  "wrt",
  "wrf",
  "er",
  "ett",
  "rftt"
]
```

The correct order is: "wertf".

Note: You may assume all letters are in lowercase. If the order is invalid, return an empty string. There may be multiple valid order of letters, return any one of them is fine.

主要难点在读题，他这个是和以前做过的题目略有不同的，题目的意思是外星人的字母顺序与人类不同，但外星人提供了他们的字母排序算法，你要根据这个算法来求外星人语言的字母顺序

其实就是个简单的拓扑排序去BFS，烦人的地方在于这个图你要自己画，他这个答案为了速度是选用的邻接矩阵，我个人是不太赞赏的，空间复杂度忒不和谐

```java
private final int N = 26;
public String alienOrder(String[] words) {
    boolean[][] adj = new boolean[N][N];
    int[] visited = new int[N];
    buildGraph(words, adj, visited);

    StringBuilder sb = new StringBuilder();
    for(int i = 0; i < N; i++) {
        if(visited[i] == 0) {                 // unvisited
            if(!dfs(adj, visited, sb, i)) return "";
        }
    }
    return sb.reverse().toString();
}

public boolean dfs(boolean[][] adj, int[] visited, StringBuilder sb, int i) {
    visited[i] = 1;                            // 1 = visiting
    for(int j = 0; j < N; j++) {
        if(adj[i][j]) {                        // connected
            if(visited[j] == 1) return false;  // 1 => 1, cycle
            if(visited[j] == 0) {              // 0 = unvisited
                if(!dfs(adj, visited, sb, j)) return false;
            }
        }
    }
    visited[i] = 2;                           // 2 = visited
    sb.append((char) (i + 'a'));
    return true;
}

public void buildGraph(String[] words, boolean[][] adj, int[] visited) {
    Arrays.fill(visited, -1);                 // -1 = not even existed
    for(int i = 0; i < words.length; i++) {
        for(char c : words[i].toCharArray()) visited[c - 'a'] = 0;
        if(i > 0) {
            String w1 = words[i - 1], w2 = words[i];
            int len = Math.min(w1.length(), w2.length());
            for(int j = 0; j < len; j++) {
                char c1 = w1.charAt(j), c2 = w2.charAt(j);
                if(c1 != c2) {
                    adj[c1 - 'a'][c2 - 'a'] = true;
                    break;
                }
            }
        }
    }
}
```

##### 126 & 127. Word Ladder I / II

Given two words (beginWord and endWord), and a dictionary's word list, find all shortest transformation sequence(s) from beginWord to endWord, such that:

1. Only one letter can be changed at a time
2. Each transformed word must exist in the word list. Note that beginWord is not a transformed word.

Note:

- Return an empty list if there is no such transformation sequence.
- All words have the same length.
- All words contain only lowercase alphabetic characters.
- You may assume no duplicates in the word list.
- You may assume beginWord and endWord are non-empty and are not the same.

```py
beginWord = "hit",
endWord = "cog",
wordList = ["hot","dot","dog","lot","log","cog"]
```

Output:

```py
[
  ["hit","hot","dot","dog","cog"],
  ["hit","hot","lot","log","cog"]
]
```

这题的思路是很无聊的，无非是BFS和DFS而已，难点：

1. 两个字符串有一个char不同：这题大家用的都是笨方法，n^26
2. 用一个parents<son, parent>储存从前向后的BFS所见到的顺序
    1. key是变化后的单词，value是它的来源
3. 曾经在parents里出现过一次的单词是不允许出现循环的
4. 用update函数更新parents
5. 用一轮反向DFS整理最后的结果：
    1. r in res：r是从前向后的，但搜索的顺序是从后向前的

```py
def findLadders(self, beginWord, endWord, wordList):
    dic = set(wordList)
    level = {beginWord}
    parents = collections.defaultdict(set)
    while level and endWord not in parents:
        next_level = collections.defaultdict(set)
        for node in level:
            for char in string.ascii_lowercase:
                for i in range(len(beginWord)):
                    n = node[:i] + char + node[i+1:]
                    # 1. new word in wordList
                    # 2. no cycle in ladder
                    if n in dic and n not in parents:
                        next_level[n].add(node)
        level = next_level
        parents.update(next_level)
    res = [[endWord]]
    while res and res[0][0] != beginWord:
        res = [r.append(p) for r in res for p in parents[r[0]]]
    return res
```
