# 周赛

## contest 178

### 有多少小于当前数字的数字

给你一个数组 `nums`，对于其中每个元素 `nums[i]`，请你统计数组中比它小的所有数字的数目。

换而言之，对于每个 `nums[i]` 你必须计算出有效的 j 的数量，其中 j 满足 `j != i` 且 `nums[j] < nums[i]` 。

以数组形式返回答案。

**示例 1：**

```
输入：nums = [8,1,2,2,3]
输出：[4,0,1,1,3]
解释： 
对于 nums[0]=8 存在四个比它小的数字：（1，2，2 和 3）。 
对于 nums[1]=1 不存在比它小的数字。
对于 nums[2]=2 存在一个比它小的数字：（1）。 
对于 nums[3]=2 存在一个比它小的数字：（1）。 
对于 nums[4]=3 存在三个比它小的数字：（1，2 和 2）。
```

**示例 2：**

```
输入：nums = [6,5,4,8]
输出：[2,1,0,3]
```

**示例 3：**

```
输入：nums = [7,7,7,7]
输出：[0,0,0,0]
```

**提示：**

- 2 <= nums.length <= 500
- 0 <= nums[i] <= 100

**解法：**

先排序，再使用HashMap，key是一个数字，value是这个数组在排序数组中第一次出现的下标。遍历原数组查表即可。

```java
class Solution {
    public int[] smallerNumbersThanCurrent(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        // 复制数组
        int[] copy = Arrays.copyOf(nums, nums.length);
        Arrays.sort(copy);	// 排序
        // 存Map
        for (int i = 0; i < copy.length; i++) {
            if (map.containsKey(copy[i])) continue;
            map.put(copy[i], i);
        }
        // 取结果
        int[] res = new int[nums.length];
        for (int j = 0; j < nums.length; j++){
            res[j] = map.get(nums[j]);
        }
        return res;
    }
}
```

### 通过投票对团队排名

现在有一个特殊的排名系统，依据参赛团队在投票人心中的次序进行排名，每个投票者都需要按从高到低的顺序对参与排名的所有团队进行排位。

排名规则如下：

- 参赛团队的排名次序依照其所获「排位第一」的票的多少决定。如果存在多个团队并列的情况，将继续考虑其「排位第二」的票的数量。以此类推，直到不再存在并列的情况。
- 如果在考虑完所有投票情况后仍然出现并列现象，则根据团队字母的字母顺序进行排名。

给你一个字符串数组 `votes` 代表全体投票者给出的排位情况，请你根据上述排名规则对所有参赛团队进行排名。

请你返回能表示按排名系统 排序后 的所有团队排名的字符串。

**示例 1：**

```
输入：votes = ["ABC","ACB","ABC","ACB","ACB"]
输出："ACB"
解释：A 队获得五票「排位第一」，没有其他队获得「排位第一」，所以 A 队排名第一。
B 队获得两票「排位第二」，三票「排位第三」。
C 队获得三票「排位第二」，两票「排位第三」。
由于 C 队「排位第二」的票数较多，所以 C 队排第二，B 队排第三。
```

**示例 2：**

```
输入：votes = ["WXYZ","XYZW"]
输出："XWYZ"
解释：X 队在并列僵局打破后成为排名第一的团队。X 队和 W 队的「排位第一」票数一样，但是 X 队有一票「排位第二」，而 W 没有获得「排位第二」。 
```

**示例 3：**

```
输入：votes = ["ZMNAGUEDSJYLBOPHRQICWFXTVK"]
输出："ZMNAGUEDSJYLBOPHRQICWFXTVK"
解释：只有一个投票者，所以排名完全按照他的意愿。
```

**示例 4：**

```
输入：votes = ["BCA","CAB","CBA","ABC","ACB","BAC"]
输出："ABC"
解释： 
A 队获得两票「排位第一」，两票「排位第二」，两票「排位第三」。
B 队获得两票「排位第一」，两票「排位第二」，两票「排位第三」。
C 队获得两票「排位第一」，两票「排位第二」，两票「排位第三」。
完全并列，所以我们需要按照字母升序排名。
```

**示例 5：**

```
输入：votes = ["M","M","M","M"]
输出："M"
解释：只有 M 队参赛，所以它排名第一。
```

**提示：**

- 1 <= votes.length <= 1000
- 1 <= votes[i].length <= 26
- votes[i].length == votes[j].length for 0 <= i, j < votes.length
- votes[i][j] 是英文 大写 字母
- votes[i] 中的所有字母都是唯一的
- votes[0] 中出现的所有字母 同样也 出现在 votes[j] 中，其中 1 <= j < votes.length

