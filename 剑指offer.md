## 03 数组中重复的数字

找出数组中重复的数字。


在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

**示例 1：**

```
输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 
```

**解法一：可以修改原数组**

也称鸽巢原理

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        // 省略边界处理
        for (int i = 0; i < nums.length; i++) {
            while (nums[i] != i) {
                if (nums[i] == nums[nums[i]]) return nums[i];
                int tmp = nums[i];
                nums[i] = nums[tmp];
                nums[tmp] = tmp;
            }
        }
        return -1;
    }
}
```

时间O(n)，空间O(n)

**解法二：不可以修改原数组**

使用二分，但不能通过，因为题目有微小的区别，原题是大小为n的数组，有n-1的范围，而该题是n的范围。

遇到[0, 1, 2, 0, 4, 5, 6, 7, 8, 9]有5个数小于等于4，5个数大于4，导致二分出错。

```java
class Solution {
    public int findRepeatNumber(int[] nums) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + (right - left) / 2;
            int count = countRange(nums, left, mid);
            if (left == right) {
                if (count > 1) return left;
            }
            if (count > mid - left + 1)
                right = mid;
            else left = mid + 1;
        }
        return -1;
    }

    private int countRange(int[] nums, int start, int end) {
        int res = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] >= start && nums[i] <= end)
                res++;
        }
        return res;
    }
}
```

时间O(nlogn)，空间O(1)

## 04 二维数组中的查找

在一个 n * m 的二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

**示例:**

现有矩阵 matrix 如下：

```
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]
```


给定 target = 5，返回 true。

给定 target = 20，返回 false。

**限制：**

- 0 <= n <= 1000

- 0 <= m <= 1000


 **解法：**

不断切掉最右边的列和最上边的行，如果剩余矩阵的右上角为target，返回true，否则返回false

```java
class Solution {
    public boolean findNumberIn2DArray(int[][] matrix, int target) {
        if (matrix.length == 0 || matrix[0].length == 0)
            return false;
        int i = 0, j = matrix[0].length - 1;
        while (j >= 0 && i < matrix.length) {
            if (matrix[i][j] > target) j--;
            else if (matrix[i][j] < target) i++;
            else if (matrix[i][j] == target) return true;
        }
        return false;
    }
}
```

## 05  替换空格

请实现一个函数，把字符串 s 中的每个空格替换成"%20"。

**示例 1：**

```
输入：s = "We are happy."
输出："We%20are%20happy."
```

**限制：**

- 0 <= s 的长度 <= 10000


**解法：**

先遍历一遍，求出有几个字符，算出要扩容的量，再从后往前填写，但参数是String类型就不可能原地解决

快乐就完事了

```java
class Solution {
    public String replaceSpace(String s) {
        return s.replace(" ", "%20");
    }
}
```

## 06 从尾到头打印链表

输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

**示例 1：**

```
输入：head = [1,3,2]
输出：[2,3,1]
```

**限制：**

- 0 <= 链表长度 <= 10000


**解法一：先反转单链表后输出**

```java
class Solution {
    public int[] reversePrint(ListNode head) {
        ListNode node = reverse(head);
        ListNode tmp = node;
        int len = 0, idx = 0;
        while (tmp != null) {
            len++;
            tmp = tmp.next;
        }
        int[] res = new int[len];
        while (node != null) {
            res[idx++] = node.val;
            node = node.next;
        }
        return res;
    }

    private ListNode reverse(ListNode node) {
        if (node == null || node.next == null) return node;
        ListNode newHead = reverse(node.next);
        ListNode tmp = node.next;
        tmp.next = node;
        node.next = null;
        return newHead;
    }
}
```

**解法二：不修改原链表的情况下打印**

```java
class Solution {
    public int[] reversePrint(ListNode head) {
        Deque<Integer> stack = new LinkedList<>();
        while (head != null) {
            stack.push(head.val);
            head = head.next;
        }
        int[] res = new int[stack.size()];
        int idx = 0;
        while (!stack.isEmpty()) {
            res[idx++] = stack.pop();
        }
        return res;
    }
}
```

**解法三：在直接输出的情况下可用**

直接使用递归栈

```java
public static void reversePrint(ListNode node) {
    if (node == null) return;
    reversePrint(node.next);
    System.out.println(node.val);
}
```

## 07 重建二叉树

输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

例如，给出

```
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
```

返回如下的二叉树：

```
	3
   / \
  9  20
    /  \
   15   7
