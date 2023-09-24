树是最简单的数据结构，主要是因为一般不会用迭代来处理树的内容，只有递归的话比较好想

### 定义

树的定义：

- 节点的集合
- 链接这些节点的边的集合
    - 限定条件：任意两个节点之间只有唯一的路径相连

有根树的定义：

- 除了根节点以外的节点N都只有一个父节点，定义为从节点N到根节点路径上第一个节点
- 根节点一般画在树的顶上
- 叶节点没有子节点

平衡树的定义：任给一个节点，其两个子树的深度差，最大不超过1

#### 例题

##### 226. Invert Binary Tree

```py
def invertTree(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
    if root == None:
        return root
    root.left, root.right = self.invertTree(root.right), self.invertTree(root.left)
    return root
```

##### 104. Maximum Depth of Binary Tree

```py
def maxDepth(self, root):
    if root is None:
        return 0
    return 1 + max(self.maxDepth(root.left), self.maxDepth(root.right))
```

##### 111. Minimum Depth of Binary Tree

Given a binary tree, find its minimum depth. The minimum depth is the number of nodes along the shortest path from the root node down to the nearest leaf node.

这个题目的陷阱在于如果一个节点只有一侧有儿子，你是不能返回min(left, right)的，因为此时有一侧返回值是0

```py
def minDepth(self, root: Optional[TreeNode]) -> int:
    if not root:
        return 0
    if not root.left or not root.right:
        return self.minDepth(root.left) + self.minDepth(root.right) + 1
    else:
        return min(self.minDepth(root.left), self.minDepth(root.right)) + 1
```

### 树的遍历

- 广度优先（BFS）
- 深度优先（DFS）
    - 前序
    - 中序
    - 后序