**解法:**

1.先打表
因为最多只有26个字母，所以可以先打一张二维表，横轴有26格，对应26个字母，纵轴对应排名，一共有几个字母纵轴就有几格。而表格里就填某字母得到某名词的次数，比如示例1：["ABC","ACB","ABC","ACB","ACB"]，打表就是
	  A	B	C
1	5	0	0
2	0	2	3
3	0	3	2
意思是A得了5次第一，B得了2次第二3次第三，C得了3次第二2次第三，那么最终排名就是ACB。

再使用递归找第一
先实现一个小目标，先不排序，先把第一名找出来。方法是看谁得第一的次数多，如果多个字母得了相同多次第一，就比这几个字母谁得第二的次数多，依次类推，因为最多有26个字母，我不可能写26层循环，那就使用递归。对应代码中的findMost函数，参数nums就是我们打的表，level就是我们在第几层（比得第几的次数），而idxs就是哪一列需要比。

最后用循环出结果
我们会找第一了，怎么排序呢？把第一归零了，第二不就是第一了嘛！所以我们用一个set存储所有参赛的字母，每找出一次第一就把表格中这个字母的竖列清0，然后把这个字母移出set，这样set存的就是没排过序的字母，正好给上一步的递归函数做参数。比如我们认为A是第一，那么把A的竖列清0，表就变成了：
\ A B C
1 0 0 0
2 0 2 3
3 0 3 2
然后C是新第一，C的列清0，变成：
\ A B C
1 0 0 0
2 0 2 0
3 0 3 0
然后B是新第一，那么顺序就是ACB

```java
class Solution {
    public String rankTeams(String[] votes) {
        int num = votes[0].length();
        // 打表，横26个字母，竖排名。
        int[][] res = new int[num][26];
        for (int i = 0; i < num; i++) {
            for (String v : votes) {
                int idx = v.charAt(i) - 'A';
                res[i][idx] += 1;
            }
        }
        // 用一个set存还没排名的下标(字母)
        TreeSet<Integer> idxs = new TreeSet<>();
        for (int i = 0; i < votes[0].length(); i++)
            idxs.add(votes[0].charAt(i) - 'A');

        // 每次都找第一，然后删除该字母的排名，删除set中的下标，循环
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < num; i++) {
            int idx = findMost(res, 0, idxs);
            for (int j = 0; j < num; j++) res[j][idx] = 0;
            idxs.remove(idx);
            sb.append((char) (idx + 'A'));
        }
        return sb.toString();
    }

    // 层层深入的递归，找当前排第一的字母。
    private int findMost(int[][] nums, int level, TreeSet<Integer> idxs) {
        if (idxs.size() == 1 || level == nums.length) return idxs.first();
        int maxi = idxs.first();
        for (int i : idxs) {
            if (nums[level][i] > nums[level][maxi]) maxi = i;
        }

        TreeSet<Integer> set = new TreeSet<>();
        for (int i : idxs) {
            if (nums[level][i] == nums[level][maxi])
                set.add(i);
        }
        return findMost(nums, level + 1, set);
    }
}
```

### 二叉树中的列表

给你一棵以 `root` 为根的二叉树和一个 `head` 为第一个节点的链表。

如果在二叉树中，存在一条一直向下的路径，且每个点的数值恰好一一对应以 `head` 为首的链表中每个节点的值，那么请你返回 `True` ，否则返回 `False` 。

一直向下的路径的意思是：从树中某个节点开始，一直连续向下的路径。

**示例：**

![](E:/kejian/知识总结/img/32.png)

```
输入：head = [4,2,8], root = [1,4,4,null,2,2,null,1,null,6,8,null,null,null,null,1,3]
输出：true
解释：树中蓝色的节点构成了与链表对应的子路径。
```

**提示：**

- 二叉树和链表中的每个节点的值都满足 1 <= node.val <= 100 。
- 链表包含的节点数目在 1 到 100 之间。
- 二叉树包含的节点数目在 1 到 2500 之间。

**解法：**

使用两个递归，一个递归判断子树上是否存在链表，另一个递归对每个结点（每棵子树）判断前一个递归。