```

**限制：**

- 0 <= 节点个数 <= 5000


**解法：**

也可以把中序遍历的数组按<值，下标>存成map，可以减少在中序中找节点的开销。

```java
class Solution {
    int[] preorder;
    int[] inorder;

    public TreeNode buildTree(int[] preorder, int[] inorder) {
        this.preorder = preorder;
        this.inorder = inorder;
        return recursion(0, preorder.length, 0, inorder.length);
    }

    // 参数：前序的范围和中序的范围(前闭后开)
    private TreeNode recursion(int preLeft,int preRight,int inLeft,int inRight){
        if (preLeft == preRight || inLeft == inRight) return null;
        int val = preorder[preLeft];    // 根节点
        int posi = -1;  // 中序的根节点位置
        for (int i = inLeft; i < inRight; i++) {
            if (inorder[i] == val) {
                posi = i;
                break;
            }
        }
        int leftLen = posi - inLeft;    // 左子树大小
        int rightLen = inRight - posi - 1;  // 右子树大小
        TreeNode node = new TreeNode(val);  // 返回的结点
        node.left = recursion(preLeft + 1, 
                preLeft + leftLen + 1, inLeft, posi);
        node.right = recursion(preLeft + leftLen + 1, 
                preLeft + 1 + leftLen + rightLen, posi + 1, inRight);
        return node;
    }
}
```

## 09 用两个栈实现队列

用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。(若队列中没有元素，deleteHead 操作返回 -1 )

**示例 1：**

```
输入：
["CQueue","appendTail","deleteHead","deleteHead"]
[[],[3],[],[]]
输出：[null,null,3,-1]
```

**示例 2：**

```
输入：
["CQueue","deleteHead","appendTail","appendTail","deleteHead","deleteHead"]
[[],[],[5],[2],[],[]]
输出：[null,-1,null,null,5,2]
```

**提示：**

- 1 <= values <= 10000
- 最多会对 appendTail、deleteHead 进行 10000 次调用

**解法：**

```java
class CQueue {
    Deque<Integer> stack1;  // 用于入栈
    Deque<Integer> stack2;  // 用于出栈

    public CQueue() {
        stack1 = new LinkedList<>();
        stack2 = new LinkedList<>();
    }
    
    public void appendTail(int value) {
        stack1.push(value);
    }
    
    public int deleteHead() {
        if (!stack2.isEmpty()) return stack2.pop();
        while (!stack1.isEmpty()) {
            stack2.push(stack1.pop());
        }
        if (stack2.isEmpty()) return -1;
        return stack2.pop();
    }
}
```

## 10-I 斐波那契数列

写一个函数，输入 n ，求斐波那契（Fibonacci）数列的第 n 项。斐波那契数列的定义如下：

F(0) = 0,   F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
斐波那契数列由 0 和 1 开始，之后的斐波那契数就是由之前的两数相加而得出。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

**示例 1：**

```
输入：n = 2
输出：1
```

**示例 2：**

```
输入：n = 5
输出：5
```

**提示：**

- 0 <= n <= 100


**解法：**

```java
class Solution {
    public int fib(int n) {
        if (n == 0) return 0;
        if (n == 1) return 1;
        int mod = 1000000007;
        // c是fib(n) a是fib(n-1) b是fib(n-2)
        int a = 1, b = 0, c = 0;
        for (int i = 2; i <= n; i++) {
            c = (a + b) % mod;
            b = a;
            a = c;
        }
        return c;
    }
}
```

## 10-II 青蛙跳台阶问题

一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。

**示例 1：**

```
输入：n = 2
输出：2
```

**示例 2：**

```
输入：n = 7
输出：21
```

**提示：**

- 0 <= n <= 100


**解法：**

```java
class Solution {
    public int numWays(int n) {
        if (n == 0) return 1;
        if (n == 1) return 1;
        int mod = 1000000007;
        // c是fib(n) a是fib(n-1) b是fib(n-2)
        int a = 1, b = 1, c = 0;
        for (int i = 2; i <= n; i++) {
            c = (a + b) % mod;
            b = a;
            a = c;
        }
        return c;
    }
}
```

## 11 旋转数组的最小数字

把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如，数组 [3,4,5,1,2] 为 [1,2,3,4,5] 的一个旋转，该数组的最小值为1。  

**示例 1：**

```
输入：[3,4,5,1,2]
输出：1
```

**示例 2：**

```
输入：[2,2,2,0,1]
输出：0
```

**书上的解法，看中间移两边：**

```java
class Solution {
    public int minArray(int[] numbers) {
        int left = 0, right = numbers.length - 1;
        while (numbers[left] >= numbers[right]) {
            if (left == right - 1) {
                return numbers[right];
            }
            int mid = left + (right - left) / 2;
            if (numbers[left]==numbers[right]&&numbers[left]==numbers[mid]) {
                return inorderSearch(numbers, left, right);
            }
            if (numbers[mid] >= numbers[left])
                left = mid;
            else if (numbers[mid] <= numbers[right])
                right = mid; 
        }
        return numbers[left];
    }