![树的遍历](https://assets.leetcode.com/users/andvary/image_1556551007.png)

```py
def inorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
    if root == None:
        return []
    return self.inorderTraversal(root.left) + [root.val] + self.inorderTraversal(root.right)

def preorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
    if not root:
        return []
    return [root.val] + self.preorderTraversal(root.left) + self.preorderTraversal(root.right)

def postorderTraversal(self, root: Optional[TreeNode]) -> List[int]:
    if not root:
        return []
    return self.postorderTraversal(root.left) + self.postorderTraversal(root.right) + [root.val]
```

#### 例题

##### 94, 144, 145. Binary Tree Inorder / Preorder / Postorder Traversal

递归解法有点过于基础了，迭代解法的重点在于插入栈的顺序，其实是递归操作的反向顺序。

```py
def preorderTraversal(self, root: TreeNode) -> List[int]:
    res, stack = [], [(root, False)]
    while stack:
        node, visited = stack.pop()  # the last element
        if node:
            if visited: res.append(node.val)
            else:  # preorder: root -> left -> right
                stack += [(node.right, False), (node.left, False), (node, True)]
    return res

def inorderTraversal(self, root: TreeNode) -> List[int]:
    res, stack = [], [(root, False)]
    while stack:
        node, visited = stack.pop()  # the last element
        if node:
            if visited: res.append(node.val)
            else:  # inorder: left -> root -> right
                stack += [(node.right, False), (node, True), (node.left, False)]
    return res

def postorderTraversal(self, root: TreeNode) -> List[int]:
    res, stack = [], [(root, False)]
    while stack:
        node, visited = stack.pop()  # the last element
        if node:
            if visited: res.append(node.val)
            else:  # postorder: left -> right -> root
                stack += [(node, True), (node.right, False), (node.left, False)]
    return res
```

##### 105 & 106. Construct Binary Tree from Preorder and Inorder Traversal (Inorder and Postorder Traversal)

这题目实际是在考察对前序和中序遍历的理解，前序遍历的话最左侧的项是root，以此去寻找中序遍历数组中的root，中序遍历数组里root的左边是左子树，右边是右子树，注意python里`pop(0)`是弹出最左侧，`pop()`是弹出最右侧

```py
def buildTree(self, preorder, inorder):
    if not inorder or not preorder:
        return None
    root = TreeNode(preorder.pop(0))
    inorderIndex = inorder.index(root.val)
    root.left = self.buildTree(preorder, inorder[:inorderIndex])
    root.right = self.buildTree(preorder, inorder[inorderIndex + 1:])
    return root
```

106的话和105类似，只不过改成了pop掉postorder的最后一项，而且需要注意，因为是从右向左pop，需要先构造右子树，再构造左子树，这个顺序不能搞错

```py
def buildTree(self, inorder, postorder):
    if not inorder or not postorder:
        return None
    root = TreeNode(postorder.pop())
    inorderIndex = inorder.index(root.val)
    root.right = self.buildTree(inorder[inorderIndex + 1:], postorder)
    root.left = self.buildTree(inorder[:inorderIndex], postorder)
    return root
```

##### 297, 449. Serialize and Deserialize Binary Tree / BST

这题其实没说要你把二叉树弄成啥样……这个解法就是复杂版前序遍历而已

```py
def serialize(self, root):
    def preorder(node):
        if node:
            vals.append(str(node.val))
            preorder(node.left)
            preorder(node.right)
        else:
            vals.append('#')
    vals = []
    preorder(root)
    return " ".join(vals)

def deserialize(self, data):
    def preorder():
        val = next(vals)
        if val == '#':
            return None
        else:
            node = TreeNode(int(val))
            node.left = preorder()
            node.right = preorder()
            return node
    vals = iter(data.split())
    return preorder()
```

BST版本比起原版，可以通过对BST性质的利用，省略掉空节点的叙述

```py
def serialize(self, root):
    def preorder(node):
        if node:
            vals.append(str(node.val))
            preorder(node.left)
            preorder(node.right)
    vals = []
    preorder(root)
    return " ".join(vals)

def deserialize(self, data):
    def preorder(minVal, maxVal):
        if vals and minVal < vals[0] < maxVal:
            val = vals.pop(0)
            node = TreeNode(val)
            node.left = preorder(minVal, val)
            node.right = preorder(val, maxVal)
            return node
    vals = [int(val) for val in data.split()]
    return preorder(-2**32, 2**32)
```

### 二叉搜索树

序：

- 完整性、传递性、反对称性
- 任给两个内容p和q：
    - 必有一p>q或者q>p为真，不可能同时为真
    - q>p而且r>q表示r>p

二叉搜索树的性质：

- 每个节点的左子树，其每一个节点的内容都小于这个节点的内容
- 每个节点的右子树，其每一个节点的内容都大于这个节点的内容
- 不允许重复内容
- 使用中序遍历BST得到的是有序数组

##### 搜索

```py
def find(BST T, key k):
    if T == None:
        return None
    if T.key == k:
        return T
    elif T.key > k:
        return find(T.left, k)
    else:
        return find(T.right, k)
```

时间复杂度为`O (log n)`

##### 插入

- 先搜索，如果找到了key，什么也不做
- 如果没有找到，则插入一个新的节点

```py
def insert(BST T, key k):
    if T == None:
        return BST(k)
    if T.key > k:
        T.left = insert(T.left, k)
    elif T.key < k:
        T.right = insert(T.right, k)
    return T
```

要反对一种陋习，就是Arms length recursion，在base case之前就进行检查，插入特例结果，而不是使用递归：

```py
if T.left == None:
    T.left = new BST(k)
elif T.right == None:
    T.right = new BST(k)
```

这种陋习有四个弊病：
- 降低可读性
- 降低可维护性
- 较难验证
- 对性能没有优化

##### 删除

三个可能性：
- key是叶子 -- 直接切掉父节点的链接，叶子会被垃圾回收
- key有一个子节点
    - 目标是维持BST性质
    - 把父节点的指向自己的指针，移到子节点上
    - key这个节点将会被垃圾回收
- key有两个子节点
    - 找到左子树里最右侧的叶子，或者右子树里最左侧的叶子
    - 把这个叶子移到key处，并删除key

#### 例题

##### 98. Validate Binary Search Tree

这题用中序遍历简直弱智，所以最好别这么写

```py
def isValidBST(self, root):
    return self.validity(root, -2**32, 2**32)

def validity(self, root, left, right):
    if not root:
        return True
    if root.val >= right or root.val <= left:
        return False
    return self.validity(root.left, left, root.val) and self.validity(root.right, root.val, right)
```

##### 108 & 109. Convert Sorted Array / List to Binary Search Tree

这俩题目一样的套路，分治法，找到中点为root，然后递归左侧构建左子树，递归右侧构建右子树

```py
def sortedArrayToBST(self, nums: List[int]) -> Optional[TreeNode]:
    if not nums:
        return None
    else:
        l = len(nums)
        mid = l // 2
        return TreeNode(
            val=nums[mid],
            left=self.sortedArrayToBST(nums[:mid]),
            right=self.sortedArrayToBST(nums[mid+1:])
        )
```

```py
def sortedListToBST(self, head):
    return self.listToBST(head, None)

def listToBST(self, head, tail):
    if head == tail:
        return None
    else:
        slow, fast = head, head
        while fast != tail and fast.next != tail:
            slow, fast = slow.next, fast.next.next
        root = TreeNode(slow.val)
        root.left = self.listToBST(head, slow)
        root.right = self.listToBST(slow.next, tail)
        return root
```

##### 173. Binary Search Tree Iterator

这题就是利用BST的中序遍历性质，先把最左边的都塞进栈里，然后就类似于中序遍历，每次只有从栈里弹出来的元素才把他的右子树塞进去

```py
def __init__(self, root):
    self.stack = []
    while root:
        self.stack.append(root)
        root = root.left

# @return a boolean, whether we have a next smallest number
def hasNext(self):
    return len(self.stack) > 0

# @return an integer, the next smallest number
def next(self):
    node = self.stack.pop()
    x = node.right
    while x:
        self.stack.append(x)
        x = x.left
    return node.val
```

##### 285. Inorder Successor in BST

货真价实的简单题，BST中序遍历是单调递增的，所以实际就是在BST里寻找比p大的最小值咯

```py
def inorderSuccessor(self, root, p):
    res = None
    while root:
        if p.val < root.val:
            res = root
            root = root.left
        else:
            root = root.right
    return res
```

##### 235/6 Lowest Common Ancestor of BST / Binary Tree

BST的话要注意方向，如果不是BST的话要注意他是如何变换状态的

```py
def lowestCommonAncestor(self, root, p, q):
    if root.val < p.val and root.val < q.val:
        return self.lowestCommonAncestor(root.right, p, q)
    elif root.val > p.val and root.val > q.val:
        return self.lowestCommonAncestor(root.left, p, q)
    else:
        return root

def lowestCommonAncestor(self, root, p, q):
    if root is None or root is p or root is q:
        return root
    else:
        left = self.lowestCommonAncestor(root.left, p, q)
        right = self.lowestCommonAncestor(root.right, p, q)
        if left and right:
            return root
        elif left:
            return left
        elif right:
            return right
```

##### 99. Recover Binary Search Tree

Two elements of a binary search tree (BST) are swapped by mistake. Recover the tree without changing its structure.

```md
Input: [1,3,null,null,2]

   1
  /
 3
  \
   2

Output: [3,1,null,null,2]

   3
  /
 1
  \
   2
```

这题看着有点难，但实际上是利用的中序遍历的性质

1. BST的中序遍遍历必为顺序，那么每次遍历的时候记录prev跟cur
2. 第一次遇到`prev > cur`的话记录下来需要交换的`first = prev`
3. 第二次记录下来`second = cur`
4. 结尾处交换一下`first`和`second`即可

```java
TreeNode firstElement = null;
TreeNode secondElement = null;
TreeNode prevElement = new TreeNode(Integer.MIN_VALUE);
public void recoverTree(TreeNode root) {
    // In order traversal to find the two elements
    traverse(root);
    // Swap the values of the two nodes
    int temp = firstElement.val;
    firstElement.val = secondElement.val;
    secondElement.val = temp;
}
private void traverse(TreeNode root) {
    if (root == null)
        return;
    traverse(root.left);
    if (firstElement == null && prevElement.val >= root.val)
        firstElement = prevElement;
    if (firstElement != null && prevElement.val >= root.val)
        secondElement = root;
    prevElement = root;
    traverse(root.right);
}
```

##### 95 & 96. Unique Binary Search Trees

Given an integer n, generate all structurally unique BST's (binary search trees) that store values 1 ... n.

```md
Input: 3
Output:
[
  [1,null,3,2],
  [3,2,null,1],
  [3,1,null,null,2],
  [2,1,3],
  [1,null,2,null,3]
]
Explanation:
The above output corresponds to the 5 unique BST's shown below:

   1         3     3      2      1
    \       /     /      / \      \
     3     2     1      1   3      2
    /     /       \                 \
   2     1         2                 3
```

这题目的题眼就是BST的性质：每个节点的左子树都比他小，右子树都比他大

```py
def generateTrees(self, n):
    if n == 0:
        return []
    def generate(first, last):
        trees = []
        for root in range(first, last+1):
            # B/c this is BST, left sub-tree < root < right sub-tree
            for left in generate(first, root-1):
                for right in generate(root+1, last):
                    node = TreeNode(root)
                    node.left = left
                    node.right = right
                    trees.append(node)
        return trees or [None]
    return generate(1, n)
```

注意96题因为有时间限制，单纯递归是不管用的，95因为要逐个生成每个树，所以不用递归也不可能

我们首先定义`G(n)`是长度n的序列能生成的独特BST树数量，`F(i,n)`是以i为root，从1到n的序列能生成的独特BST树数量，所以`G(n)=sum(F(i,n))`，而我们观察95题，得到`F(i,n) = G(i-1) * G(n-i)`

```java
public int numTrees(int n) {
    int [] G = new int[n+1];
    G[0] = G[1] = 1;
    for(int i=2; i<=n; ++i) {
        for(int j=1; j<=i; ++j) {
            G[i] += G[j-1] * G[i-j];
        }
    }
    return G[n];
}
```

### 一些杂题

##### Ternary Expression to Binary Tree

这题目leetcode里没有，可能因为太简单？两个解法，一个是栈解法：

1. 一开始推进去一个树节点
2. 每次向右推两格
    1. 遇到问号呢，就把当前栈顶节点的左子树加上这个节点
    2. 遇到冒号的话，先往外弹一个，然后如果遇到右子树满着的节点，一路从栈往外弹，直到遇到右子树为空的节点
    3. 每次循环的最后把节点推进去

另一个是递归，问号后面的【整个字符串】是左子树，冒号后面的【整个字符串】是右子树呗

```java
public TreeNode convert(String expr) {
    char[] exp = expr.toCharArray();
    if (exp.length == 0)
        return null;
    TreeNode root = new TreeNode(exp[0]);
    Stack<TreeNode> stack = new Stack<>();
    stack.push(root);
    for (int i = 1; i < exp.length; i += 2) {
        TreeNode node = new TreeNode(exp[i + 1]);
        if (exp[i] == '?')
            stack.peek().left = node;
        if (exp[i] == ':') {
            stack.pop();
            while (stack.peek().right != null)
                stack.pop();
            stack.peek().right = node;
        }
        stack.push(node);
    }
    return root;
}

Node convertExpression(char[] expression, int i) {
    if (i >= expression.length)
        return null;
    Node root = new Node(expression[i]);
    ++i;
    if (i < expression.length && expression[i]=='?')
        root.left = convertExpression(expression, i+1);
    else if (i < expression.length)
        root.right = convertExpression(expression, i+1);
    return root;
}
```

##### 572. Subtree of Another Tree

这题其实没说要你把二叉树弄成啥样……这个解法就是复杂版前序遍历而已

```py
def isSubtree(self, s, t):
    def helper(s, t, root):
        if not s and not t:
            return True
        if not s or not t:
            return False
        if s.val == t.val:
            return (helper(s.left, t.left, False) and helper(s.right, t.right, False)) or (helper(s.left, t, True) or helper(s.right, t, True))
        else:
            if root:
                return helper(s.left, t, True) or helper(s.right, t, True)
            else:
                return False
    return helper(s, t, True)
```

##### 124. Binary Tree Maximum Path Sum

这题名为难题实际上没多难……重点是体会递归关系

```java
int maxValue;

public int maxPathSum(TreeNode root) {
    maxValue = Integer.MIN_VALUE;
    maxPathDown(root);
    return maxValue;
}

private int maxPathDown(TreeNode node) {
    if (node == null) return 0;
    int left = Math.max(0, maxPathDown(node.left));
    int right = Math.max(0, maxPathDown(node.right));
    maxValue = Math.max(maxValue, left + right + node.val);
    return Math.max(left, right) + node.val;
}
```

##### 543. Diameter of Binary Tree

这个做法似乎是唯一解？但反正在二叉树问题里时间复杂度是很不漂亮的一种了

```py
def diameterOfBinaryTree(self, root):
    self.best = 1
    def depth(root):
        if not root:
            return 0
        depL = depth(root.left)
        depR = depth(root.right)
        self.best = max(self.best, depL + depR + 1)
        return max(depL, depR) + 1
    depth(root)
    return self.best - 1
```