```java
class Solution {
	// 判断是否有一个结点开始的子树包含链表
    public boolean isSubPath(ListNode head, TreeNode root) {
        if(root == null) return false;
        return recursion(head, root) || isSubPath(head, root.left) || isSubPath(head, root.right);
    }
    
	// 判断一棵子树是否从根节点开始包含链表
    private boolean recursion(ListNode ln, TreeNode tn) {
        if (ln == null) return true;
        if (tn == null) return false;
        if (ln.val == tn.val)
            return recursion(ln.next, tn.left) || recursion(ln.next, tn.right);
        return false;
    }
}
```

### 使网格图至少有一条有效路径的最小代价

给你一个 `m x n` 的网格图 `grid` 。 `grid` 中每个格子都有一个数字，对应着从该格子出发下一步走的方向。 `grid[i] [j]` 中的数字可能为以下几种情况：

- 1 ，下一步往右走，也就是你会从 grid[i] [j] 走到 grid[i] [j + 1]
- 2 ，下一步往左走，也就是你会从 grid[i] [j] 走到 grid[i] [j - 1]
- 3 ，下一步往下走，也就是你会从 grid[i] [j] 走到 grid[i + 1] [j]
- 4 ，下一步往上走，也就是你会从 grid[i] [j] 走到 grid[i - 1] [j]

注意网格图中可能会有 无效数字 ，因为它们可能指向 grid 以外的区域。

一开始，你会从最左上角的格子 (0,0) 出发。我们定义一条 有效路径 为从格子 (0,0) 出发，每一步都顺着数字对应方向走，最终在最右下角的格子 (m - 1, n - 1) 结束的路径。有效路径 不需要是最短路径 。

你可以花费 cost = 1 的代价修改一个格子中的数字，但每个格子中的数字 只能修改一次 。

请你返回让网格图至少有一条有效路径的最小代价。

**示例 1：**

![](img/33.png)

```
输入：grid = [[1,1,1,1],[2,2,2,2],[1,1,1,1],[2,2,2,2]]
输出：3
解释：你将从点 (0, 0) 出发。
到达 (3, 3) 的路径为： (0, 0) --> (0, 1) --> (0, 2) --> (0, 3) 花费代价 cost = 1 使方向向下 --> (1, 3) --> (1, 2) --> (1, 1) --> (1, 0) 花费代价 cost = 1 使方向向下 --> (2, 0) --> (2, 1) --> (2, 2) --> (2, 3) 花费代价 cost = 1 使方向向下 --> (3, 3)
总花费为 cost = 3.
```

**示例 2：**

![](img/34.png)

```
输入：grid = [[1,1,3],[3,2,2],[1,1,4]]
输出：0
解释：不修改任何数字你就可以从 (0, 0) 到达 (2, 2) 。
```

**解法：**

BFS+记忆搜索
这题是求最短距离的变种，按最短距离的bfs解法来写。
在这题中求的最小cost可以当作最短距离，只是这个cost的算法不太一样，当我们
使用bfs时向上下左右四个方向扩展，向网络所指方向扩展则cost不变，往其他方向
则cost+1，遍历过程中使用二维数组dst来保存由(0, 0)到其他网格的最小花费。

```java
class Solution {
    public int minCost(int[][] grid) {
        int n = grid.length;
        int m = grid[0].length;
        // 从(0, 0)到其他网格的最小花费，为-1表示未计算
        int[][] dst = new int[n][m];
        int[][] d = {{}, {0, 1}, {0, -1}, {1, 0}, {-1, 0}};// 方向
        for (int i = 0; i < n; i++) Arrays.fill(dst[i], -1);

        // dfs
        Queue<int[]> queue = new LinkedList<>();
        queue.offer(new int[]{0, 0, 0});    // 纵坐标 横坐标 当前cost
        while (!queue.isEmpty()) {
            int x = queue.size();
            for (int i = 0; i < x; i++) {
                int[] q = queue.poll();
                if (q[0] == n - 1 && q[1] == m - 1) continue;

                int val = grid[q[0]][q[1]];
                for (int j = 1; j <= 4; j++) {
                    int r = q[0] + d[j][0];
                    int c = q[1] + d[j][1];
                    if (r >= 0 && c >= 0 && r < n && c < m) {
                        // j代表当前的前进方向，val是格子上的方向
                        int add = j == val ? 0 : 1; // 方向不一致就cost++
                        if (dst[r][c] == -1 || dst[r][c] > q[2] + add) {
                            dst[r][c] = q[2] + add;
                            queue.offer(new int[]{r, c, dst[r][c]});
                        }
                    }
                }
            }
        }
        return Math.max(0, dst[n - 1][m - 1]); // 对初值-1特殊处理。
    }
}
```