    private int inorderSearch(int[] numbers, int left, int right) {
        for (int i = left + 1; i <= right; i++) {
            if (numbers[i] < numbers[i - 1]) {
                return numbers[i];
            }
        }
        return numbers[left];
    }
}
```

**更简洁的实现，只比右边和中间：**

```java
class Solution {
    public int findMin(int[] nums) {
        int left = 0, right = nums.length - 1;
        if (nums[left] < nums[right]) return nums[0];
        while (left < right){
            int mid = left + (right - left) / 2;
            if (nums[mid] > nums[right]) left = mid + 1;
            else if (nums[mid] < nums[right]) right = mid;
            else right = right - 1;
        }
        return nums[left];
    }
}
```

## 12 矩阵中的路径

请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一格开始，每一步可以在矩阵中向左、右、上、下移动一格。如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。例如，在下面的3×4的矩阵中包含一条字符串“bfce”的路径（路径中的字母用加粗标出）。

[["a","b","c","e"],
["s","f","c","s"],
["a","d","e","e"]]

但矩阵中不包含字符串“abfb”的路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入这个格子。

**示例 1：**

```
输入：board = [["A","B","C","E"],["S","F","C","S"],["A","D","E","E"]], word = "ABCCED"
输出：true
```

**示例 2：**

```
输入：board = [["a","b"],["c","d"]], word = "abcd"
输出：false
```

**提示：**

- 1 <= board.length <= 200
- 1 <= board[i].length <= 200

**解法：**

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        if (board == null || board[0] == null || board.length == 0 || board[0].length == 0 || word == null || word.equals("")) {
            return false;
        }
        boolean[][] isVisited = new boolean[board.length][board[0].length];
        char[] chs = word.toCharArray();

        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[0].length; j++) {
                if (board[i][j] == chs[0]) {
                    if (bfs(board, i, j, isVisited, chs, 0)) return true;
                }
            }
        }
        return false;
    }

    private boolean bfs(char[][] board, int i, int j, boolean[][] isVisited, char[] chs, int index) {
        if (index == chs.length) {
            return true;
        }
        // 判断越界、是否和字符是否相等
        if (i < 0 || j < 0 || i == board.length || j == board[0].length || isVisited[i][j] || board[i][j] != chs[index]) {
            return false;
        }
        isVisited[i][j] = true;
        boolean res = bfs(board, i + 1, j, isVisited, chs, index + 1)
                || bfs(board, i - 1, j, isVisited, chs, index + 1)
                || bfs(board, i, j + 1, isVisited, chs, index + 1)
                || bfs(board, i, j - 1, isVisited, chs, index + 1);
        isVisited[i][j] = false;
        return res;
    }
}
```

## 13 机器人的运动范围

地上有一个m行n列的方格，从坐标 [0,0] 到坐标 [m-1,n-1] 。一个机器人从坐标 [0, 0] 的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

**示例 1：**

```
输入：m = 2, n = 3, k = 1
输出：3
```

**示例 2：**

```
输入：m = 3, n = 1, k = 0
输出：1
```

**提示：**

- 1 <= n,m <= 100
- 0 <= k <= 20

**解法：**

```java
class Solution {
    boolean[][] visited;

    public int movingCount(int m, int n, int k) {
        this.visited = new boolean[m][n];
        return backTrace(m, n, k, 0, 0);
    }

    // 行数 列数 k值 行下标 列下标
    private int backTrace(int m, int n, int k, int i, int j) {
        int count = 0;
        if (i<0||i>=m||j<0||j>=n||visited[i][j]||check(i,j)>k)
            return 0;
        visited[i][j] = true;
        count += 1 + backTrace(m, n, k, i + 1, j)
                + backTrace(m, n, k, i - 1, j)
                + backTrace(m, n, k, i, j + 1)
                + backTrace(m, n, k, i, j - 1);
        return count;
    }

    // 数位和
    private int check(int i, int j) {
        int sum = 0;
        while (i > 0) {
            sum += i % 10;
            i /= 10;
        }
        while (j > 0) {
            sum += j % 10;
            j /= 10;
        }
        return sum;
    }
}
```

## 14-I 剪绳子 DP

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 `k[0]*k[1]*...*k[m-1] `可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

**示例 1：**

```
输入: 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1
```

**示例 2:**

```
输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36
```

**提示：**

- 2 <= n <= 58

**解法：**

状态：i >= 4时：dp[i] = 长度为 i 的绳子的最大乘积；i < 4 时 dp[i] = i

转移：dp[i] = max(dp[i - x] * dp[x])

base case: i < 4 时 dp[i] = i。dp[0]舍弃。因为当n < 4时，剪过的乘积小于本身的长度，而题目要求至少剪一刀

```java
class Solution {
    public int cuttingRope(int n) {
        if (n == 2) return 1;
        if (n == 3) return 2;
        int[] dp = new int[n + 1];
        dp[1] = 1;
        dp[2] = 2;
        dp[3] = 3;
        for (int i = 4; i < dp.length; i++) {
            int left = 1, right = i - 1;
            dp[i] = 0;
            while (left <= right) {
                dp[i] = Math.max(dp[i], dp[left++] * dp[right--]);
            }
        }
        return dp[n];
    }
}
```

## 14-II 剪绳子 贪心

给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。请问 `k[0]*k[1]*...*k[m-1] `可能的最大乘积是多少？例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

**示例 1：**

```
输入: 2
输出: 1
解释: 2 = 1 + 1, 1 × 1 = 1
```

**示例 2:**

```
输入: 10
输出: 36
解释: 10 = 3 + 3 + 4, 3 × 3 × 4 = 36
```

**提示：**

- 2 <= n <= 1000

**解法：**

找规律得：

```java
n     乘积     子数字
2       1       1 1
3       2       1 2
4       4       2 2
5       6       2 3
6       9       3 3
7       12      2 2 3
8       18      2 3 3
9       27      3 3 3
10      36      2 2 3 3
11      54      2 3 3 3
12      81      3 3 3 3
13      108     2 2 3 3 3
14      162     2 3 3 3 3
15      243     3 3 3 3 3
16      324     2 2 3 3 3 3
17      486     2 3 3 3 3 3
18      729     3 3 3 3 3 3
19      972     2 2 3 3 3 3 3
20      1458    2 3 3 3 3 3 3
21      2187    3 3 3 3 3 3 3
22      2916    2 2 3 3 3 3 3 3
23      4374    2 3 3 3 3 3 3 3
24      6561    3 3 3 3 3 3 3 3
25      8748    2 2 3 3 3 3 3 3 3
26      13122   2 3 3 3 3 3 3 3 3
27      19683   3 3 3 3 3 3 3 3 3
28      26244   2 2 3 3 3 3 3 3 3 3
29      39366   2 3 3 3 3 3 3 3 3 3
```

也就是5以上的数字需要分出尽可能多的3

```java
class Solution {
    public int cuttingRope(int n) {
        if (n == 2) return 1;
        if (n == 3) return 2;
        int mod = 1000000007;
        long res = 1;	// 注意这里一定是long，10亿*3>21亿
        while (n > 4) {
            res *= 3;
            res %= mod;
            n -= 3;
        }
        return (int)(res * n % mod);
    }
}
```

## 15 二进制中1的个数

请实现一个函数，输入一个整数，输出该数二进制表示中 1 的个数。例如，把 9 表示成二进制是 1001，有 2 位是 1。因此，如果输入 9，则该函数输出 2。

**示例 1：**

```
输入：00000000000000000000000000001011
输出：3
解释：输入的二进制串 00000000000000000000000000001011 中，共有三位为 '1'。
```

**示例 2：**

```
输入：00000000000000000000000010000000
输出：1
解释：输入的二进制串 00000000000000000000000010000000 中，共有一位为 '1'。
```

**示例 3：**

```
输入：11111111111111111111111111111101
输出：31
解释：输入的二进制串 11111111111111111111111111111101 中，共有 31 位为 '1'。
```

**解法一：**

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int res = 0;
        while(n != 0) {
            res+=n&1;
            n=n>>>1;    // 注意>>>是无符号位移
            // >> 是有符号位移，负数右移会在左边填1，造成死循环
        }
        return res;
    }
}
```

**解法二**

```java
public class Solution {
    public int hammingWeight(int n) {
        int count = 0;
        while (n != 0) {
            count++;
            n = (n - 1) & n;	// 把最末尾的一个1变为0
        }
        return count;
    }
}
```

**JDK中的解法：**

```java
public class Solution {
    public int hammingWeight(int n) {
        return Integer.bitCount(n);
    }
}
```

源码：

```java
public static int bitCount(int i) {
    // HD, Figure 5-2
    i = i - ((i >>> 1) & 0x55555555);
    i = (i & 0x33333333) + ((i >>> 2) & 0x33333333);
    i = (i + (i >>> 4)) & 0x0f0f0f0f;
    i = i + (i >>> 8);
    i = i + (i >>> 16);
    return i & 0x3f;
}
```

## 16 数值的整数次方

实现函数double Power(double base, int exponent)，求base的exponent次方。不得使用库函数，同时不需要考虑大数问题。

**示例 1:**

```
输入: 2.00000, 10
输出: 1024.00000
```

**示例 2:**

```
输入: 2.10000, 3
输出: 9.26100
```

**示例 3:**

```
输入: 2.00000, -2
输出: 0.25000
解释: 2-2 = 1/22 = 1/4 = 0.25
```


说明:

- -100.0 < x < 100.0
- n 是 32 位有符号整数，其数值范围是 [−231, 231 − 1] 。

**解法**

在书中，次方数是非负数，而本题中不是，所以不能使用位运算

当 n 为奇数时 $a^n = a^{n/2} * a^{n/2} * a$，

当 n 为偶数时 $a^n = a^{n/2} * a^{n/2}$

```java
class Solution {
    public double myPow(double x, int n) {
        if (n == 0) return 1;
        if (n == 1) return x;
        if (n == -1) return 1 / x;
        double a = myPow(x, n / 2);
        double mod = myPow(x, n % 2);
        return a * a * mod;
    }
}
```

快速幂的迭代做法：

```java
class Solution {
    double quickMul(double x, long N) {
        double ans = 1.0;
        // 贡献的初始值为 x
        double x_contribute = x;
        // 在对 N 进行二进制拆分的同时计算答案
        while (N > 0) {
            if (N % 2 == 1) {
                // 如果 N 二进制表示的最低位为 1，那么需要计入贡献
                ans *= x_contribute;
            }
            // 将贡献不断地平方
            x_contribute *= x_contribute;
            // 舍弃 N 二进制表示的最低位，这样我们每次只要判断最低位即可
            N /= 2;
        }
        return ans;
    }

    public double myPow(double x, int n) {
        long N = n;
        return N >= 0 ? quickMul(x, N) : 1.0 / quickMul(x, -N);
    }
}
```

## 17 打印从1到最大的n位数

输入数字 n，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。

**示例 1:**

```
输入: n = 1
输出: [1,2,3,4,5,6,7,8,9]
```

**说明：**

- 用返回一个整数列表来代替打印
- n 为正整数

**解法：**

因为题目中返回的是int数组，所以n肯定不会大于10

```java
class Solution {
    public int[] printNumbers(int n) {
        int rightRange = (int)Math.pow(10, n);
        int[] res = new int[rightRange - 1];
        for (int i = 1; i < rightRange; i++) {
            res[i - 1] = i;
        }
        return res;
    }
}
```

但实际上，n可能会大于10甚至更大，所以用字符串解决才是正确的方法，用字符串模拟加一

更好的方法是把它看作是一个全排列问题，每一位都可以是 0 ~ 9 中的任意数字，排列 n 位，用回溯法，最前面的0不输出。