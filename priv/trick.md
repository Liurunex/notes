---
title: Trick Part-1
---
> **ToDo**
> 1. Resume project Impact
> 2. 理解题目和方法选择
> 3. 表述重复
> 4. 结合例子讲，防止抽象，沟通问题
> 5. 方法的trade-off, 直接说结论
> 6. 优化提一嘴


## BFS
> - BFS: 一般找最短路径/step，没有**显式TreeNode**需要**自己定义寻找state**，比如773,847
> - **Bidirectional BFS** 必须同时知道start && end，然后从两者同时bfs; **优化** 每次处理size小的queue(set)
> - 有时候需要priority_queue 替代queue， `Dijkstra`
> - 有时候要自己set search boundary，针对infinity
> - **DFS on BFS**: *next adjacent node* 不一定是i相邻点，而是一连串`cost/weight=0`的点，用DFS找出，如1368
> - **BFS with pruning** bfs是否需要logical thinking来pruning
> - **BFS 自定义 visited state** 类型

- **127. Word Ladder** [==BFS==]: word转换的问题尝试convert to graphic problem，使用Shortest Path Algorithm，变化次数就是edge cost；查找word可以从1～26 char入手，constant `O(26)`
- **126. Word Ladder II** [==BFS + MUST==] 127加强版， **BFS on path**: bfs queue里存上一round的path, visited里记last level visited word；**KEY** 需要记录每一level visited words，这些word**在本level其他path可以用，但不能在之后的level用，因为BFS处理cycle** 👉 level travel 时候先 remove visited words 然后visited words list清空给当前level用
    > - **NOTE** 需要记录found or minLevel来标记是否已找到最小path的level
    > - **坑** Why not 在bfs pop 后insert word into visited，而要在bfs level traversal 外层remove last-level visited? **答** **上一level用过的word新level一概不可用**，若pop后 insert visited，回漏掉其他的上一level用过的，出现cycle
- **301. Remove Invalid Parentheses** [==BFS==] 类似126，标准BFS level traversal，即对每个`(,)`考虑remove，判断是否visited，入queue；**KEY** queue pop后validate当前是否valid，若是 👉 已找到min-remove level，此level剩下candidate只validate
- **286. Walls and Gates** [==BFS==] **多点BFS**，对每个gate进行BFS update graph太慢了，此处将所有gate入queue，同步进行经典**level traversal**，可看作一种**n-way BFS**
    > - **NOTE** no need visited map, directly check value size
- **417. Pacific Atlantic Water Flow** [==BFS/DFS==] **逆向**，让边界逆向流向内陆，所以起点就是边界，常规两次BFS，最后找出两次BFS均true的点
    > - **KEY** 相当于逆向流动，而且由此不需要visited map
- 515. Find Largest Value in Each Tree Row [==BFS==] 标准BFS level travel
- 529. Minesweeper [==BFS==] 标准BFS，只要Empty node继续，统计8方向mine，若无且neighbor是E，入queue
- **490. The Maze** [==BFS==] 基本BFS框架，**注意**：移动时候要移动一个方向移动到底；可以直接修改matrix当visited map
- **778. Swim in Rising Water** [==BFS==] **heap版本BFS**，因为高度限制，每次处理queue中height最小的，相当于：在此height下可以到达的最远处；类似Dijkstra思路（其实**MST**）, **pq解法**不用判断高度，因为高度是distinct
    > - **Binary Search** 经典BS on solution set，选对mid，即水高，validate是否此水平面可以BFS到达终点即可，validation是经典BFS+判断 `mid>=grid[i][j]`
- **752. Open the Lock** [==BFS + MUST==] **BFS** 状态转移：一个code一步可以衍生出8个code，此为 8-way tree. 遇到dead-code就是Leaf，同时注意visited
    > - **Bi-BFS** 优化，同时从start和target出发，当遍历相遇即结束；各自maintain一个queue，每次处理其中一个；这里**queue实际为set**，end condition就是：**没处理的set中有包含当前处理的code**
    > - **Bi-BFS** 再优化，每次处理**size 小的那个queue（set）**，节约space
- **773. Sliding Puzzle** [==BFS + MUST==] 求minimum steps，锁定BFS，**转换问题** 以整个board为单一node来BFS，其child就是0与其neighbor swap后board
    > - **NOTE1** 2-D index convert to 1-D index and convert back
    > - **NOTE2** 常规BFS，BFS内部：先找到当前0 👉 convert 1d-index to 2d index 👉get neighbor‘s 2d index 👉 convert 2d-index to 1d-index 👉**check visited map** 👉next iteration until found
    > - **Bi-BFS 可优化**
- **815. Bus Routes** [==BFS SP==] **BFS + DFS扩展单点**；此处bus route 是大node，stop是小node；**从小node，其相连的大node，遍历此大node里面的小node**
    > - **准备**：首先需要构建映射`stop-->bus route`；queue里面存bfs的stop，**visited存seen bus route**；
    > - **过程**：每次取出cur-stop，看此stop的所有bus route，跳过当前bus route， 对于一组新的bus route，push其所有stop into queue
    > - **坑** avoid TLE，return res on check next，不要check current **因为大case存在queue里存太多**
- **864. Shortest Path to Get All Keys** [==BFS SP==] 复刻1293, BFS框架一摸一样，而且visited判读条件更宽松
    > - **tricky** 记录key的状态用bitmap思路，总key状态`1<<key_count-1`, 判读key `key>>(c-'A')&1 == 0`, 加入新的key `k |= 1<<(c-'a)`
- *1034. Coloring A Border* [==BFS==] 典型BFS，需要标注boundary
    > - **坑** 使用`x*m+y`在visited map里面要判断x，y是否在范围，否则值无效
- **1293. Shortest Path in a Grid with Obstacles Elimination** [==BFS SP + MUST ==] 经典BFS，容易TLE.
    > - **queue** 里记录多一维度：K剩余次数，其等于第三坐标
    > - **KEY** greedy思路, 关键在于**visited**优化(**类似Dijkstra的dist**),：visited里面记录是**从此点开始的max剩余K**，即对于点[i][j], 当新的`K <= visited[i][j]`， 没有必要再对其bfs，之前已经记录过**K更大即更优**情况
    > - 使用emplace优化push，但 `O(n*m*k)不变`
- **909. Snakes and Ladders** [==BFS + MUST==] 标准BFS的**level traversal**， 选择是next 6 steps，当有ladder时候要变化值，用des为visited指标
    > - **难点** *坐标转换* row简单`row=n-1-(des-1)/n`，因为是reverse走，所以要做减法，因为**des start at 1**，所以`des-1`
    > - **坑** *col坐标转换*，col坐标取决于：**n和所在row的parity**，不是单一指标，此处做转换，假设des从top-left开始，即`row=(des-1)/n`, 则n的parity不再考虑，因为第一行（odd start）是开始，永远是left->right.
- 934. Shortest Bridge [==BFS==] 先DFS找一个island，然后queue里存此岛，BFS扩张直到遇到一个1
- *994. Rotting Oranges* [==BFS==] 类似1020/733，常规**BFS level traversal**，记录fresh count
- *1091. Shortest Path in Binary Matrix* [==BFS SP==] DP难以处理子问题，八个方向；传统BFS即可
- **1197. Minimum Knight Moves** [==BFS==] 经典BFS，8方向.
    > - **KEY** 确定search范围，在确定 `min_x, max_x, min_y, max_y`时候，注意特例: **边界向外extend 两格**. **WHY** 因为knight走斜线，若target和start在一条线，那么boundary限制将错过solution，必须extend
    > - **引出boundary限制**
- **1162. As Far from Land as Possible** [==BFS==] 关键在于从**land出发标记**water距离，而不是从water出发；过程即经典bfs
    > - 注意用**BFS level traversal**，用step标记当前level的距离，这里用了greedy思路，**已标记过的water不再标记，ensure minimal distance**
    > - **坑**: BFS 算step因为没有找到target返回，最后step会+1表示last round; 最后返回前看step是不是initial 1，若是则没有water
- **1298. Maximum Candies You Can Get from Boxes** [==BFS==] 经典BFS
    > - 记录`seen keys` and `seen box without key`;
    > - `queue`里面维护`box can open`;
    > - 进行bfs时候，将key和box分开处理，处理key时候考虑可否找到key打开`seen box without key`
- *1311. Get Watched Videos by Your Friends* [==BFS==] 经典BFS
    > - **坑** visited最后在push前就检查，不然最后level可能有duplicate
    > - **如何根据frequency输出** 利用set是ordered
- **1284. Minimum Number of Flips to Convert Binary Matrix to Zero Matrix** [==BFS==] 773 复刻，**需要自己寻找node结构类型**，此处每个点都可以flip，所以每个node有n*m个children，巧用serialization和**index convention**
- **1215. Stepping Numbers** [==BFS/DFS BT==] **非典型BFS**，此处替换backtracking来**维护res order**. 思路即数字位数逐步增加，遍历所有case，注意用**long** avoid overflow
- *1490. Clone N-ary Tree* [==BFS/DFS==] 同步DFS/BFS即可
- **317. Shortest Distance from All Buildings** [==BFS + MUST==] 类似286，**标准BFS**，即从每个building出发，BFS填充dist vector，最后遍历一遍找出min；
    > - **坑** empty might not reach all building，如：`[11101,10101]`, 对每个empty考虑：`if 当前building visited` + building count `总共有多少building visited过`; **iff empty is visited by all buildings, update res**
    > - **Optimize** 每个building的BFS不用visited map，而是直接对原graph修改：`第一次BFS找0，第二次找-1...`，同时每次BFS都update res，要将res每次initial 为 MAX，其实只有最后一次BFS的res update 有效，但可以少一次mn遍历
- *1236. Web Crawler* [==BFS==] 标准BFS
- 339. Nested List Weight Sum [==DFS==] 弱化版364，直接DFS
- **341. Flatten Nested List Iterator** [==DFS==] flatten的意思就是从左到右去除nested，借用stack实现DFS
    > - **KEY** hashNext中要while 判断栈顶是不是integer，否则while push 之
    > - **坑** 由于stack又要maintain order，从后push
- **364. Nested List Weight Sum II** [==BFS/DFS==] DFS是先找depth，然后再带入depth dfs算res
    > - **BFS**， 常规BFS框架，**KEY** maintain `weighted`和`unweighted`，前者是算上depth的res，后者只是每一层level sum的累加；关键在于level traversal后每一层`unweighted` 不清零，这样下一层累加时候上一层的又加了一遍，契合题意
- **854. K-Similar Strings** [==BFS + MUST==] **BFS with pruning** naive bfs on choice肯定TLE. 考虑A和B是homographic &rarr; `A[i]==B[i]`的case不能够swap &rarr; BFS产生下一round candidates时候**wisely pick** 👉对于`swap(i,j)`, `i` is first index where `cur[i] != B[i]`，`j`是 first index where `cur[j] != B[j] && cur[j] == B[i]` (同形异构+wisely pick)
    > - **Greedy** 优先选择swap一次match两个的case[link](https://www.cnblogs.com/grandyang/p/11343552.html)
- **847. Shortest Path Visiting All Nodes** [==BFS==] **BFS with self-define state** 此处state不是简单的node visited or not, 而是**current visited state** `当前所有node visited state + 当前的node` 👉 其实是memorize 去重的思路;
    > - **KEY-1** BFS 允许走回头路，类似1293那样**自定义 state**
    > - **KEY-2** Encode state: 需要bit-mask来标记每个node state + 当前node 来encode
- *433. Minimum Genetic Mutation* [==BFS==] 127 复刻，标准BFS, 注意visited map就是bank，发现即delete **WHY** cycle will not generate optimal solution
- **1345. Jump Game IV** [==BFS==] 标准BFS level travesal
    > - **KEY** 优化corner case：array**大部分是一个val**；此情况下，算法复杂度劣化到`O(n^2)`, **WHY** 第一轮将遍历n，只排除一个visited pos，下一level的n-1的node，每个node依此遍历`n-2, n-3, ... 1`个点；
    > - **prune** 对于一个同val的index，只在**第一次遇到时候遍历**，如`[1,1,1,1,2,3]`, first time met 1, add index`1,2,3`, 下一level遇到1 at index 1，没有必要再去遍历`2,3` 👉 **第一次遍历后clear 同index的next position vector**


## DFS
- **98. Validate Binary Search Tree** [==DFS==]: top-down, 将left/right边界依次pass down, 传递时候替换为最新left/right， 只要node的左右子树满足，且自身和上面传递下来的left/right不冲突，就可以了；corner case INT_MAX/INT_MIN, 用long解决
- **333. Largest BST Subtree** [==DFS==]: **post-order**, 需要知道子树isBST后才能判断自身 &rarr; post-order；recursion**返回为BST的count**
    > - **KEY** 需要分别计算左右子树的left-right boundary；当左右返回`!0`，说明左右都是BST &rarr; 判断自身是否是BST &rarr; yes 则update res 以及**当前root形成的BST的left-right boundary**
- **133. Clone Graph** [==BFS/DFS==] 常规DFS/BFS，关键在于graph可以有环，如何知道已经copy完 👉 **hashmap to map source &rarr; copied**，若当前遍历点已经在hashmap，说明不用由此node dfs下去
- 200. Number of Islands [==DFS/BFS==]: 经典BFS/DFS，建立visited ma·p或者直接原图修改
- **694. Number of Distinct Islands** [==DFS/BFS==] 200加强，重点**serialize island**，这里除up,down,left,right, 必须加入back的动作，否则无法区分某些island
    > - 也可以用offset against src 来记录，string短
- **711. Number of Distinct Islands II** [==DFS+MATH==] dfs同694，关键是normalization
    > - **KEY** 对于一个点，可以rotate 90, 180, 270, 同时可以reflect 👉 八种变化，画图；集齐八个坐标集，各自排序然后normalize &rarr; 对**8个坐标集合** sort return first
    ``` c++
    rotate 0, 90, 180, 270: {x,y}, {-y,x}, {-x,-y}, {y,-x}
    reflect: {-x,y}, {x,-y}
    combine: {y,x}, {-y,-x}
    ```
- **329. Longest Increasing Path in a Matrix** [==DFS / Topology Sort==] 标准DFS+memorize，定义dp[i][j]：当前点出发能拿到点max len，recursive dfs
    > - Topology sort: 构造graph，对于每个点，与其周围大数有edge，得出in-degree 和 adjacent list，常规BFS做topology sort; **NOTE** 比**DFS mem** 慢得多
- 394. Decode String [==DFS==] recursion DFS, 遇到数字就count number然后call next level of recursion，跳过`[]`
- 690. Employee Importance [==DFS==] DFS+hash-tbl.
- **733. Flood Fill** [==DFS/BFS==]: **坑**newColor和old一样，直接返回, 否则会inf-loop
- 463. Island Perimeter [==DFS/BFS==] flood-fill扩展， 默认每个land+4，然后看up，left是否有land，有则-2
- *1254. Number of Closed Islands* [==DFS==] **FloodFill** boundary，then count number of island
- 841. Keys and Rooms [==DFS/BFS==]: 经典DFS/BFS + visited map 问题，每次取出当前room，加入keys前需要判断是否访问过，prevent loop
- 872. Leaf-Similar Trees [==DFS==] 两个root各自dfs求出leaf sequence
- *1020. Number of Enclaves* [==DFS/BFS==] **Flood fill** boundary相连的land，然后count enclaves
- *1222. Queens That Can Attack the King* [==DFS==] 从King出发，dfs八个方向找queue
- **1245. Tree Diameter** [==BFS/DFS==]
    > - BFS: twice BFS, 先找depth Leaf，又任何leaf出发做BFS找另一depth Leaf
    > - DFS: **post-order**, 类似543, 对node所以child，找其depth，取深度最大的俩update diameter
- *1291. Sequential Digits* [==DFS==] 暴力DFS
- *1376. Time Needed to Inform All Employees* [==DFS==] 经典top-down dfs，需要先建adjacent-list
    > - bottom up 做法， 类似union found想法 [link](https://leetcode.com/problems/time-needed-to-inform-all-employees/discuss/532560/JavaC%2B%2BPython-DFS)
- *1306. Jump Game III* [==DFS/BFS==] 标准DFS/BFS，需要visited map避loop，这里可以用*-1改变值
    > - **NOTE** 不是bt, 没必要，因为若之前访问某点没有return true，说明此点不可能
- **403. Frog Jump** [==DFS==] modified **DFS+memorize**, 起点`pos=0, jump=0`开始做dfs，dfs返回**能否到达终点**。每一round的dfs，遍历所有剩下的next-pos：
    > 1. `dist(pos, next) > jump+1` &rarr; 没法达到next，且之后`>next`的`next`也没法到达, 返回false, **WHY** 因为`[dist-1..dist+1]`会在3提前看过，3不返回true说明没法到达
    > 2. `dist(pos, next) < jump-1`，&rarr; 没法达到next，但且其后其他`>next`的`next-next`可能到达，continue
    > 3. `otherwise` 可以到达next，recursive call next round，当`next-recursion==true`，说明可以到达last node， return true.
    > - **Prune** 为了prune，引入memorize，此处key为`jump_pos + jump`，需要合成key
    > - **DP解法** 繁琐且慢，[link](https://www.cnblogs.com/grandyang/p/5888439.html)
- **282. Expression Add Operators** [==DFS==] 常规DFS **KEY** 处理乘法，假设现在前序`A-B && A-B=pre_val`, 选择当前做乘法，不能直接传递`pre_val * C`到下一层，因为其为`(A-B)*C instead of A-(B*C)`,  **FIX** 多记录**pre_num in recursion parameters**, 上述例子中`-B=pre_num` &rarr; `(pre_val-pre_num) + pre_num*C = A-B*C`
    > - **坑** 不能有`000`出现在当个number，只能保留`0`一个case，其余直接break
    > - **NOTE** 对第一个数字单独处理，因为其前不能+符号
    > - **坑** overflow，用long
- **756. Pyramid Transition Matrix** [==DFS==] 标准DFS，类似于nested list；hash-map mapping `pair-str -> end char`; 对每一组pair，遍历其所有possible case，dfs入下一层
    > - **KEY** maintain cur-level string and next-level string, 当cur-level走到头，**重置** cur-level为next-level，next-level清零, 继续DFS下一level
- **726. Number of Atoms** 类似calculator，**DFS** 注意，index必须pass by reference，因为之后dfs影响当前level的index位置；**用map的自动sort**
    > 1. 遇到`(` &rarr; dfs下一层, 由返回的dict更新此level的dict
    > 2. 遇到`)` &rarr; 计算数字，对当前dict进行update, 返回到上一level
    > 3. 到`)` &rarr; 先遍历求atom，然后int，update dict
    > - **KEY** 每一level有自己的**map**
- **1377. Frog Position After T Seconds** [==DFS/BFS==] **深度限制**DFS/BFS，此题假设了满足tree结构，但其实完全可以graph存在cycle结构，以下建立在Graph有cycle假设
    > - **KEY** edges要考虑双向，对Tree使用visited即可，对Graph使用unordered_set + erase
    > - **DFS** **终止** 深度到达或者没有next；**Prob** 每一轮算prob，就是`last_prob * 1/next_count`, 此处next_count 即区分tree和graph关键，由于graph可cycle，所以**next_count是可变的**, 利用set，在遍历next时候，**从next的 adj-list erase cur**
    > - **BFS** 类似DFS，利用Dijkstra的dist array, 可以求root到所有**终止点**prob, **NOTE** frog always jump when having next, 即当某node有next可走，此node的自己`prob -> 0`

### Backtracking
> - big O of Backtracking: O(recursion times) * O(recursion func)
> - 一般 `O(n!)` 纯brute-force
> - **memorize & prune** 一般prune是记录已知solution，类似dp的top-down，但也可以记录**已知无解**的case，跳过
> - [questions](https://leetcode.com/problems/permutations/discuss/18239/A-general-approach-to-backtracking-questions-in-Java-(Subsets-Permutations-Combination-Sum-Palindrome-Partioning))

- 17. Letter Combinations of a Phone Number [==Backtracking==]
- 22. Generate Parentheses [==Backtracking==] 经典bt，注意任何位置，左括号>=右括号
- **37. Sudoku Solver** [==Backtracking+MUST==] 经典bt，判读valid Sudoku等同36； big O 指数级
    > - **NOTE1** 先pre-traversal将已有的数字对应row， col， sub-square mark
    > - **NOTE2** bt先遍历col，当col==9，去next row
    > - **NOTE3** 找到一个solution立刻返回true，prune
    > - **NOTE4** 确定数字少，遍历次数少，因为少走回头路
- 39. Combination Sum [==Backtracking==]: 注意允许dup并且unlimited use, 同时为了prune，必须sort original vector，记得pass index 到下一层.
        > dp的做法，dp[i]: sum为i时候解集
- **40. Combination Sum II** [==Backtracking==]: 注意unordered_array里可有duplicate，必须排序后跳过相同的int，否则有重复.
- 216. Combination Sum III [==Backtracking==] 最简单一道bt
- **51. N-Queens I** [==Backtracking + MUST==] 经典bt问题，关键在于判读**当前放Queen的pos isValid**， 做法是判读其**左上，右上，列**方向是否有Queen了 （由于按照row，一层层放置queue，不用考虑*左下，右下*）
    > - 优化：可以只存queue的位置走，不用存整个board，这样isValid中：**check 两个Queen位置的 `abs(x-y) ?= abs(row-col)`** 即可，但其实没有优化big O
- 52. N-Queens II [==Backtracking==] 完全等同51
- **60. Permutation Sequence** [==BT/Math==] bt做法不可取
    > - Math找规律：从高位到低位观察，按照最高位分成n组，最高位每个数出现`(n-1)!`, recursively 看每一组的第二高位，每个第二高位出现`(n-2)!`; **Recursion思路** 寻找第`n`位数用`k/(n-1)!`，对剩下`k%(n-1)!`, recursively寻找`n-1`位
- 77. Combination [==Backtracking==] 注意dfs到下一层需要`i+1`
- 79. Word Search [==Backtracking==] classic DFS backtracking; 注意bt里先判断是否找完，在判断当前位置的合法性，以此应对2d-array `[['a']]` case.
- **212. Word Search II** [==Backtracking==] **Trie** to store all words, do dfs backtracking:
    > - **Note** 当`node->end == true`不能终止dfs， consider overlap case: `[“app”, “apple”]`
    > - normal brute force cost `O(n^2 * word_num * word_size)`, in Tire version, `O(n^2 * word_size)`
- 93. Restore IP Addresses [==Backtracking==] 注意考虑IPv4的合法性，01此类以0开头不可取
    > 其一：只要遇到字符串的子序列或配准问题首先考虑动态规划DP，其二:只要遇到需要求出所有可能情况首先考虑用递归
- 131. Palindrome Partitioning [==Backtracking==] typical bt，注意选择是sub-string length
    > - 对于每个index，看len=1,2,3...的sub-str是否palindrome
- 357. Count Numbers with Unique Digits [==Backtracking==] 经典bt，注意第一个数字不能0，单独处理
    > - Math: 通项`dp[i] = 9 * 9 * 8 * ... * (9-i+2)`
- 526. Beautiful Arrangement [==Backtracking==] typical bt，注意index从1开始
- 797. All Paths From Source to Target [==Backtracking==] 标准BT问题
- **996. Number of Squareful Arrays** [==Graph + Backtracking==] **转换为Graph**，这里能构成square的node之间有edge，据此构建**adjacent-list**
    > - **Backtracking** permutation的性质就是pos的val不同才是不同permutation;
        1. 放1st number: `adjacent-list`是hash-table，所以1st保证不会取duplicate
        2. 放2-Nth number: 如何保证之后dfs时候不会取duplicate？ 👉 maintain `freq list`，每次place a number，decrease its freq &rarr; 下次遇到相同数，若freq==0,则说明无可用，保证唯一性
- 980. Unique Paths III [==Backtracking==] 纯brute force bt，注意题目要求走完所有空位置，step inital为1，代表start point
- *1079. Letter Tile Possibilities* [==Backtracking==]: 注意visited map是以字母频率，访问一个char，`fq[char]--`，dfs到下一层，返回后补回`fq[char]`. 因为一个重复字符在一个位置只能访问一次，下一个位置依然可用其dup，所以不用set用`fq_vector`.
- *1087. Brace Expansion* [==Backtracking==] 典型BT，先遍历leading string，然后对`{}`进行拆分得option，dfs入下一层，**Note** 在遍历当前leading string后再加入res，否则错过trailing string case
- *1219. Path with Maximum Gold* [==Backtracking==]
- *1239. Maximum Length of a Concatenated String with Unique Characters* [==Backtracking==] use alphabet table/bitmap to indicate duplicates.
- **489. Robot Room Cleaner** [==Backtrack + MUST==] typical **BT**, 每一round dfs，选一个direction走一步，pass current direction
    > 1. **Blind** 不知道房间和位置，使用**relative position**，用hash—set来当visited-map;
    > 2. **Direction** `cur_dir = (pre_dir+I)%4`来决定移动方向，每次for loop end处要`turnRight`； **NOTE** 方向vector顺序不是随便的 + 方向也是相对的，以当前方向为基准
    > 3. **Backtrack** 走到底如何backtrack：先reverse direction，然后rollback pos，最后rollback direction.
    > 4. visited map 是用来看clean过的地方，不是visited， **rollback 时候不需要看visited map**
- **140. Word Break II** [==Backtracking + MUST==] Modified **bt** 可以用139 DP的做法，但是为了输出res可能会memory leak;
    > - **标准BT**，找出一个word，然后push into cur，下一层DFS，返回后pop word from cur，寻找下一个更长的word
    > - **Prune** 普通bt有duplicate case，要prune，用hash-map; 其可以为**`string -> split sub-str combinations`** &rarr; 太占空间
    > - **memory limit** 不同于一般memorize，此处记忆`[i..end]` sub-str是否可以split 👉 初始化任何位置可split 👉 dfs后会发现一些`[i]`不能split，标记为`mem[i]=0` 👉 此后遇下一层为`mem[i]=0`情况, skip 👉 **跳过已知不能split，远远好于记录已知解** (**HOW** 看res.size() 在进入dfs和返回后是否增加解)
- **291. Word Pattern II** [==Backtracking + MUST==] 290加强版，经典**Backtracking**，暴力：分case：bt中看pattern是否matched
    > - case pattern matched: 对当前str取 pattern size的word，看起是不是matched，是则继续dfs，否则false return
    > - case pattern not matched: 从当前pos遍历str，取出sub-str，看是否可用于matched pattern，是则dfs 下去，return回来要backtrack
- **351. Android Unlock Patterns** [==Backtracking==] *hard-code的backtracking*
    > - **KEY1** 对称性质，只需要看从`1,2,5`出发count即可，因为`1,3,7,9`和`2,4,6,8`各自等价
    > - **KEY2** hard-code jump vector, `jump[i][j]: i->j 中间需要jump的node`； 当jump node not empty，必须看jump node 是否visited，否则不能dfs下去
- **254. Factor Combinations** [==Backtracking==] bt方法常规，每次遍历`[start..target]`，`push - bt - pop`
    > - **DFS** 由于factor对称性，只需要遍历到`sqrt(target)`, **WHY copy a new vector based on current** 对 `target/i`，先dfs找其因数set的solution，再把`target/i`本身加入一个新solution，由于此中多个solution，所以需要每一个dfs copy 1个vector
- **679. 24 Game** [==Backtracking==] 基本BT，maintain 当前剩下的numbers，combination选两个number，做一种计算，replace old two with new value，dfs下一层，返回后replace new value with old two；`END: left only one number, check if it's 24`
    > - **KEY 2** why combination pick: 避免重复计算a+b, b+a etc, 对应`a-b, b-a, a/b, b/a`，新加入two operator来表示`b/a, b-a`
    > - **坑** 精度丢失，所以最后需要加入epsilon 0.001
- **1088. Confusing Number II** [==Backtracking==] 普通backtracking，很慢


#### Subset
- 46. Permutations [==Backtracking==] 三种解法
    > - 带visited map的bt，类似78
    > - 类似78，但是为了实现visited，所以固定好一个数后，将其与start_index swap，dfs后在swap back
    > - **插入空隙解法**: 比如[1,2,3]，已有 [1,2], [2,1]，则求permutation 就是在空隙里插入3: `[_1_2_] and [_2_1_]`,其iterative写起来麻烦，用recursion好一
- **47. Permutations II** [==Backtracking]
    > - **Hash-table** 用hash-table map value->freq, bt里面遍历hash-tbl instead of nums，避免duplicate问题处理
    > - **next permutation** 31加强，就是不断next permutation直到底，需要sort， `O(nlogn)`
- 78. Subsets [==Backtracking/BFS==] **time `O(2^n)`**
    > - 递归DFS/BT，经典做法，注意传index为下次for loop的起点
    > - 非递归BFS, iterative做法，不断根据已经生成的subsets加入新的val
- **90. Subsets II** [==BackTracking/BFS==] 基本和78一样
    > - 非递归，处理duplicate时候，只将dup加入上次产生的那些subsets，所以需要记录上一次start-pos
    > - 递归，在recursive遍历时候跳过与与[i-1]相等的数, 但不能跳过[index]，画个树理解.
- **784. Letter Case Permutation** [==Backtracking/BFS==] 类似78
    > - iterative-1: 初始化只有一个“”在permeation中，把当前一个字母的大小写都push into previous round的permutation中，极其类似78
    > - iterative-2: 初始化只有original-str在permutation中，遍历每一字母，然后copy previous round的permutations，toggle当前字母的大小写，入res
    > - recursive-1: [DFS](https://www.cnblogs.com/grandyang/p/9065702.html)


## Binary Search
> - [tutoral](https://www.cnblogs.com/grandyang/p/6854825.html)
> - **lower_bound** `(first >= target)` &rarr; `[mid]<target`; **upper_bound** `(first > target)` &rarr; `[mid]<=target` **针对duplicate** 类似`[lower_bound= AAAA < upper_bound]` 同时注意 **返回的upper_bound可能在末尾，需要判断是否--**
        > - 建立在写法`left<right && left=0, right=n-1`
        > - `lower_bound` convert to **find last i < target** &rarr; lower_bound结果--
        > - `upper_bound` convert to **find last i <= target** &rarr; upper_bound结果--
> - Why we always say `left=mid+1, right=mid`: mid计算的取left-boundary
> - **LIS**令人窒息的binary search优化
> - 2-D sorted array(vertical and horizontal): 用`O(m+b)`: 就是从top right pointer移动：遇0则down，遇1则left，这样直到index越界即可.
> - 有时需要自己组合**new search array**

- **GCI: find ceiling and flooring** [==Binary Search==]: `ceiling ==lower_bound first>=target`; `flooring: biggest number <= target`;
    > ceiling用lower_bound写法，**flooring用upper_bound写法+最后判读**
    ``` c++ {.line-numbers}
    int start = 0, end = arr.size() - 1;
    while (start < end) {
        int mid = start + (end - start) / 2;
        if (key >= arr[mid])
            start = mid + 1;
        else end = mid;
    }
    return (start>0 && arr[start] > key) ? --start:start;
    ```
- GCI: Minimum Difference Element [==Binary Search==]: 经典BS，就是去找key，找到最后没有就看left-right左右两边离key最近的那个
- **34. Find First and Last Position of Element in Sorted Array** [==Binary Search==]经典lower_bound+upper_bound， 做法就是做两次lower_bound, 第一次对target，只有找到了才做第二次对target+1的lower_bound, 返回`end--`
    > **坑**: 当`num[lower_bund] < target+1`说明num里没有比target大的数了，此时不可end--.
- 35. Search Insert Position [==Binary Search==] lower_bound
- **69. Sqrt(x)** [==Binary Search==] to find `last i <= target` **upper_bound**结果--，**坑** `x<1`情况直接返回，不然会越界到negative，同时不用用`m*m<=target`会 overflow
    > - **Newton法** `切线方程逼近` [link](https://www.cnblogs.com/AnnieKim/archive/2013/04/18/3028607.html)
- **74. Search a 2D Matrix** [==Binary Search==]经典lower_bound
    > - 一次Binary Search：将matrix看成`m*n`的array，在其上做BS，关键在于index转换，很巧妙，即用`i*j`**因为[mid/n][mid%n]即为所求点**, `O(logn+logm)=O(logmn)`
    > - 两次Binary Search：先对第一列做flooring得到target可能在的row，然后对row做lower_bound, `O(logmn)`
    > - O(m+n)做法就是378里binary search的condition func，从top-right or bottom-left开始
- **240. Search a 2D Matrix II** [==Binary Search==] 74加强版
    > - `O(m+n)`的方法依然适用，完全等同74的`O(m+n)`
    > - `O(logn!)`是走对角线，vertical和horizontal各自做bs，不高效
    > - `O(3mnlog4)` **time难算**divide&conquer, 把matrix按centric分成4部分，若target > centric, discard二象限，若target < centric, discard 四象限，recursive call [link](https://leetcode.com/problems/search-a-2d-matrix-ii/discuss/66147/*Java*-an-easy-to-understand-divide-and-conquer-method)
- 278. First Bad Version [==Binary Search==]: 典型lower_bound问题
- 153. Find Minimum in Rotated Sorted Array [==Binary Search==] 非典型lower_bound, 比较就是与两边boundary，只有`[mid]>[right]` 左移动
- **270. Closest Binary Search Tree Value** [==Binary Search==] 答案只能是root或者其子树上一点，利用BST特性遍历，然后update res
- **300. Longest Increasing Subsequence** [==DP/Binary Search + MUST==]: brute force DP 要点`dp[i]: the longest increasing sequence that end at nums[i]`;
    > **binary search**的优化.
    > - Why? &rarr; dp中包含了过多duplicate: `length=i`的increasing sequence(IS) 存在多个，不必要可优化.
    > - How? &rarr; 用single case代表 all`length=i`的IS, 考虑到brute force DP思路，maintain 一个 tails vector，**tails[i]=目前已知length=i+1的IS中minimum tail value**
    > - 做法: **init tails empty**, 遍历nums，对nums[i], find the lower bound in tails(Binary Search), replace it if found, otherwise append nums[i] to tails
    > - 分析1：**tails是natural有序的**，因为**tails[i]定义**, 可用Binary Search (lower_bound)
    > - 分析2: 对于num[i]: we know tails contains all known IS minimum tail value, we only need to look at the first value tail[k] satisfying `tails[i] >= nums[i]`, 一旦找到，说明我们可以用更小的nums[i]结尾构造`length=k`的IS，否则，我们可以用num[i]结尾构造第一个`length=tails.size()+1`的IS。
    > - 分析3: **Why must minimum tail value?** 如果不用最小tail来代表`length=i`的IS，我们之后将错过构造longer IS，举例：[1,5,2,3]走到最后的3，此时`length=2`IS tails = [2,5],如果不选用2，无法构造出`length=3`的IS[1,2,3], 不选用minimum使得我们无法构造最长IS as the previous non-minimal tails hide the info.
    > - **坑** 这里lower_bound用`right=size`而不是`right=size-1`，便于对upper overflow的数判断
- **334. Increasing Triplet Subsequence** [==Binary Search/DP==] 弱化版300，就是BS优化使用`tails-vector`记录各个size的IS的min tail. 只是此处k=3，所以BS可用`O(1)`直接比较替换。
    > - 不binary search，直接遍历一遍`O(n)`填充i,j,k
- **315. Count of Smaller Numbers After Self** [==Binary Search/Merge Sort==] Binary search就是用insert sort方法，reverse遍历插入每个num，然后看到当前插入的index即所求, `O(n^2)` 因为vector insert
    > - **Binary Search Tree 优化**，建立BST，同时每个node记录left—subtree-size, 所以需要不断update，思路和上面一样, 但插入是O(logn), 总时间`O(nlogn)`
    > - **MergeSort + MUST** [link](https://www.geeksforgeeks.org/counting-inversions/?ref=rp)
- **354. Russian Doll Envelopes** [==DP/Binary Search + MUST==] 300的加强版; 做法就是在width acceding下**求height的LIS**
    > - sort 原数组有讲究，Ascend on width and descend on height if width are same.
    > - **Why?** 想法是确保width有序，然后求height的**LIS**，但是[3,3] cannot contain [3,]，所以 same width下一定要height大的优先，排除干扰.
- 367. Valid Perfect Square [==Binary Search==]: 需要注意overflow，用long
- **436. Find Right Interval** [==Binary Search==] **自己构建search list + lower_bound**, 提出所有start，映射其index，然后对其sort，之后对每一个end，进行lower_bound on start-list
- **702. Search in a Sorted Array of Unknown Size** [==Binary Search==] 经典lower_bound, 关键在于求right，就是不断update left right until `[right]>target`，当然要**exponential increase window size**
- 441. Arranging Coins [==Binary Search==]: 经典upper_bound
- **528. Random Pick with Weight** [==Binary Search + MUST==] 巧妙在于如何按照weight 取random
    > - 分析: 对于w[1,3], 0是1/4几率，1是3/4，排序`[0,1,1,1]`, 发现是**按照weight sum/index freq sum的比例**，即`(0):一次：[1]; (1) 三次：[2,3,4]` &rarr; given pre_sum [1,4], 1是0，[2..4]是1
    > - Idea：算**pre_sum**，randomly取[1..weight_sum], 得到的random target, 👉 pre_sum中找target lower_bound, **转换为binary search**
    > - **follow up**: what if allow to update weights frequently? **Binary Index Tree/Segment Tree**
- 704. Binary Search [==Binary Search==] lower_bound
- 744. Find Smallest Letter Greater Than Target [==Binary Search]： 经典upper_bound
- **911. Online Election** [==Binary Search + Data Design==] 需要拿到每个时刻的leader，所以借助hash-table记录time-leader,同时要保留time list；找leader用upper_bound, **注意upper_bound返回需要判断是否--**
    > - **KEY** 本质上类似528的想法，**range round to point**， 对于vote timestamp：`[1....4....12]`, leader只会在vote时刻发生改变，记下每个vote时刻的leader 👉 time range [1...3]的leader就是`leader[1]`, [4...11]的leader是`leader[4]`
- 981. Time Based Key-Value Store [==Binary Search + Data Design==] 极其相似911，就是用`u_map<key, map<time, value>>`, get时候就是对map进行upper_bound
    > - **坑** 先确认timestamp是否小于min time，此时要返回空，911中忽略了这个case
- **1146. Snapshot Array** [==Binary Search + Design==] 类似911; **KEY** 对每个index，只记录`set时候改变的<sid, val>`，即只记录**changed part**, 之后get时候对sid进行 **upper bound**即可
    > - **KEY** 本质是911复刻，每个index maintain 自己的increasing sid-value array
    > - **坑** snap_id, 初始化就是0, 之后snap时候先返回当前id，然后id++
- *1150. Check If a Number Is Majority Element in a Sorted Array* [==Binary Search==] 经典lower_bound,只需bs一次，然后看[l+n/2]是不是target
- **1064. Fixed Point** [==Binary Search==] 经典lower_bound, 关键在于**组合searched list**，直接在原array做bs很难，注意到index本身也是ascending，自然考虑新array`new[i]=arr[i]-i`, new也是ascending，找到第一个0.
- *1182. Shortest Distance to Target Color* [==Binary Search==] 经典lower_bound, 将color分组为sorted index array，每一query寻找lower_bound, shortest_path就是 `min(lower,lower-1)`；注意两个corner cases
- *1198. Find Smallest Common Element in All Rows* [==Binary Search==] 标准lower_bound, 以第一row为benchmark，对每一个val，遍历每一row do lower_bound，直到每一行都找到. `mnO(logm)`
    > - **Optimization**:  1. remember the position of an previously found element and use it as a starting point for the next search; 2. exit early if all elements in the row are smaller than value we search for.
    > - **O(mn)**  就是hash-tbl count
- *1237. Find Positive Integer Solution for a Given Equation* [==Binary Search==] O(m+n)做法就是378里的, 就是从top right pointer移动：遇0则down，遇1则left，这样直到index越界即可.
- *1351. Count Negative Numbers in a Sorted Matrix* [==Binary Search==]bs做法就是每row做bs然后count，`O(nlogm)`
    > 完全利用row/col sorted，就是378里binary search的condition func，就是从left-bottom元素开始遍历，`O(m+n)`.
- *1337. The K Weakest Rows in a Matrix* [==Binary Search+Heap==]就count 1时候用了lower_bound
- *1365. How Many Numbers Are Smaller Than the Current Number* [==Binary Search==] 常规BS or Bucket sort
- **50. Pow(x, n)** [==Binary Search + MUST==] **math binary search** 利用 `x^n = (x^(n/2)*x^(n/2))`，recursive实习`O(logn)`：即先求 `x^(n/2)`, 由已知half去求`x^n`, `base case n==0, res=1`
    > - 即**快速幕运算** [ref](https://mp.weixin.qq.com/s?__biz=MzA5MTM1NTc2Ng==&mid=2458321758&idx=1&sn=e92e7a72d245831c29acb3d2a29607da&scene=21#wechat_redirect)
    $pow(x, N) = x^N = (x^{2^0*BIN[0]})(x^{2^1*BIN[1]}) ... (x^{2^k*BIN[k]})$
    $pow(x, 6) = x^6 = (x^{2^0*0})(x^{2^1*1})(x^{2^2*1})$
    `BIN[k]` 就是N的二进制表达式 👉 利用**相邻项**$x^{2^k} * x^{2^k}= x^{x^{k+1}}$
    ```c++ {.line-numbers}
    while (n) {
        if (n&1) res *= x; // 只有当前BIN[k]为1，才会计入，因为此项是 x^(2^k*BIN[k])
        x *= x;  // 由 x^(2^k) -> x^(2^(k+1))，下一项base
        n >>= 1; // 右移到BIN下一位
    }
    ```
    > - **KEY1** N奇偶情况要分别处理，`even: x = half*half`, `odd` 则还需要乘上x，因为half*half不够
    > - **KEY2** 特别处理**N is negative** &rarr; 结合N奇偶: 在even时候返回`half*half`不变，而在odd时候返回`half*half*x` &rarr; **当negative n, 实际乘数是`1/x`** 👉 negative return `half*half*(1/x)`; **NOTE** 不能N negative沿用`half*half*x`，最后`1/RES`,会overflow
    > - **follow-up** iterative way, 思路一样，提前判断x是否取1/x
- **372. Super Pow** [==Binary Search==] 50加强
    > - **KEY1** 处理指数数组 $X^{123} = (X^{12})^{10}*X^3$ 👉 recursion处理：`p1 = mypow(X, last-digit), p2 = mypow(rest, 10)` rest就是递归调用
    > - **KEY2** mypow就是**快速幕**
    > - **坑** 要对base X mode M, avoid overflow
- **1428.Leftmost Column with at Least a One** [==Binary Search/Matrix 位移==]: 二分法就是对每一行做bs，update result，`O(mlogn)`; 对于`O(m+n)`，就是从top right pointer移动：遇0则down，遇1则left，这样直到index越界即可.
- *1268. Search Suggestions System* [==Binary Search==] Trie做法太复杂，用binary search；**WHY** sort wordList后，对每一个prefix做binary search，那么lower_bound 就是first suggest candidate，此处需要validate连续三个candidate是否以是当前prefix
    > - **Optimize** BS的left boundary可随着每一此lower_bound移动，因为前面的word不会成为之后prefix的suggestion
- **4. Median of Two Sorted Arrays** [==Divide&Conquer/Binary Search + MUST==] 两种做法 `O(log)` [ref](https://leetcode-cn.com/problems/median-of-two-sorted-arrays/solution/xiang-xi-tong-su-de-si-lu-fen-xi-duo-jie-fa-by-w-2/)
    > 1. **Divide & Conquer** recursive: `O(log(m+n))` **KEY** 实质arrays中找Kth，两个array各取前k，比较`A[k/2] <> B[k/2]`；若`A[k/2]<B[k/2]` &rarr; **drop `A[0...k/2]`**, recursive call next round；👉 **WHY** `A[k/2]`最多大于 `A[0..k/2-1]`和 `B[0..k/2-1]`  即 **k-2个数** &rarr; **`A[k/2]`至多是k-1th**,可以drop，**调整新的K和A的start index**, go next round;
    > - **KEY end case**
        1. 其中一个array走完了, 返回另一个的Kth
        2. `k==1` 返回`min(A[start], B[start])`
        3. `A[k/2] == B[k/2]` 融入判断中，drop谁都一样
    > 2. **Binary Search**
- **1545. Find Kth Bit in Nth Binary String** [==Binary Search==] 非经典BS，观察可知 `cur_len = 2*pre_len+1 = 2^n-1`, **divide case**，k在前半部，正中，后半部，其中last half要`inverse`
    > - **KEY** 所有digit都是有**最开始的0** or **正中的1**演化而来，此处要move back to find initial case，然后根据inverse的flag返回结果
    > - **Transfer** k在first half，直接move back to `n-1` case；k在last half, 需要convert k to its pos at `n-1`，此处其为后半部分倒数kth，同时要inverse；
    > - **Base** `n==1` 根据inverse返回基于`0`的结果；`k在正中` 根据inverse返回基于`1`的结果

### Unique Binary Search
> - 有unique func来判断左右移动
> - 没有特定target
> - do BS on value range
> - minimize maximum：**left-converge**，`mid=left+(right-left)/2`, `left=mid-1, right=mid`
> - maximum minimum: **right-converge**, `mid=left+(right-left+1)/2`, `left=mid, right=mid-1`

- **162. Find Peak Element** [==Binary Search==] **search without target** 需要注意一个特性：`[i] < [i+1]` &rarr; 右边一定有peak；反之左边. Why? 因为边界0和n-1的overflow neighbor都是`-inf`， 画个图就清楚了
    > - 关于[mid+1]会不会越界，**数学分析**绝不会，因为当`mid=n-1` &rarr; `left == right = n-1`此时已经跳出loop
- **410. Split Array Largest Sum** [==Binary Search + MUST==] 超经典*search on value with unique condition func* **Minimize &rarr; left-converge**即在值域上search，left是max val，right是sum，判断时候是去看arr可否按照`max-sub-sum == mid`分成m个，可以则继续*往左缩小right来找minimal*，否则往右走
    > - **坑** overflow
- **1231. Divide Chocolate** [==Binary Search + MUST==] **Maximize &rarr; right-converge**410对比：410是minimize maximum **left-converge**, 此题是maximize maximum **right-converge**；大体类似于410，但BS和validation的细节处理不同
    > - **KEY** right-converge VS left-converge; **validate true 不管左右移动，都要保留mid 为可能解，这也是 left-converge vs right-converge**
- *1552. Magnetic Force Between Two Balls* [==Binary Search==] **right-converge**
- **658. Find K Closest Elements** [==Binary Search/Two Pointer/Heap==] pq的做法就是维护一个size=k的pq，two pointer做法是bs找到closet number，然后inside-out left&right方向延展；此处讨论binary search
    > - 希望找到the start index of sub-array，用binary search
    > - 如何移动left和right：对于当前window [mid..mid+k-1]，x可以位于四个位置：得出基本结论，x离谁近往哪边走
    ```
    0 x...[mid]....[mid+k]...
    1 ....[mid]x...[mid+k]...
    2 ....[mid]...x[mid+k]...
    3 ....[mid]....[mid+k]..x
    ```
    > - **Key** 为何不用abs距离而是`x-[mid]<>[mid+k]-x`? 这完全是为了考虑`[mid]==[mid+k]`，用abs时候不知道具体怎么移动，需要比较`[mid]<>x`(参考case 0，3)，若非要用abs，则要把`[mid]==[mid+k]`的情况单列出来;
    > - **坑1** 为何比较[mid]和[mid+k]与x距离而不是[mid+k-1]？ 比较的原因是要决定是否right shift，此处[mid]和[mid+k-1]都是存在与candidate list的，而[mid+k]是右移后才会加入的，我们是考察若右移动，是否能得到closet value，所以必须和不在当前list的数比较
    > - **坑2** [mid+k]不会越界吗？ 不会因为初始`right<n-k`
- 852. Peak Index in a Mountain Array [==Binary Search==] **search without target** 弱化版162
- 875. Koko Eating Bananas [==Binary Search==] **Minimize &rarr; left-converge** same idea as `401.Split Array Largest Sum`， 验证时候巧用数学ceiling，直接得到在val下需要N天吃完，比较N和H来移动.
- **1011. Capacity To Ship Packages Within D Days** [==Binary Search==] **Minimize &rarr; left-converge** totally same as `410. Split Array Largest Sum`, 区别是确认initial value of left and right: must ensure the capacity can hold each single weight, otherwise make no sense.
- **1060. Missing Element in Sorted Array** [==Binary Search==] **有quick select**影子，经典search on value，missed的count，与k比较，lower_bound: 就是找到mid，看[0..mid]missed的count，与k比较，左右移动. 注意对于kth 在array之外情况提前判断；找[0..mid]missed count就是`range - count`
    > - **坑** 最后返回时候要用**ceiling**，[left-1]，因为left是`missed >= k`的情况, 类似**atMost** 问题
- **718. Maximum Length of Repeated Subarray** 常规做法DP LCS，这里用了binary search on value range，checker是利用ASCII直接转换数字到ASCII，利用hash-set记录substr A，然后比较substr B
    > - **坑** 为何right这里用闭区间会失败
- *1283. Find the Smallest Divisor Given a Threshold* [==Binary Search==]: 410 复刻
- *1300. Sum of Mutated Array Closest to Targe·t* [==Binary Search==]: 相似与Divide array into shortest sub array，关键在于binary search on solution set, 在解集上进行binary search
- **719. Find K-th Smallest Pair Distance** [==Binary Search==] 经典**bs on value**
    > - **validator** **sliding window atMost**, 固定start，移动end到第一个`distance > k`处，有`end-start-1`个，**关键**是对于下一个`start+1`, 不需要从头找`end+1`，直接接着之前的end，即two pointer

- *1482. Minimum Number of Days to Make m Bouquets* [==Binary Search==] same as 410

## DP
> - `Top-down` 带memorization的DFS recursion; `Bottom-up` 标准DP: 确定transfer function，消除duplicate sub-problem
> - `原则`： 之前state已计算 + 终点即结果 &rarr; 决定遍历方向(左右上下斜)
> - `Process`: 转换问题 &rarr; 确定**state & choice** &rarr; 确定**dp definition** &rarr; 确定**transfer function** based on choice &rarr; space optimization
> - [question_list1](https://leetcode.com/discuss/general-discussion/458695/dynamic-programming-patterns/544912)
> - [question_list2](https://leetcode.com/discuss/general-discussion/662866/dp-for-beginners-problems-patterns-sample-solutions)

- 53. Maximum Subarray [==DP==]: 经典DP，they call it **Kandane Algorithm**, dp[i]：以nums[i]结尾的最大continuous subarray sum. 空间优化就是去除dp array.
- **918. Maximum Sum Circular Subarray** [==DP==]: 加强版53；考虑两种情况:
    1. 若maxsum位于正常array中: `[.....max-array....]`
    2. 若maxsum位于头尾结合的array：`[....min-array....]`,试想拿出maxarray后，剩下的就是minarray，那就是53的来找min, 而max就是`sum-min`
    > **坑** 若array全是negative，则`sum==min`，此时需要直接返回case 1的max，不能取`max(sum-min, max)`
- 119. Pascal's Triangle II [==DP==] rolling array
- 152. Maximum Product Subarray: [==Array==] 53变化版, **坑点** 负数，要同时记录之前的max和min product.
- **338. Counting Bits** [==DP==]: 1D dp，规律是 `i is odd -> dp[i] = dp[i-1]+1; else dp[i] = dp[i>>1]`
- **368. Largest Divisible Subset** [==DP==] 标准DP由极值返回状态，`dp[i]: 以ith结尾的LDS`，需要先sort array；**`dp[i]`同时存极值和状态**，状态就是此点parent.
    > - **坑** 对i，要遍历[0..i-1]来找其max
- *1043. Partition Array for Maximum Sum* [==DP==] `dp[i]: 0..i能拿到max`
    > - **transfer**: 以i为end逐渐回退组建last sub-array，update `dp[i]= max(dp[i], dp[i-k] + cur_max*k, k = [1..K]`, 就是从i往回最多走k步组建新的k-array，看max值
- **1048. Longest String Chain** [==DP==] `dp[i]`: 以str i结尾的max；transfer: `dp[i] = max(dp[sub_str_i])`
    > - **KEY** dp table is **hash-tbl**
    > - 从长度小的str开始，方法就是对每一个str，看其所有去除一个char的substr的max
    > - 同时**sort 按照size**
- *1277. Count Square Submatrices with All Ones* [==DP==] 221变形，一毛一样
    > - **follow-up**: do it in constant space, 直接在原数组修改
- **1411. Number of Ways to Paint N × 3 Grid** [==DP==]: 经典一维dp，分pattern讨论，对i行可以是121或者123，推测i+1行，画图自推，得出即可[ref](https://leetcode.com/problems/number-of-ways-to-paint-n-3-grid/discuss/574923/JavaC%2B%2BPython-DP-O(1)-Space)
    ``` c++
    [0][123] = 6, [0][121] = 6
    // case
    [i+1](121) = [i](212, 232, 313; 213, 312)
    [i+1](123) = [i](212, 232; 231, 312)
    // sum up
    [i+1](121) = [i](121)*3 + [i](123)*2
    [i+1](123) = [i](121)*2 + [i](123)*2
    ```
    > - **坑** long overflow
    > ==此题还有 logN解法，参考 **935. Knight Dialer**==
- *1314. Matrix Block Sum* [==DP==] 其实是prefix-sum思路，注意越界问题
- **343. Integer Break** [==DP==] 普通dp[i]: i能达到的max product; transfer 要考虑单数情况: `dp[i]=max(dp[j], j)*max(dp[i-j], i-j)`，而非仅仅dp[j]*d[i-j]
    > - `O(n)`做法：观察1～10的拆分结果，从5开始，总是先拆得n个3，最后剩下2或4
    > - `O(n)`做法2：找规律，所以结果都是[1..6]的迭代来的，7是4的3倍，8是5的3倍，etc
    > - **Proof** 假设n可以拆分成k个实数（而非正整数）的和：n=km，那么相应的乘积是pow(m, k) = pow(n/k, k), 要maximize这个值，take log, f = k(ln(n)-ln(k)), then take derivative, f' = ln(n) - ln(k) - 1 = 0。所以optimal 的k = n/e，相应的m=e=2.7...。所以如果m是正整数的话，尽可能的向3 round up，剩下的2来凑。
- **238. Product of Array Except Self** [==DP==]: 关于只用一个vector解法，很简单，用两个int记录左右边product, 一次traversal，每一次更新res[i]和res[n-i-1]的值以及左右product.
- **361. Bomb Enemy** [==DP==] 强化238，对每row，col，分开使用238的**two pointer outside-in**来update dp[i][j]; `O(3mn)`
- 562. Longest Line of Consecutive One in Matrix [==DP==] **3d dp** 最后一维度为vertical，horizontal，diagonal，anti-diagonal，`O(m*n)`
- 304. Range Sum Query 2D - Immutable [==DP==] pre_sum 问题
- **689. Maximum Sum of 3 Non-Overlapping Subarrays** [==DP + MUST==] 二维DP， `dp[i][j]: [0..i]的sub-array中组成j个k-size sub-array的max-sum`，transfer `end at i: dp[i-k][j-1] + sum(i-k+1, i); not end at i: dp[i-1][j]`, 此处还需要记录具体的start-index array, 即每个node `pair<max-sum, list of start indexes>`非常占空间
    > - **Sliding Window Solution** `O(n)`，分成三组，left，mid，right; 提前算出**每个index位置**对应left-interval，right-interval的`k-size max sub-array`的start-index，然后遍历对mid进行iterate: 以当前mid index start build mid k-size array, 由**left[i],right[i+k]** 分别算出左右max-sub-array， 不断update res
        1. **left[i]**: `start index of (MAX left sub-array in [0..i])`; **right[i]**: `start index of (MAX right sub-array in [i, n-1])`
        2. **坑1** 求left时候iterate end index from left to right，求right时候iterate start index from right to left
        3. **坑2** 求right时候，update用`>=` instead of `>`， 因为题目所求lexicographic order，而求right时从后向前，必须使用`>=`来保证lexicographic
- **1477. Find Two Non-overlapping Sub-arrays Each With Target Sum** [==Hash Table==] 类似689做法
    > 1. pre-process left_min array, `left_min[i]: 以i位置往前min-size的target array`
    > 2. reverse process，算right-part的target array，然后结合`left_min[i]`update res
    > - **KEY** 为了避免overlap，reverse算的是**当前index之前的target-array**, 区别于left_min
- **1553. Minimum Number of Days to Eat N Oranges** [==DP==] **DFS+memorization** 普通DP回TLE，因为DP是iterate from [1..n]; 此处用DP+hash-tbl memory，这样只记录**meaningful** node
    > - **KEY1** `dp[n] = 1+min(n%2+minDays(n/2), n%3+minDays(n/3))` 为 **eat `n%2` one by one day**, 但对于**`n/2` 每天吃n/2，剩下n/2**
    > - **WHY** why correct：可能想万一n%2 or n%3 不是eat one per one day，这是不对的，n%2 是`[0,1]`, n%3 是`[0,1,2]`,  **case `[0,1,2]`都是eat one per day**
    > - **KEY2** 为了提速，不能先`dp[n] = minDays(n-1)+1`, 这本质上是iteration [1..n]，和dp bottom up没区别了，我们只关注`2k,3k`nodes，利用KEY1来提速，`O(logn)`
- **517. Super Washing Machines** [==DP==] **特别的DP** `dp[i]: 第i位置min操作`， 此处dp[i]之间没有相互关系，完全独立，没有transition
    > - **KEY** kth 位置, 计算**lCost**: 左边`[1..k-1]`需要`send-to/recv-from`右边`[k..n]`的衣服count； **rCost**: 右边`[k+1..n]`需要`send-to/recv-from`左边边`[1..k]`的衣服count; **lCost和rCost 决定在kth 位置的操作！**
    > - **3 case**: `lCost>0 && rCost>0`：kth需要两边同时send; `lCost<0 && rCost<0`：kth需要send-to两边，没法同时操作; `otherwise`: kth一边send一边receive
    > - **NOTE** 左右两边需要的衣服count是`avg*洗衣机个数`


### Knapsack DP
> - 完全背包 or 01背包
> - subset问题<>01背包
> - 空间优化时候**01背包**要考虑**inner loop reverse**, 缘由：求dp[i]时避免dp[k] update到new round
> - subset 01背包问题中注意数组包括0的corner case

- 322. Coin change [==DP==]: 经典**完全背包**，注意先遍历**coin后amount的解法**：相当于先用coin[i]update所有dp，在用coin[i+1]更新后==用coin[i, i+1]一起更新
        > - **leetcode的 test-case bias**：先遍历coin后amount速度翻番：'cause memory copy，因为coins的size远远小于amount的大小，我们应当尽量**保持outer loop小inner loop大，减少memory move**
- **518. Coin Change 2** [==DP==]: 经典**完全背包**，dp[i][j]: 用前i种coin凑j的总个数.
    > - **KEY** 此处为combination不是permutation
        - 2d dp中先遍历coin 或者 amount不影响结果，总是combination, **WHY** `dp[i][j]：前i种coin凑j`，此处transaction`dp[i][j] = dp[i-1][j-x]`已经是包含了coin先后关系，是combination
        - 1d dp中先遍历coin 或者 amount影响结果，**WHY** `dp[j]: 组成j的个数`，若先遍历amount，等于每一round随机选coin，这肯定是permutation，反之**先coin**，**等同于给coin排好序**，这样就是combination
    > - **滚动数组优化** 参考322的先coin后amount做法，考虑就是先用[i-1]coin去凑所以amount，然后用[i]coin update之前的值
    > - **NOTE** 这里优化不需要reverse inner loop，**WHY** 完全背包问题不指定item只能用一次，和0-1背包不同！所以不用考虑放在item时候item是否已经放过了.
- **416. Partition Equal Subset Sum** [==DP==]: 经典**0-1背包** 二维dp.
    > - 转换问题: 能否构成sum/2的subsequence.
    > - 定义`dp[i][j]`: 前i个num是否能构成和为j的subsequence
    > - transfer func：`dp[i][j] = dp[i-1][j] || dp[i][j-num[i-1]]`
    > - **滚动数组优化**：由于只依赖与上层结果，可优化到1d; **重点**: 优化后inner loop要reverse travel：`[sum..0]` **WHY** 由于`dp[i][j]`需要`dp[i][j-v]`，若正向走`dp[j-v]`会被overwrite before arriving at dp[j]； **换个角度**：每个number只能用一次，若正向走，number会被复用 [link](https://www.cnblogs.com/grandyang/p/5951422.html)
    > - **follow-up-1** *Partition the set into two subsets wit minimum difference.* dp问题不变，只是最后从`d[n][sum/2]..dp[n][0]`找到第一个true就是最小difference划分，即离`sum/2`越近的划分
    > **follow-up-2** *find count of subsets whose sum equal to S* dp[i][j]：用first i number构成sum=j的count，transfer: `dp[i][j]= dp[i-1][j]+ dp[i-1][j-val[i-1]]`
- **474. Ones and Zeroes** [==DP + MUST==] **0-1背包**, 用有限01组成最多string，`典型0-1背包，string的个数是val，string的01count是weight，背包是m1n0`，只是capacity是**2D**
    > - `dp[i][j]`: 用i个0j个1组成最多的string数量; transfer：`dp[i][j] = max(dp[i-zeros][j-ones]+1, dp[i][j])`
    > - dp本身已经space optimization，从str开始一一遍历，类似322 coin change的空间优化，所以i，j都reserve travel
- **494. Target Sum** [==DP==] **0-1背包** 416变形，转换题意：分正负两组，得求`sub=(target+sum)/2`的subset counts,和416一摸一样
    > **坑** 2d dp时候`0<= j <= sum`, 因为要考虑0；而1d时候`nums[i] <= j <= sum`，因为`[0..nums[i]-1]`的情况之前已计算过
- **1155. Number of Dice Rolls With Target Sum** [==DP==] **非完全背包** 复刻518, 但choice不是unlimited，而是`[1..f]`，等同于给每个种类coin加了**个数限制**，但此限制为一维度
- **1049. Last Stone Weight II** [==DP==] **01背包**， 就是416 **follow-up-1**

### Min/Maximum Path
- 64. Minimum Path Sum [==DP==] 经典二维, 两种空间优化：若直接更改grid，O(1) space. 讨论O(n) space，即优化到一维DP：
        > update dp[i]时候，dp[i-1]就是left值，dp[i]存上一轮遍历时候值，就是up，所以可以 `dp[i] = grid[i][j] + min(dp[i], dp[i-1])`
- 70. Climbing Stairs [==DP==] fibonacci array
- **174. Dungeon Game** [==DP+MUST==] 实质64, **reverse travel**，正向遍历会需要记录过程中最小值以及到达[i][j]的实际值，不方便计算
    > - **dp[i][j]**： initial health when start at [i][j];
    > - **transfer**: `dp[i][j] = max(1, min(dp[i][j+1], dp[i+1][j])-val[i][j])`
    > - 求minimum health，用**反向思维**，要保证最后存活，则到达[n][m]后health >= 1；**逆向**退回[0][0]起点
    > - 遇到**负数**，则说明`dp[i][j] - |val[i][j]| >= min(next start)`；
    > - 反之遇到**正数**，说明是加血，则只需要此处 `dp[i][j] + val[i][j] >= min(next start)`，其中若`val[i][j] > min(next start)` 👉 dp[i][j]只需要为1即可
- **120. Triangle** [==DP==] 64变形，经典**reverse** dp，最好bottom-up省去corner check, `dp[i][j]`： 从底到[i][j]的min cost; `transfer`: `dp[i][j] = min(dp[i+1][j], dp[i+1][j+1]) + val[i][j]`
    > - 空间优化：以triangle底的vector构建1d dp
- **221. Maximal Square** [==DP==]: 经典二维DP，dp[i][j]表示：以matrix[i][j]为右下角的最大square的边长，转移关系：`dp[i][j]=min(dp[i-1][j], dp[i-1][j-1], dp[i][j-1])`, 就是看相邻点的min值，这样才能保证组成square.
    > 空间优化到一维：需要单独记录斜对角的值，其实就是上一round: dp[j-1].每次要不断update `diag_pre`
- 279. Perfect Squares [==DP==] 一维dp，和983类似，在值域构建dp，transfer `dp[i] = min(dp[i-j*j]+1)`
    > - 数学方法 **Lagrange's Four Square theorem**
- 746. Min Cost Climbing Stairs [==DP==] `dp[i]:` 到达i的最小cost；**坑**此处要到**n+1**
- 931. Minimum Falling Path Sum [==DP==] 类似120，简单2d dp, 跳过第一行
- **983. Minimum Cost For Tickets** [==DP+MUST==] 一维dp，**dp可能建立在连续值上** 转换问题，记录全年365天的cost，dp[365], `dp[i]`: 到i天的总cost
    > - transfer func: 若i天不travel，dp[i]=dp[i-1], otherwise: `dp[i]=min(dp[i-1]+cost[1], dp[i-7]+cost[7], dp[i-30]+cost[30])`
    > - **需要一个set记录要travel日期**
    > - 由于最多30-day pass，所以可以用一个[30]的滚动数组优化空间
    > - **坑** `max(0,i-7/i-30)` 没有负数天
- **650. 2 Keys Keyboard** [==DP==]: 对dp[i], 一旦`j<i && i%j==0`, 即可以COPY j 'A', PASTE until i 'A's, 则就是找出所有i的factor，`dp[i] = min(dp[i], dp[j]+i/j, dp[i/j]+j`，其实可以证明`dp[j]+i/j==dp[i/j]+j`.
    - 若以为到J做了COPY后一直PASTE到i会疏漏情况，其实不然，试想先COPY j 'A's再PASTE util k (k>j)，又做COPY&PASTE，此情况dp[k]+i/k已经包含
    > 有math解法Prime Factorization， O(1) space
- **651. 4 Keys Keyboard** [==DP==]: `dp[i]: max print with i operations`, transfer: 要么only do print `dp[i]=i`, 要么do `[C-A,C-C,P]`, **j为C-A, j+2是C-V起点**: `dp[i] = dp[j] * (i-j-2+1)` `O(n^2)`
    > - **ANOTHER** **j是做C-C的地方，j+1是C-V起点** `dp[i] = max(dp[i], dp[j-2] * (i-j+1))`
    > - **坑** ACP+P..P 时候别忘了original， 所以`-2+1`
    > - **数学optimization** `O(n) and O(1)`

### Distinct Ways
- 91. Decode Ways [==DP==] dp[i]: [0..i-1]有多少decode ways；transfer：`dp[i]=dp[i-1] + ?(dp[i-2])`, 判当前单独decode和与i-1合并两种decode，注意invalid case
    > - **KEY** dp[0] = 1,为了dp[2]valid的情况；s.size() == 0 corner case单独处理
    > - **坑** corner case `10` on count dp[2]
- **96. Unique Binary Search Trees** [==DP==] `dp[i]: 有i个点时候BST个数`, transfer `dp[n] = dp[0][n-1]+dp[1][n-2]+...+dp[n-1][0]`
    > - 本质是[Catalan Number](https://en.wikipedia.org/wiki/Catalan_number)
    > - 分析：对于n，此时有[1..n]分别为root的case, 由于node val是[1..n], 对于每一root，考虑其左右子树情况，例如root=i，由于BST，那么左树有i-1个node, 共dp[i-1]种组合，同理右树
    ```
    root 1: left=dp[0]，right=dp[n-1],
    root 2: left=dp[1]，right=dp[n-2],
    root 3: left=dp[2]，right=dp[n-3],
    ...
    ```
- **95. Unique Binary Search Trees II** [==DP/Divide and Conquer==] 经典**Divide and Conquer** 以每个val为node，divide其左右树，recursive call **post-order**
    > - **DP** 用top-down DP，即divide & conquer + memorization，此处cache用3d，`mem[i][j]: i～j的所以BST集合`，也是dp[i][j]
- **377. Combination Sum IV** [==DP==] 此题其实是permutation sum，Coin Change 2才是combination. `dp[i]: 构成target i的permutation count`
    > - **区别coin change 2** coin change是combination是遍历有序的，其消除了permutation重叠，而此处permeation无序
    > - 举例coin change的有序消除permutation重叠：比如 [2,3]凑5，遍历`dp[2][target]`是只有2 coin，此时`dp[2][3]=0`, 那么`dp[2][5] = dp[2][3]`自然是0，要等到遍历`dp[3][target]`才行
    > - **followup** 加入负数会怎么样? 👉会造成infinity permutation like `[-1,1], target=1`. The limitation 是加入sequence长度限制
- **935. Knight Dialer** [==DP==] 常规2d DP，需要建立数字与其next的连接，`dp[N][i]: 第N round，total counts when end at i`，transfer `dp[N][i] = Sum(dp[N-1][i's connect nodes])`
    > - **矩阵乘法 Optimize** [link](https://zhanghuimeng.github.io/post/leetcode-935-knight-dialer/#fn1)
- **837. New 21 Game** [==DP + Sliding-Window + MUST==] 数学分析; 首先到K时候stop &rarr; final points range `[K, K+W-1]`; 定义 `dp[i]: probability on reaching i points`, 已知单步取一个x的概率 `p[x]===1/W, x = [1..W]`
    > - **case `i <= K`**: 没有K stop的限制，即可[1..W]任取一数，此操作`pb=p[x]*dp[i-x]`; 可看到此处重复取`W-size window` 👉 sliding window 思路，也是rolling hash那个路子，delete left-most old value，plus right-most new value
    ```
    dp[i] = dp[i-1]*p[1] + dp[i-2]*p[2] + ... + dp[i-W]*p[W]
          = Sum(dp[k])/W, where k=[i-W...i-1]
    ```
    > - **case `i >K`**: 有K stop限制，思路和上case一样，but **cannot reach i from >=K case** 👉 从dp[i-W]取到dp[K-1], 即**承接i==K的sliding window只减left不加right**
    ```
    dp[i] = dp[i-W]*p[W] + dp[i-W+1]*p[W-1] + ... + dp[K-1]*p[i-K+1]
          = Sum(dp[k])/W, where k=[K-1...i-W]
    ```
    > - **corner case** K==0 或者 N>=K+W-1，说明一定stop after K后一定<=N

### Merging Intervals
- **312. Burst Balloons** [==DP+MUST==] **重点转换问题，找到独立子问题** 👉 反向思考，考虑最后burst那个气球
    > - 两头需要加入虚拟气球，`dp[i][j]: 刺破[i+1, j-1]后可以得到的max`
    > - **transfer** 反向思考，当(i,j)中burst最后一个气球k，**选择**就是选k，`dp[i][j] = max(dp[i][k]+dp[k][j]+ p[i]*p[k]*p[j]`
    > - **决定遍历顺序**，画图观察transfer可知，必然是斜着走 or **bottom-up + left-right*
    > - **边界问题** dp[i][j]不戳破i, j

### Decision Making
> - Finite State Machine: 根据state来decide，画出state transfer diagram

- **198. House Robber** [==DP==] rob和no_rob两个state相互转换，单维dp[i]: [0..i]间符合规则能抢到的max value.
    ```dot {engine="dot"}
    digraph G {
        Yes [color=blue]
        No [color=blue]
        No -> Yes [label="rob", fontcolor=red, color=red]
        Yes -> No [label = "no rob"]
        No -> No [label = "no rob"]
    }
    ```
    > - **Note** `dp[i] = max(dp[i-1], dp[i-2]+nums[i])`, 若是dp[i-1]没有抢`i-1`, `dp[i-1]==dp[i-2]`.
    > - 两种空间优化，一是dp用pre, pre_pre; 另一种不用dp，用not_rob 和 rob两个int来保存上一轮情况，更新本轮rob和not_rob，最后return `max(rob, not_rob)`
- 213. House Robber II [==DP==] 198复刻
    > - 解决**circle**问题：**Partition来阻断首位相接** 把[0...n-1]分割成[0..n-2]和[1..n-1], 做两次house robber I
- **337. House Robber III** [==DP==] 变种198，因为是tree，用top-down memorization可以做，核心依然是**rob or not rob**，即based on status to make decision
    > - **BETTER** 不用memorization方法：为何要memorization，因为是top-down，会overlapping subproblem，那么考虑下能否**bottom-up**：**自底向上时候carry sub-tree values**，即children rob和not_rob的值
- 121. Best Time to Buy and Sell Stock [==DP==] 记录min buy so far，不断update res
    > - **state machine** k=1, 对transfer func无影响, k=1:
    >`dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])`
    >`dp[i][1] = max(dp[i-1][1], - prices[i]) // how k=1 works`
- 122. Best Time to Buy and Sell Stock II [==DP/Greedy==]: 不限制次数则低买高卖，遍历一遍累加所有positive differences.
    > - **state machine** k=+infinity，对transfer func无影响，k==k-1:
    > `dp[i][k][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])`
    > `dp[i][k][1] = max(dp[i-1][1], dp[i-1][0] - prices[i])`
- 309. Best Time to Buy and Sell Stock with Cool-down [==DP==] **state machine**, 在122 base上加上cooldown, 也就是buy时候看的i-2那天的no_stok:
    ```c++
    dp[i][0] = max(dp[i-1][0], dp[i-1][1]+prices[i-1]);
    dp[i][1] = max(dp[i-1][1], dp[i-2][0]-prices[i-1]); //i-2 to cooldown
    ```
- 714. Best Time to Buy and Sell Stock with Transaction Fee [==DP==] **state machine** 在122base加上fee，**注意fee放在buy时候avoid sell时候dp[0][1] overflow**
   ```c++
    dp[i][0] = max(dp[i-1][0], dp[i-1][1]+prices[i-1]);
    dp[i][1] = max(dp[i-1][1], dp[i-1][0]-prices[i-1]-fee);
    ```
- **123. Best Time to Buy and Sell Stock III** [==DP==] **state machine** 188弱化版，
    > - **空间优化** 由于k==2, 可以不用dp数组而是枚举优化，**order不重要** [==WHY==]
    > - [state machine explain](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/discuss/149383/Easy-DP-solution-using-state-machine-O(n)-time-complexity-O(1)-space-complexity)
- **188. Best Time to Buy and Sell Stock IV** [==DP==] 经典**state machine**，确定两个state：持有或者不持有stock，三个操作：buy，sell，reest，那么state transfer func
    ```c++
    dp[i][k][0] = max(dp[i-1][k][0], dp[i-1][k][1] + prices[i])
    dp[i][k][1] = max(dp[i-1][k][1], dp[i-1][k-1][0] - prices[i])
    ```
    ```dot {engine="dot"}
    digraph G {
        1 -> 1 [label = "reset"]
        0 -> 1 [label = "buy", fontcolor=blue, color=blue]
        1 -> 0 [label="sell", fontcolor=red, color=red]
        0 -> 0 [label = "reset"]
    }
    ```
    > - **坑1** 初始化`[0][k][1] = INT_MIN`, `[0][k][0] = 0`
    > - **坑2** 若`k >> prices.size()`，比如k是好几百万，而prices的天数个位数, DP解法没有效率，用122
- **740. Delete and Earn** [==DP==] 变形198，decision make，同时有**bucket sort**思路，在**值域进行dp**，把所有value放进bucket，这样自动排序，然后从低往高，dp思路就是：到当前value，take or drop当前value能拿到的max，就是198的dp.
- 256. Paint House [==DP==] **state machine** 分red，green，blue三个选择，类似198
- **265. Paint House II** [==DP==] 256强化，有k colors，直接套用256是`O(nkk)`
    > - **Optimize** `O(nk)`: 记录`ith` row的**last two min color**的index，这样对`i+1 row`, 可以`O(1)`time 找到min value，同时update 此level的last two mins
- 276. Paint Fence [==DP==] **state machine** 分paint_same, paint_diff 俩选择，类似198，不过此处求count instead of min-cost/max-value

### Game Theory DP
> - transfer func重点：**先手下完后变后手!**`dp`为两名玩家的**积分差**或者积分情况(pair)，站在先手角度计算
> - **Minimax**

- **877. Stone Game** [==DP/Brain==] 按照题目限制even number of piles 并且总piles is odd &rarr; 先手赢
    > - **follow-up** *piles number can be odd* 经典**Game DP**，dp[i][j]:`largest difference of stones first player can have than his rival` &rarr; transfer `dp[i][j] = max(piles[i]-dp[i+1][j], piles[j]-dp[i][j-1])` **斜向遍历DP**
    > - **WHY** 定义`K[i][j].first 为先手拿到max，K[i][j].second为后手的`；对于先手，`dp[i][j]=K[i][j].first-K[i][j].second`, 可以pick i或者j，**pick后其变后手**，即对于pick i的先手，其为后手在`dp[i+1][j]=(K[i+1][j].first-K[i+1][j].second)`
- **1140. Stone Game II** [==DP==] 此处`dp[i][m]: 先手在[i..n]和M=m情况下取到max point`，此处top-down DP: **dfs+memorization**
    > - **KEY-1** 需要pre-sum，因为可取前X个piles
    > - **KEY-2** dfs就是遍历 `x=[1..2*M]`, 此轮对当前选手：`earn=sum([start, ..., start+x])`, 已知left point `[start+x+1,...,n]`, 对手此后**总earn**是game DP核心swap：`opp_earn = dfs(start+x, new m)`，据此不断update 此轮res
    > - **KEY-3** hash-tbl memorization 去重
- **1406. Stone Game III** [==DP==] 经典**Minimax DP** 两种dp思路: `dp[i]: 取[i..0]，先手多于后手的points； dp[i]=pair<int, int>: 取[i..0], first为先手max-point，second为后手`
    > - **KEY** dp的initial val是`-inf`，因为此处允许stone val为negative
    > - **坑** dp需要reserve `n+1`空间，最后一个out-boundary是为了`i+k+1>=n`的case，`dp[n]`**initial val必须是0**，因为没法reach到
- *486. Predict the Winner* [==DP==] 877复刻，注意==0也算先手win
- **464. Can I Win** [==DFS==] **memorization DFS** 用top-down因为bottom-up不好找DP；mask记录每个int使用情况；memorization就是dict map `mask->res`来prune
    > - **KEY** DFS中遍历所以number，if not used，check是否used后到达target，or DFS进入下一层看返回值 **NOTE DFS进入下一层后先手变后手** 所以期待返回false才是本round先手胜利

### String DP
> - **LPS, LCS, LIS**; str处理**subsequence问题**：求longest sequence，一个str也有时需要2d dp
> - **LPS** 类要斜着或者反着遍历
> - [tutorial](https://leetcode.com/discuss/general-discussion/651719/How-to-solve-DP-String-Template-and-4-Steps-to-be-followed.)
- **10. Regular Expression Matching** [==DP==] **经典LCS**，`dp[i][j]`: `s[0..i-1] matched p[0..j-1]?`
    > - transfer:
        > 1. `p!=*`: 看当前char是否相等或者`p ?= .`;
        > 2. `p==*`：有两种情况，* matched 0次，👉`dp[i][j]=dp[i][j-2]`，或者 matched N次：👉 `dp[i][j] = dp[i-1][j] && (s[i-1] == p[j-2]|| p[j-2]==.)` **WHY不关注具体match次数** 👉 dp和recursion一样，**只关注当前decision**，matched 1次然后去看dp[i-1][j]，已经是recursion了，dp[i-1][j]包括那一轮包含N-1次match的结果
    > - **坑** 别放了 s="", p="a*"的情况，所以**i从0开始**
- **44. Wildcard Matching** [==DP==] **经典LCS** 10复刻,dp和transfer基本一样，同样考虑`p[j]==*`，decision是：**match 0 或者 1 个**
    > - **坑** 别放了 s="", p="a*"的情况，所以**i从0开始**
- 62. Unique Paths [==DP==] 经典DP，用一维dp够了
- 63. Unique Paths II[==DP==] 经典**二维LCS**，只有node为0才累加
- **72. Edit Distance** [==DP==] **经典LCS**，dp[i][j]:把word1[0..i] 转换到 word2[0..j]需要min steps；transfer func：相等时候看dp[i-1][j-1]，不等时候考虑三个操作
- *161. One Edit Distance* [==DP==] 72弱化版，完全可以不用dp，直接匹配
- **115. Distinct Subsequences** [==DP==] 经典**LCS**
    > - `dp[i][j]`: s[0..i]中的sub-seq match t[0..j]的个数;
    > - **transfer**: `dp[i][j] = dp[i-1][j] + (s[i-1]==t[j-1] ? dp[i-1][j-1]:0)` **choice** 用不用s[i-1]去匹配p[j-1]
    > - 滚动数组优化空间
- 583. Delete Operation for Two Strings [==DP==] **经典LCS** 弱化版72，只需考虑删除，此处不用考虑dp[i-1][j-1]+2，其已经包含在`dp[i-1][j]+1` 和 `dp[i][j-1]+1`
    > - **另一种DP** 直接找LCS，然后`mn-LCS`即所求
- 712. Minimum ASCII Delete Sum for Two Strings [==DP==] **经典LCS** 变形583，但这里考虑ASCII值不是step，然鹅写法没变化
    > - **另一种DP** 也是直接找LCS，但是根据ASCII来找
- **139. Word Break** [==DP==]: 经典DP，dp[i]代表[0..i]可以拆为dict里面string.
    > 注意在inner for loop 判断是否`dp[j] && substr(i,i-j)` 要从right &rarr; left 才高效，因为dp[0..i-1]是必然已知，反向遍历更先能找到match然后break
- *472. Concatenated Words* [==DP==] 139加强，就是对每一个word用139的dp，dict要跳过当前遍历的word，同时跳过empty word
- 5. Longest Palindromic Substring [==DP==] 经典2d dp `dp[i][j]: [i..j]是否是palindrome`, **斜着遍历**；当dp[i][j] == true &rarr; update global left and right
- **516. Longest palindromic subsequence** [==DP==]: **经典LPS** 二维dp[i][j]: [i..j]间LPS. 分情况讨论, 注意outer loop 逆序，inner loop 顺序起于i+1.
    > `空间优化`: 使用一唯dp[i]: [0..i]以i结尾的LPS. 这里的outer loop和anginner loop与二维dp一样.
    > - outer loop:
    >   - 需要用一个tmp来代替dp[i+1][j-1], 此tmp每次在outer loop初始化为0;
    > - inner loop:
    >   - `s[i]==s[j]`情况就是 `tmp+2`
    >   - otherwise: 啥也不做，因为dp[j] meaning, 此时dp[j] 不需要update，就是之前outer loop能找到的.
    >   -  必须更新tmp，也就是dp[i+1][j-1]，因为next round j++, 求`dp[i-1][j]` &rarr; `ddp[i+1][j]=tmp=max(dp[j], tmp)`,此处dp[j]是上一轮的outer loop中更新的值，也就是i=i+1时候, 自然就是下一轮要用的`dp[i+1][j]` candidate; 注意此前`s[i]==[j]`可能update了`dp[j]`,所以要提前记录.
- **1312. Minimum Insertion Steps to Make a String Palindrome** 经典**LPS**, `dp[i][j]: [i..j]中min insertion to make it palindrome`，transfer，`s[i]!=s[j]`时候考虑使用insert s[i] or s[j], `s[i]==s[j]`直接考虑`[i+1...j-1]`
    > -**坑** `s[i]==s[j]`的巨坑, i+1 > j-1是存在可能，要单独处理corner case `i==j-1`
    > -**LCS** 转换为求str和其reversed str的 LCS
- **647. Palindromic Substrings** [==DP==] 变种**LPS**, 这里是substring 不是sequence，`dp[i][j]`不能直奔主题为count，而是`str[i..j]`是否是palindrome，然后用update res
    > **坑** if `[i]==[j]`, len<=3的一定是palindrome, 高于3才看dp[i+1][j-1]
    > **另一种DP** dp[i][l]： 表示以i为起点长度l的substring, 其实一样
- *1035. Uncrossed Lines* [==DP==] **经典LCS** 二维DP，根据`A[i]==B[j]`写出递归function
- **1143.Longest Common Subsequence** [==DP==] **LCS本尊** 2d dp，dp[i][j]： `str1[0..i]于str2[0..j]的LCS`， Choice: str[i]与str[j]是否在LCS &rarr; Transfer: `str[i-1]==str[j-1]: dp[i][j] = dp[i-1][j-1]+1; else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);`
    > - Question：为何不考虑str[i-1]和str[j-1]都不在LCS情况？因为其已包含在dp[i-1][j]和dp[i][j-1]
- **1092. Shortest Common Supersequence** [==DP==] **经典LCS**，加强版LCS，这里需要先求出具体LCS，所以dp[i][j]直接存LCS，求出LCS后，依次遍历两个str来构建新string
- **1458. Max Dot Product of Two Subsequences** [==DP==] **经典LCS**，注意初始化是INI_MIN，因为我们需要至少一个product；transfer func: `dp[i][j]=max(dp[i-1][j], dp[i][j-1], max(0,dp[i-1][j-1]) + n1[i]*n2[j])`
- *1246. Palindrome Removal* [==DP==] **经典LPS** `dp[i][j]: 最少的steps to remove [i..j]`
    > - case 1：`s[i]==s[j]`, 即s[i], s[j]可以随着`[i+1, j-1]`**最后一步一起remove**，考虑`32123`, `remove(1)=1，remove(212)=remove(1), remove(32123)=remove(212)` 👉 `dp[i][j] = dp[i+1][j-1]`, **corner case**： `i+1==j`处理下
    > - case 2: `s[i] != s[j]`, 即[i..j]需要拆成两个substr来remove，&rarr; 转化为sub-problem：`dp[i][j] = min(dp[i][k] + dp[k+1][j]), k=[i..j-1]`
    > - 注意init case `dp[i][i] = 1`



## Stack/Queue
- 445. Add Two Numbers II [==Stack==] 两个stack分别存两个list，这样就reverse了，其余和2一样
- 20. Valid Parentheses [==Stack==] 一个stack
- 71. Simplify Path [==Stack==] 基本stack，注意若自己split需要对最后一个string明文处理，因为`/`为触发处理条件，而最后一个字符不一定是`/`
    > 用`stringstream`来split
    ```c++
    streamstream ss(str); getline(ss, buffer, delimiter)
    ```
- 150. Evaluate Reverse Polish Notation [==Stack==] 标准stack，里面按序存尚未进行运算的数字，包括中途新生成数字
- 155.Min Stack [==Stack==]两个stack解法，一个正常存，一个存min；一个stack解法，将存min的stack合并到一个stack，思路没有变.
- **173. Binary Search Tree Iterator** [==Stack==] 经典in-order问题，和230一毛一样。 这里**amortized `O(1)`**
- **230. Kth Smallest Element in a BST** [==Stack/Recursion/Divide&Conquer==]recursion写法常规in-order写法.
    > - In-order with Stack 1: stack里存所以node的left-child，先push当前node左child直达null，然后pop后得到当前left-most，就是in-order第二步看自己，然后`node=node->right`, 返回while loop开始push `node->right`的left-child
    > - In-order with Stack 2: pre-process先dfs入栈root及其left-child，然后进stack loop，pop出left-most，记录值，接着dfs入栈其所有left-child
    > - **Divide and Conquer**: 二分思路，就是计算left-tree counts，若count <= k, 说明在左子树，若 count == k-1,说明就是当下node，反之则在right-tree，recursive-call
    > - Follow-up: **Divide&Conquer**的延续，需要**修改node structure加入count**，即是search时候可以O(1)时间拿到子树count，[code](https://www.cnblogs.com/grandyang/p/4620012.html)
- 232. Implement Queue using Stack [==Stack==] `amortized O(1)`因为每个int 出入一次
- 225. Implement Stack using Queues [==Queue==] pop将front取出重新push 入queue
- 682. Baseball Game [==Stack==] 简单跳过
- 735. Asteroid Collision [==Stack==] 遍历，比较stack top和cur_val, 讨论相撞情况，分abs相等和不等情况，得出撞击后val，入栈，注意val=0不入栈
- **856. Score of Parentheses** [==Stack==]
    > - Stack中保留外层layer的val
    > - 遇到`(`将cur入栈并reset cur，&rarr; 外层layer前部分值已计算完，马上进入inner 括号;
    > - 遇到`)`时候说明当前inner完成，可与outer合并值，所以pop出top，即outer layer前部分值，与cur合并;
    > - **follow-up** do it without extra sapce. 找规律，**只有`()`才是真正产生score**，需要看此`()`外面包裹了多少层,最后此portion val就是`2^layer`. 所以**实时统计当前layer层数**，遇到`()`时候计算。
- **880. Decoded String at Index** [==Stack?==] 非经典题目，常规求出decode string做法不可取
    > - 先计算出`cur_len >= K`的index处，然后backward寻找Kth
    > - Backward moving 有stack的思路：当遇到digit，说明是copy而来，则source str len为`cur_len/digit`，同理，Kth在source str的位置为对其长度做mod `K%source_str_len`
    > - 当遇到char，要判断是否在kth，然后`cur_len--`. 此处要用`k%cur_len == 0`，因为mod后K的meaning从目前`1-based index` 转换到**上一round source string中的位置**，此时 `K == x * cur_len`的case下 `K->0`而不是`K->cur_len`，所以要用`k%cur_len == 0`来判断是否找到了
    > - **坑** cur_len用int会overflow，用long
- 921. Minimum Add to Make Parentheses Valid [==Stack==]借用stack思路, left表示当前left-parentheses的count，若此`count<0`，就说明缺right，res++
    > - **follow-up** 优化到 O(1) space: **two pointer** 类似cyclic sort，也是two pointer Flag Color问题：即利用原array，`pointer i: the next position to put new val`, 最后看i是否归零
- **1249. Minimum Remove to Make Valid Parentheses** [==Stack==]借用stack思路
    > 1. 先遍历得到close count，表示**当前剩下可用的`)`**； 再从头遍历，统计open，为**目前为止尚未match的`(`count**
    > 2. 关键在于跳过invalid，当遇到`(`，若此时`open==close`即没有多余`)`，skip；遇到`)`,当尚未匹配的`open==0`，说明当前`)`多余，skip；
- **946. Validate Stack Sequences** [==Stack==] **simulation**，先push入stack，然后while loop尝试按序pop出popped list直到`top != popped[j]`，此处不能只pop出一个，要尽可能pop，因为马上push新元素，会限制sequence可能性.
- *1021. Remove Outermost Parentheses* [==Stack==] 只需要一个count记录当前left parentheses数量就可以了，每一组第一个`(` 和最后一个`)`不计入.
- *1047. Remove All Adjacent Duplicates In String* [==Stack==]简单stack，相同则pop，不同push
    > - **Two Pointer** 两个pointer分别指向：next char of output, index of current; 当遇到adjacent 相同，**output index回退2**
- **1209. Remove All Adjacent Duplicates in String II** [==Stack==] 1047加强版. Stack记录`<char, count>`, 遍历str，先update/push pair，然后看`stack.back.count >= k`,当`==k`，pop back；最后组建output str
    > - **Two pointe** 类似1047的，但需要记录每个index处char的freq，这样当发现`count[i] >= k`，让output-str index倒退k
- 224. Basic Calculator [==Stack + Recursion==] 弱化版772
- 227. Basic Calculator II [==Stack==] 经典stack， +-将数字入栈，*/改变栈顶，关键在于
    > - **记录上轮计算完后的operator**，默认第一个`+`，之后遇到operator，先根据pre-operator做运算，再update pre-operator
    > - 不要老想着while一次把数字搞出来
- 582. Kill Process [==Queue==] hash-map映射ppid->pid, 常规bfs
- **772. Basic Calculator III** [==Stack + Recursion + MUST==] 经典Stack，遇到`(`，即recursion，遇到`)`，即返回
    > - recursion 处理非`()`和227 一样，**exception**： 遇到`)`或者走到底跳出while后，还剩下一个数字和operator未处理，要处理了再计算括号和返回
- *636. Exclusive Time of Functions* [==Stack==] 标准**Stack**，存`<id, start-time>`
    > - 遇到start且stack里有task，说明要中断pre-task，此处update pre-task run-time，然后push new task
    > - 遇到end且stack有**多余task**，先pop，update当前end-task的run-time，然后update stack top中的pre-task的start time，因为其resume了，**坑** `resume-time = end-time+1`

### Monotonic Stack
> - O(n)，因为所有元素只会进入栈一次，并且出栈后再也不会进栈了, push: amortized `O(1)`
> - 一般要maintain 一个original order
> - MStack元素不一定有序，但代表的值一定有序，不如存index
> - [tutorial](https://www.cnblogs.com/grandyang/p/8887985.html) [helper](https://leetcode.com/problems/sum-of-subarray-minimums/discuss/178876/stack-solution-with-very-detailed-explanation-step-by-step) [questions](https://medium.com/@gregsh9.5/monotonic-queue-notes-980a019d5793)
> - 两种pattern：Previous/Next less element **PLE/NLE** &rarr; **MStack increasing**, Previous/Next greater element **PGE/NGE** &rarr; **MStack decreasing**
    > - Previous Pattern 处理 outside while，**Stack存当前node的potential P index**，即新node的value在Stack
    > - Next Pattern 处理 inside while，**Stack存尚未确定N value的node index**，即遇新node是Stack里面node的值
- **907. Sum of Subarray Minimums** [==MStack + MUST==] 经典**PLE + NLE**，类似**828思路**，关键是找到以A[i]为min的subarray counts &rarr; 找A[i]的PLE和NLE 👉转换为经典MStack问题
    > - 计算subarray：`ple[i]..i` n个数, `i..nle[i]` m个数, 排列组合问题，前面包括空集有n个，后面包括空集有m，则包含i的subarray一共 `n*m`
    > - **followup** deal with duplcaite: ` Set strict less and non-strict less(less than or equal to) for finding NLE and PLE respectively.`
- **1130. Minimum Cost Tree From Leaf Values** [==MStack + Greedy + MUST==] 907的变种
    > - **思考**： **cost to remove a node** 如何min cost构建tree：对于一个leaf n，if another max-leaf of it's parent > n, 上一层构建时候n将不会再参加cost计算 👉从leaf取出一个node的cost是 n*(left/right leaf) 👉为了min cost，尽量少使用large leaf 👉 **Greedy** 取出n的最小cost是 n*min(left, right), 此时总cost min
    > - **转换问题**：`从array中取俩数a,b，remove min(a,b) would cost a*b, what's the min cost to remove until array.size()==1`
    > - 经典**PGE + NGE**问题，对于每一个node，remove其的min cost是 `node*min( pge[node], nge[node])`
- **42. Trapping Rain Water** [==MSstack/Two Pointer/DP + MUST==]类似84的stack, 所以做法都是确定两边**boundary**，然后以当前idx为bottom
    > - MStack：思考问题：以每个bar为bottom，能形成的**最矮一层水坑**，需要看左右min height; 引出关键：确定bottom和min height，一层层填补水坑；
    > - MStack 维护decreasing order，存**尚未以其为bottom计算最矮一层水坑的area**；当`top<i`触发计算：以当前top为bottom，next top为left bar，当前遍历的bar为right bar，计算**一层水坑**
    > - MStack：为何只计算一层水坑，因为其后stack中更高的bar为bottom时候会计算高一层水坑.
    > - **坑**： stack至少要有俩才能构成水坑，不然有了bottom没有left boundary.

    > - *Loop twice solution*: 就是先👉走一遍找每个点的left-boundary, 然后👈走一遍找打right-boundary,取min做差值
    > - **Two Pointer solution**: outside-in, 两边向中间，先移动小的那边，若发现当前数大于两边boundary，重新确定boundary和移动方向
- **84. Largest Rectangle in Histogram** [==MStack + MUST==]经典MStack，考虑问题：若以每个bar heigh作为长方形高度，需要看其左右连续的>=其height的bar个数；需要找两边的boundary； 触发计算即右边boundary找到了：`top height > 当前bar高度` &rarr; MStack用武之地; **法二：也可以直接PLE+NLE解决**
    > - **区别42的MStack decreasing，此处是increasing** &rarr; **WHY**
        1. 42当right>top, 说明**top为bar bottom**的**那一层水**可以确认了，所以是*right>top触发计算*；
        2. 84中当right>top, 说明当前**top为bar height**还可向右扩展，而当**right<top**，说明当前**top bar height**来构造的长方形不可向右扩展了，触发计算
    > - MStack维护increasing order，存**尚未以其heigh计算长方形area**
    > - 遍历bars，大于stack top 入栈，因为其**前top bar可以通过当前bar构造更大长方形**
    > - 若小于 stack top， pop top, 触发计算：就是以当前top为height，计算👉构造的长方形area, height已知，求weight，
        > 1. `MStack.size()>1`: 就是当前`index-top_idex-1`，WHY: **right_bound = cur_index-1, left_bound= pop后新top的index+1** &rarr; `因为stack下一个top高度是<=当下height, 区间bar都可以构造height, **若区间某时段有高的bar但之前pop了**，提前pop的一定比当前height高，因为MStack` eg：14532 case，
        > 2. `MStack.size()==0`： 此时pop后MStack空了，说明当前height是**之前所有最小高度**，那么width就是当前index.
        > 3. **坑** height append 0, 为了计算stack最后一个height
- **85. Maximal Rectangle** [==MStack==] 84加强版，对每一row，统计当前node高度（连续1），使用84的思路，计算出每一行的Largest rectangle，update res
    > - **坑** 每一行heights要append 0，这样不会漏掉MStack中最后一个height
- **316. Remove Duplicate Letters** [==MStack + MUST==] 类似402，经典单调stack做法
    - stack里面维护着目前的**尽可能**按照alphabet order的序列，即stack里面不是完全有序的，因为存在有序字母在freq之后归零，没法让整个res 有序
    - 需要dict记录目前剩下的字母freq，visited标记已经访问过字母
    - 流程和402类似：关键在于visited和dict的应用，按照一般MStack做法
        > - 按序遍历string 若visited过，则跳过因为其已经合适位置；**no matter what都要`dict[c]--`**
        > - 如果当前字母小于MStack的top，说明当前字母**有可能替换MStack的top**，这里需要dict判断是否剩下多余的top char，*no*则没法替换因为之后没法再加入此char，*yes*则可以pop再push；注意最后visited标记
- *1081. Smallest Subsequence of Distinct Characters* [==MStack==] 复刻316
- **402.Remove K Digits** [==MStack==] 经典单调stack思路
    - 观察可知需要尽量去除高位的大数字；
    - 当数字是non-ascending时候去尾, 若non-decreasing则去头
    - 难点在于non-sort的部分，这里使用MStack，从高位开始往后走，**MStack维护一个non-ascending的window**，当目前`digit>top`时候，不断pop直至`top>digit`，同时考虑k是否归零，然后`push digit`
    - 考虑leading zero问题，可在之前push前判断当前`digit==0 && stack.size()>0`，避免之后手动清除leading 0
    - 最后遍历完后若k>0, 则是non-ascending 情况去尾
- **456. 132 Pattern** [==MStack / Two Pointer + MUST==] two pointer做法类似3Sum，就是先固定最小的i和j，然后for loop中固定k，然后verify `(i,j,k)`. 下面讨论Mon-stack：
    > - **MStack**: 关注k，因为其可以限制i和j,所以reverse traversal, 目标是追踪**目前valid pair(j,k)中 k最大的case** &rarr; k最大最有可能找到valid i
    > - stack里**存当前k的candidates**，同时**stack.top()是当前最小的j满足`[j]>[k]`**
    > - stack是单调递减的，**WHY** 当从头一直递减，只是不断update j，因为没有`[k]<[j] && k>j`; 当新的 `num > st.top()` 👉 `j=num` and **stack中`<j(num)`的部分可以作为k**, 其满足`[k]<[j] && k>j` 👉 **Greedy** 此处k要max，所以decreasing stack不断pop util`st.top()>=num`, 每次pop都update k
    > - **reverse traversal** 想想**231 pattern**就是正序
    > - 每次遍历就是对于当前数看作i: `是否有 132 with [i] == cur_num`
    > - O(n), at worst case each number pushed/popped once at most
    > - **follow-up**: 求所有132. 此时用two pointer的法子
- **496. Next Greater Element I** [==MStack==] 经典MStack，MStack里放的是没有**尚未**找到next greater的**decreasing number**
    > - 转化为到原arr找每个number的next great并且maintain order；
    > - 考虑adjacent，若`[i+1]>[i]`则`next_great[i]==[i+1]`，同时可能`next_great[i-k] = [i+1]`, 反之next_great在后面
    > - **出现一个decreasing sequence**: MStack放的是**按序**尚未找到next_great的val，因为找到next_great的会pop出去，未找到的push into，故而MStack一定是**decreasing**
- **503.Next Greater Element II** [==MStack==] 加强版503，关键要两次loop，第一次分配**next great在其右边的element**，第二次**next great 在其左边的element**，只是第二次时候不push，因为第一次后stack剩下的就是第二次需要assign
- **654. Maximum Binary Tree** [==Recursion/MStack + MUST==]常规recursion做法就是递归传递left和right，`O(n^2)`
    > - **PGE + NGE** 对于一个node，关心其PGE和NGE两个可以成为其parent的；要求得PGE和NGE，显然**decreasing MStack**
        > 两种case: `312, 315`；**312** `3->2, 1<-2`,而**315** `3->1, 3<-5` 👉 MStack **while pop时cur node就是pop出的node的NGE &rarr; node不断其left**；**while之后PGE node update right**
    > - MStack, `O(n)` 观察可知对于有序列，一定是同一方向子树，MStack可用
    > - MStack里维护着**decreasing+已有right child但未成为其他node的left的node**
    > - 遍历arr，创建当前node，若val < st.top(),说明此node为st.top()的 right child，分配之，入栈
    > - 若 val > st.top(), 不断pop，因为当前node的left child找到, 即stack中第一个小于其的node
- 998. Maximum Binary Tree II [==DFS==] 654弱化；recursive dfs，返回**当前subtree的root**，`if root.val > val -> recursively call root->right`, `else new the node and assign it's left to root, return new root`
- 739. Daily Temperatures [==MStack==] 经典MStack，复刻496，decreasing stack
- 901. Online Stock Span [==MStack==] **经典PGE** stack里面maintain decreasing `(price, maximum span)`, 每次就是loop pop出之前所有比price小的，
    > **Why keep pop in loop** 因为之前的stock price可能有[2,1]，若当前3，must respectively加上`1, 2`的span.
- **1019. Next Greater Node In Linked List** [==MStack==] 496变形，由于list不支持index，所以要手动转换，stack里面存的是统计而来的index，res里面记对应值, 最后stack需要清空来填补res里面找不到next greate的0值
    > - **KEY** res是**mix NGE和raw value**，每一轮结束res.back()先赋予raw value, 此后若找到its NGE，则update然后pop之，之后不会再update
- **768. Max Chunks To Make Sorted II** [==MStack==] 769升级，需要考虑duplicate，普通**pre-sum**方法，先copy and sort，然后two pointer两个array一起走，当各自pre-sum相等时，可划分一个chunk，浅显易懂
    > - **MStack** **特别的MStack**，MStack里维护**non-decreasing** number &rarr; 代表**独立chunk的max value**
    > - 思维推导论：例子[013524], 做法即是对新的num，其若是目前max，则独自成chunk; otherwise，其需要加入且update之前的chunk：找到`已存chunk max 第一个 < cur_num者`，cur_num将加入其后的chunk，此时push stack的val是**新chunk的max**，只能是开始此round时候：`max(cur_val, st.top())`
    > - 最后stack的size就是chunk size，因为其记录所有chunk的max in no-decreasing order

### MonoQueue
> - 思路基本和MStack一样，一般用deque/list(dlist)，需要双向处理
> - 常用DP优化 range max

- **239. Sliding Window Maximum** [==MQueue / Heap + MUST==] 普通的sliding window做法就是利用multiset的排序，借鉴480
    > - 这里讨论**MonoQueue**，思路和MStack一样，因为要找k window max，是会不断去头加尾，所以选用**dequeue**，利用其`O(1)`的头尾push/pop
    > - 此处维护一个**decreasing** queue，**存index**，因为新加入tail可为**当前和之后k个窗口的max candidate**，其必须入queue，若用increasing queue，就不一定加入这个tail.
    > - 先看要不要去头，即`[front]==i-k`, 然后就是常规MStack的做法，不断pop top直到`q.empty() || [q.back] >= [i]`
    > - 最后要加入res的值是`[q.front()]`因为decreasing queue.
    > - 和MStack一样，每个操作**amortized `O(1)`**，所以`O(n)`
- **862. Shortest Subarray with Sum at Least K** [==MQueue + MUST==] sliding window做就是利用map/pq的排序，此处用**MonoQueue**思路, 先计算pre_sum
    > - **核心1** deque里面存的是**pre[i]的index，尚未找到以其开头的validated shortest sub-array**
    > - 👉`First while loop`，对应当前index，不断pop出validate 的dq[0]，update res. **Why pop `dq[0]`** 因为之前都没有找到dq[0] 开头的valdiate sub-array，如今找到了，那一定是shortest，其后的那些index不会找到以此dq[0]开头的更小的validated sub-array
    > - **核心2** keep dequeu inscreasing
    > - 👉 `Second while loop` 对于`pre_sum[dq.back()] > pre_sum[i]`, 我们知道 `i > dq.back()`, 若之后在`[dp.back()...j]`是validate，那么`[i..j]`也一定是validate且shorter，所以需要维持**dequeu increasing**
    > - **说明** 举例子引导到increasing queue，对`pre_sum: 236, 216，K=4`；216中2是无效的因为1的存在，可以有16更短的；236中2是有效的，因为36不一定满足; 👉 考虑用按序的window装所有`pre_sum[i]`尚未找到其开头的VSS 👉由于216这种case，说明需要保留increasing window 👉 为了快速访问head+tail，用dequeue
- **1499. Max Value of Equation** [==MQueue/Heap + MUST==] 239变种，仔细分析可知 `sum=xj+yj+yi-xi` &rarr; sliding window 求`max(yi-xi)`；
    - **Heap** `O(nlogn)` 维护max-heap，存`<y-x, x>`；先维护sliding window `x-xi <= k`，除去当前max-heap top invalid, 剩下max-heap top就是目前window中max yi-xi, update res， push 当前点
    - **MQueue** `amortized O(n)` 和239一样，用**decreasing deque 存index**; 先维护sliding window `x-xi<=k`, pop front所以invalid &rarr; 此时max yi-xi 就是新的front，直接update res &rarr; push 当前point into deque，需要maintain deque deceasing特性 **WHY decreasing deque** 试想若当前 y-x 是新的max，因为之前所有的smaller value均可discard，plus当前的index 和 后面的index更近，更满足`xj-xi<=k`
        > - **KEY** 此处是对于当前node，求得其之前`xj-k`范围内`max(yi-xi)`, 然后结合**当前node** update res，所以update res发生在pop out-range left points后，**MStack pop前**
- **1425. Constrained Subsequence Sum** [==MQueue/Heap + MUST==] 1499变形，经典**MQueue**应用: **对DP的优化**；Initially，dp做法, `dp[i]: max subsequence sum when end at i`, transfer: `dp[i] = nums[i] + max(0, dp[i-k...i-1])`, **KEY** 其实就是在`dp[i-k]...d[i]`中找 P-MAX 👉 **Sliding window max**
    - **MQueue** 基本等同于1499的做法，维护`decreasing MQueue`, 先`pop_front()`remove invalid index， 然后update current `dp[i]`, 最后push into MQueue，要maintain MQueue的`no-increasing order`


## Greedy
> - 一般建立在 sorted or native-order 条件
> - **jump game类型对比interval类型**
    - 前者`[i]->j`, **在position上遍历，利用pos natively increasing, O(N)**，易于求合并需要min interval, 有**line-sweep** 意思
    - 后者`[i,j]`, **在intervals上走， 需要sort, O(mlogm)**，易于合并
> - tails array的妙用 对sequence： LIS, 658

- **45. Jump Game II** [==Greedy + MUST==]: 55加强，思路基本一样，要同时维护`preMax`和`cur_max`;
    > - `pre_max`:上一轮到达最大，`cur_max`: 当前下一步能到达最大；
    > - 当前`index > pre_max`，需要`pre_max=cur_max && step++`, 因为到[index...new pre_max]，需要跳一步.
- **1326. Minimum Number of Taps to Open to Water a Garden** [==Intervals + Greedy + MUST==] 45加强版；**转换问题**：构造jump game中的jump array或者Video Stitching中sorted **intervals**， 之后就是jump game II
    > - **解法1** 构建jumps解法：`jump[i]=j`, 剩下就是45复刻
    > - **解法2** 非常不建议, 构建interval解法：`interval[i, j]`, 剩下就是45复刻, 但坑多：sort是先`first increasing and second decreasing`, 初始化`cur_max = intervals[0].second`, 最后返回若`pre_max != cur_max` 则还有一步要走
- **1024. Video Stitching** [==Intervals + Greedy==] 1326复刻, jump game II. sort intervals &rarr; preMax + curMax
- **55. Jump Game** [==Greedy==]: 经典greedy，有BFS的level travel感觉，只需要考虑目前的max_reach, iterate array，如果当前`index > max_reach`，说明完蛋，否则update max_reach
- **406. Queue Reconstruction by Height** 类似315，sort+insertion.
    > - Greedy 思路：按height decreasing然后index increasing 排序; 遍历插入sorted array at the index(people heigh than him)
    > - **reason**：height高的index会小，在处理新node，sorted array里面所以pp都 height >= current，所以只需要将当前node insert into sorted array at its index，不用担心其后的，因为they are short
    > - **Space optimization** 常规使用build-in insert O(n) space；使用**冒泡**思路，把当前node swap到其index，即 `O(1)` space
- **1029. Two City Scheduling** [==Greedy==] 经典greedy，数学假设：所有人去A，然后挑N人去B，则greedy选择`cost[B]-cost[A]`，就是给cost 按照 `cost[B]-cost[A]` sort.
- *1221. Split a String in Balanced Strings* [==Greedy=] 左到右优先挑出RL freq一样的
- *1282. Group the People Given the Group Size They Belong To* [==Greedy+HashMap==] 用hashmap给分组，然后greedy遍历每一个组
- *781. Rabbits in Forest* [==Greedy + HashMap==] **greedy** 报相同数的人尽量归同一group；统计同一group的人数，`tot/group_size`即为有几组，**不够一组按一组算**
- *1338. Reduce Array Size to The Half* [==Greedy==] 标准greedy，优先去除freq大的直到满足条件
- **659. Split Array into Consecutive Subsequences** [==Greedy== ] **Greedy**: `if num[i] can append to tail of existing sequence, do it; otherwise build a new sequence start at num[i]`
    > - **NOTE** `tails[i]: 需求tails==i的seq个数` 或者 `目前tails==i的seq count`
    > - **KEY 1** first，sliding window 没用，因为`[1,1,2,2,3,3]`; 需要maintain not-used的number count
    > - **KEY 2** 对于每个num，关键在于**start a new sequence or append to exiting one** 👉 需要maintain tails array：代表`exiting valid sequence的tails and its freq`
- **846. Hand of Straights** [==Greedy==] **659强化**，count freq，从每个数开始count W-size array，若没法完成false, **KEY** `tails`思路
    > - **follow-up: what if W is too big** [queue法](https://leetcode.com/problems/hand-of-straights/discuss/135598/C%2B%2BJavaPython-O(MlogM)-Complexity) **很类似tails array group，此处是opened array group**
    > - **KEY** `opened:` 目前有多少不到W-size的连续array，`queue starts:` 按val顺序记录每个val处new open的array个数，**即使0也要加入**，**作用：help on W-size check**, `last:` 上一个检查的val，check是否valid
    > - **Core-1**：维护opened，从小到大遍历val dict，对于new val，关心**其是否可以承接之前opened array，即val 连续性**，否则false
    > - **Core-2**: `starts queue`加入当前val需要new opened的 array(**即使0也要加**), update `opened`, `start queue`的作用就是**sliding window**，来判断queue头部的arr group是否可以关闭了，此时 update `opened`, pop head;
    > - **NOTE** 最后看`opened==0`
- *1296. Divide Array in Sets of K Consecutive Numbers* [==Greedy==] 846
- **1055. Shortest Way to Form String** [==Greedy==] Greedy就是对每一个target char，从当前source位置iterate找到第一个match：找不到`idx=source_end`则重置source index, **重置次数**就是subsequence个数
    > - **Optimize to `O(nlogm)`** 需要`O(logm)`得知next source index &rarr; 使用Binary search，就是将每个char的source index整合按序放入vector，之后直接对其进行lower_bound 即可
    > - **Optimize to `O(n+m)` **空间换时间**，需要`O(1)`时间知道next source index &rarr; 提前建表记录每个位置char的next index
- **1057. Campus Bikes** [==Greedy==] 常规greedy，按照要求来，`dist-> w_index -> b_index`依次排序，可以sort or priority_queue `O(mnlog(mn))`
    > - **bucket sort optimize** 因为distance 在范围[0..2000],可以用bucket sort 替换priority_queue. `O(m*n)`
- **1066. Campus Bikes II** [==DP/Heap==] 1057加强，不能在使用之前greedy；最低级用backtracking，好一点用dfs + memorization, 此处关注**Dijkstra类方法**
    > - **KEY-1** 用 bitmap记录bike使用情况，此处由于10bikes限制所以`0x3FF`足以
    > - **KEY-2** **DFS + memorization**思路，worker的order不重要，在常规DFS中，就是对当前worker选取一个unused bike，就算次**state的cost**，当是最后一个worker，即bike选完了，则比较计算total min cost
    > - **KEY-3** **How Dijkstra help** 是在Key-2基础引入min-heap, 每次取min来遍历
    > - **WHY correct** 设一共3个worker，w1时加入all w1的choice，pq 含有基于w1的按序的tot-cost，w2选取时若发现new tot-cost入pq后还是min，说明w1其他留在pq的选择是non-optimal的，那么接下来进入w3时候可以保证w1,w2已经在optimal solution set, so w3 under this state can find the optimal set
    > - **KEY-4** 巧用integer表达state，此处`worker+bike_map`, 需要visited map on state 来跳重复
    > - follow-up: to Minimize the max distance between bike and worker
- **948. Bag of Tokens** [==Greedy + Two Pointer==] Greedy思路：power买进min-p-cost的token，point去换max-power-cost的token；所以先sort, **two pointer outside-in**: 先看left能否换point，不行再去right换power，若都不行，**dead-game break**
- 763. Partition Labels [==Greedy+Sliding Window==] 类似**merge interval**；存下每个char出现的最远index，开始遍历，initial当前遍历到的right_most，当发现当前index== 当前char最远index, 记录其长度, update right_most, move forward.
- 769. Max Chunks To Make Sorted [==Greedy==] 763 复刻，类似merge interval, 设置local_max, iterate array，当index==local_max, 说明前面chunk元素已全部找到对应位置，chunk++
- *1546. Maximum Number of Non-Overlapping Subarrays With Sum Equals Target* [==Greedy + HashTable==] pre-sum的hash-tbl + greedy
    > - **Greedy** 记录之前满足target的interval的right, 对当前的val，看`dict[pre_sum-target] >= pre_right` 来avoid overlapping
- **1414. Find the Minimum Number of Fibonacci Numbers Whose Sum Is K** [==Greedy==] 先得到fib数列，最大值不超过k，然后逐一greedy找最接近得ceiling，用binary search
    > -

## Graph
> -  DAG判断cycle:
    1. Topology sort的indegree(`<0` means cycle); **DON'T USE BFS for DAG**
    2. [DFS coloring](https://www.geeksforgeeks.org/detect-cycle-direct-graph-using-colors/?ref=rp) (**3 colors**: `white/gray(not visited all children)/black(all children visited)`) 若访问遇到grey就是遇到cycle了, **可得cycle count**
> -  UDAG判断cycle:
    1. Topology sort的indegree(`==1`);
    2. Union Found; 发现同一group的俩node要union就是cycle
    3. DFS/BFS (**2 colors**: visited/unvisited) 若next u是visited并且`u != parents[cur] 因为UDAG，cur->parent[cur]`,找到cycle
> - DAG：从任一点出发找cycle，看`indegree<0`

- **332. Reconstruct Itinerary** [==Graph + DFS + MUST==] 经典**post-order** DFS on graph. **KEY** DFS时候去除visited edge，**难点** 希望先visited cycle
    > - 维护smallest lexical order &rarr; **pick small next in each dfs**; &rarr; 用`hashtable+multiset`存adjacent list **WHY multi-set** 因为可能多次去一个目的地；因为去除visited edge，DFS不会遇到cycle loop
    > - **WHY Post-order** DFS走到endpoint stuck后会backtrack到有unvisited node的点。题目保证了有解，我们希望`cycle->path`, 但DFS可以`path->cycle`也可以`cycle->path`
        1. 若用**pre-order**思路先打node再visited，由于其先self再children，最后可遇到`path->cycle`，是invalid
        2. 考虑**post-order**；post-order是先visited next后print self，即是**reversed path**，打印一定是`r_path->r_cycle`, 这是post-order性质，先children最后self [link](https://leetcode.com/problems/reconstruct-itinerary/discuss/78768/Short-Ruby-Python-Java-C%2B%2B)
- *1042. Flower Planting With No Adjacent* [==Graph==] 题目限制保证有解，greedy即可
- *1267. Count Servers that Communicate* [==Graph==] 可以简单判断，计算`rows[]`和`cols[]`代表每行列sum，对一个server，只要`rows[i]>1 || cols[j]>1`即可
- **1059. All Paths from Source Lead to Destination** [==Graph==]经典DAG找cycle，正常BFS，用topology sort的**indegree代替visited map** 👉 `indegree[i]<0`即有cycle

### Shortest Path
> - weight 计算转换： `a + b + c` &rarr; `ln(a) + ln(b) + ln(c)` &rarr; `a*b*c`，针对换汇类
> - **BFS** 常规BFS可以求src->dst SP
> - **Dijkstra** 基于BFS+relax，**单点到所有点** **algorithm**：set A和B，A是确定SP的，B是尚未确定，dist记录每个点SP，每次取B中dist最小值（**取出min时候src->此node的SP已固定**），relax connects, 将点放入A，until set B空或者没有connects，实现用priority_queue，[code](https://zh.wikipedia.org/wiki/%E6%88%B4%E5%85%8B%E6%96%AF%E7%89%B9%E6%8B%89%E7%AE%97%E6%B3%95) **validation**：relax dist based on `min(dist(B))` **drawback** 只能处理positive weight, 可以有cycle **Time** `O((E+V)logV)`
> - **Flyod-Warshall** DP **所有点到所有点的SP**  **algorithm:** `dp[i][j][k]: 经过前k的i-j的SP` **transfer**: 用不用k做中转, **实际实现** `if (dp[i][k] + dp[k][j] < dp[i][j]) dp[i][j] = dp[i][k]+dp[k][j]` **drawback** 不能处理negative cycle **Time**  `O(n^3)`
> - **Bellman-Ford/SPFA**
- 743. Network Delay Time [==SP==] 经典Dijkstra，也可以bellman-ford, 注意label从1开始
- **787. Cheapest Flights Within K Stops** [==BFS/Dijkstra==] 由于K stop限制，**此题不能直接用Dijkstra**, 因为Dijkstra永远沿着minimum-path走，由于K的限制，不一定会选择global minimum-path
    > - **普通BST**：就是走K level，若遇到dst，update res
    > - **Modified Dijkstra** 大体和Dijkstra相同，但取消**relax**，即不沿着global minimal path走, 是将所有possible path导入pq，此处pq里面装的`<price, city, stops>`.
    > - **原理** 把所有当前city的edge入pq，等于考虑所有情况，包括optimal global的，此时由于pq特性，**每个city第一次pop出来时候就是src &rarr; city的最小值**，后面pop出来的也不会减少到此cisty cost，这也就是Dijkstra优化的地方，然鹅**此处有k限制，需要考虑所以sub-optinmal情况**, 所以全部push进pq
- **1129. Shortest Path with Alternating Colors** [==BFS==] 常规BFS找shortest path, 重点是**同时考虑red/blue**遍历case，故visited/queue都加入color一维度, 交替遍历
- *1334. Find the City With the Smallest Number of Neighbors at a Threshold Distance* [==SP==] 求多点到多点SP，直接套用**Flyod**
- **1368. Minimum Cost to Make at Least One Valid Path in a Grid** [==BFS/Dijkstra + MUST==] **BRILLIANT SP** 先转换题意，*方向相连的neighbor `weight=0`，其他neighbor的`edge weight=1`* 👉 求从top-left &rarr; bottom-left的最短距离
    > - **常规Dijkstra** 遇到arrow相连的push weight 0, otherwise 1, 标准pq流程，`O(mnlogmn)`
    > - **o-1 Dijkstra** 注意到weight只有0和1，用**dequeue**替换pq，其余不变，这样优化 `O(mn)`, 其实本质是**BFS+DFS**
    > - **Magic BFS + DFS** 外部常规BFS，内部找**NEXT NODE**时候不再是向周围扩散一格，而是用DFS找到当前node可以`cost=0`到达的node，跳过visited然后enqueue. **KEY** 寻找next node由于cost=0可以到达更远点，用DFS找出
- *1462. Course Schedule IV* [==SP==] 不用topology，**转换问题**node a能否到b，这里需要知道所有node可否到所有node，修改套用**Floyd**，其实就是DP思路
- **505. The Maze II** [==SP==] 标准**Dijkstra**, 不同于The Maze的bfs，常规BFS只能求出`minimum steps to reach dest`，此处是求SP，转换为graph问题，经典**Dijkstra**；
    > - 第一次遇到destination即可return，因为**Dijkstra**中，pq出来的一定是confirm SP的node
    > - **比较490** 仅仅是将queue转换为pq; 不需要visited map, 因为**Dijkstra**的relax已经暗示了visited or not
- **499. The Maze III** [==SP==] 505升级，变形**Dijkstra**;
    > - **KEY 1** 处理hole问题，在滚动时候加入对hole位置判断，当为hole时候，**不回退**
    > - **KEY 2** 生成path，相似与dist vector，建立path vector，不用带着path走；
    > - **KEY 3** 要求minimal lexicographically path，所以在`dist[i][j] == dist[x][y]+moves` 时候也尝试update path
- **1514. Path with Maximum Probability**[==SP==] **Modified Dijkstra**，求的是max，所以用max-heap，同时求dist由+变*
    > - **坑** 初始化`dist[start]=1`
    > - **不用pq用queue的解法** 思路是一样的，但queue时候遇到end不能return，因为不一定是optimal，pq则反之


### MST
> - 有时候需要加入virtual node
> - **Kruskal**: 加边, Sort Edge + Union Found, **给edge时候写简单**
> - **Prime**: 加点, 类似Dijkstra, 除了维护pq，还需要一个inMST来标注每个node是否在MST，[code](https://www.geeksforgeeks.org/prims-algorithm-using-priority_queue-stl/)
    1. **prim**更新的是未标记集合到已标记集合之间的距离
    2. **Dijkstra**更新的是src到未标记集合之间的距离

- **1168. Optimize Water Distribution in a Village** [==MST==] 经典**MST**，重点是**加入virtual node 0 代表水源**，连接所有node，edge weight就是打well的cost，这样就是MST了
- **1135. Connecting Cities With Minimum Cost** [==MST==] 使用**Kruskal Algorithm**，即sort edge然后union found

### Bipartition
> - 二分图例子：movie <-> actors, 快速求movie的actors，actor‘s movie, 常规是两个hash-table，但一个bipartition graph足矣
- **785. Is Graph Bipartite** [==Bipartition/BFS==] 等同886，对于一个node，若已经染色，则其分组确定，属于一subset，否则，以其为起点，建立新的subset.
- **886. Possible Bipartition** [==Bipartition/BFS==] 非典型UF，建立邻接表，遍历表，对每个node，其root不能和edges' root相同，同时把所以其edges root设为edge[0].
    > - BFS做法：**填充颜色**， 建邻接表，遍历node，填充其和dislikes颜色，包括其dislikes的dislikes也要填充颜色，此处类似用了UF的思路

### Topology Sort
> - BFS思路
> - 一般是选对source，一层一层剥离sources，得到ordering
> - sources的选择借助degree，不一定非得root，可以Leaf，取决于剥离top-down or bottom-up
> - 适用DAG和un-directional graph
- **GCI: Topology Ordering** [==Topology Sort==]
    > - 关键在于从source出发，keep remove source，产生新source:
    > - Stepwise: `O(v+e)`
        >   1. Initialize the graph (in-degree+Adjacent List)
        >   2. Build the graph &rarr; Find the initial source
        >   3. Iterate source, (remove it's children in-degree, push new source)
    > - follow-up-1: 判断cycle in DG：就是看存在topology order否
    > - follow-up-2: return all orderings: 用backtracking
- 207. Course Schedule [==Topology Sort==] 复刻GCI：Topology Ordering.
- 210. Course Schedule II [==Topology Sort==] 207复刻
- **269. Alien Dictionary** [==Topology Sort==] 加强版207，基本做法topology的BFS版本，记录in-degree和adjacent-list
    > - 比较相邻的word，得到edge，同时跳过记录过的edge，避免in-degree重复计算
    > - 最后比较`res.size==num of characters`
    > - **坑** 计算edge时候，若走完相邻word较小的len后没有找到edge，且`word[i].size() > word[i+1].size()`，这是invalid，返回“”
- **310. Minimum Height Trees** [==Topology Sort + MUST==] 逆向207，此题不是从root向leaf剥除，而是**反向从leaf向root剥除** + **level traversal 剥离**
> - **转化题意**：首先这是un-directional graph instead of tree；注意到**leaf不会生成MHT因为其parent可以产生更矮的树**；从leaf向root剥除, **NOTE：这里root是original情况下不为leaf的node**
> - 由于不是DAG，所以in-degree和out-degree合并成单一degree； 构造graph过程edge的两边degree都++, 并且各自的children包含对方；
> - 当`degree==1` push queue, 当`left_node == 2`终止，WHY？**`leaf==2`时候只有一个edge，谁当root都行**，最后里queue剩下的就是可能的roots
- **444. Sequence Reconstruction** [==Topology Sort==] 207变形，思路一样，构建topology ordering，验证之; 注意要求unique order所以**queue size不会超过1在anytime**
    > 另一个不构建graph方法 [link](https://www.cnblogs.com/grandyang/p/6032498.html)
- **802. Find Eventual Safe States** [==Topology Sort==] **reverse TS**，需要从**outdegree == 0**入手
- **1136. Parallel Courses** [==Topology Sort==] 207复刻，只是改为求topology sort中bfs的depth, 关键在follow-up
    > - **follow-up** `If only pick at most k courses in one semester, the shortest time to finish all courses`
    > - **Greedy**：计算depth array + course-denpend, `depth[i]: steps to deepest leaf`，用`priority_queue`替换`queue`，**存node depth**，每次pq中取**at mode K**
- **1203. Sort Items by Groups Respecting Dependencies** [==Topology Sort + MUST==] **outer-inner topology sort**, 需要里外两层tp，即sort-group，sort-item-inside-single-group；**WHY correct?** 要求相同group的相邻，同时满足item topology order
    > - **KEY1** 不同group的item有topology order &rarr; 其正好就是group topology order 👉**do group TP first to maintain TP order between 不同group的item**， then do TP on items inside a group
    > - **KEY2** 如何处理-1的group 👉 不能将-1 group item 放入single group like m 👉 而是**每一个-1 group item 单独成group**
    > - **坑** 需要建立`group -> items` 的map，不然timeout

### Union Found
> - 实现find，union: [demo](https://labuladong.gitbook.io/algo/gao-pin-mian-shi-xi-lie/unionfind-suan-fa-xiang-jie)
> - **weighted balance optimize to `O(lgn)`**：`union`总是小树append大树；
> - **path optimize to amortized `O(1)`**: `find`压缩path `path[i] = path[path[i]]`,此举ensure depth never > 3 at anytime(画图解释) **recursion 写法注意**
```c++ {.line-numbers}
// quick-find recursion VS iteration
string find(int target) {
    if (target != parents[target]) {
        int pr = find(parents[target]);
        parents[target] = pr;
    }
    return parents[target];
}
string find(in target) {
    while (target != parents[target]) {
        parents[target] = parents[parents[target]];
        target = parents[target];
    }
    return target;
}
```
> - Why 有了path压缩优化还需要balance的？没有balance下需要find时候触发compression，而有了balance则减少这些压缩
> -  **Index 转换** ==MUST==转化 2-D index to 1-D: **`n*i+j`**，其中**n为column**，m为row, **由此拓展到更高维度 &rarr; 1-D**
> - UF也适用于任何**配对**问题 如765

- 128. Longest Consecutive Sequence [==Union Found / Hash-Table==] UF就是`union(i, i-1, i+1)`, 同时apply path compression和weight balance，amortized `O(n)`
    > - **Hash-Set**解法 `O(n)`: 所有数入hash-set，然后对每一个数，dfs不断pop其两端连续数，然后update res，**每个number进出set一次** &rarr; amortized `O(n)`
- **130. Surrounded Regions** [==Union Found==] 常规DFS/BFS是先遍历边缘上O，把其相连的O及其自己换成其他字符，然后遍历整个board，把left O换成X，在遍历board，把之前的其他字符换成O
    > - **Union Found**, 把**边缘的O的parent union到一个dummy node**，然后遍历**inner board**, union 当前O和与之其相邻的O **4 direction**, **这里所有边缘延伸出来的O将union到dummy**，最后遍历inner board，将parent不为dummy的O变为X
    > - **MUST KNOW** convert 2-D array index to 1-D: `n*i+j`，其中n为column，m为row
- 261. Graph Valid Tree [==Union Found/Topology Sort==]
    > - **Topology Sort** 即从leaf往root剥离，若最后没法遍历完则false
    > - **Union Found** 经典UF，需要最后判断`count==1`
- **305. Number of Islands II** [==Union Found==] 经典UF，关键在于initial count=0, 遍历positions do `count++`, 然后和其四周的land进行union
    > **坑**： positions可以有duplicate，要跳过；不能只依靠`i*n+j`，要check boundary
- 323. Number of Connected Components in an Undirected Graph [==Union Found==] UF本尊
- **399. Evaluate Division** [==BFS / Union Found + MUST==] **recursion版本 quick union**
    > - **BFS** 常规adj-list + BFS找min step to reach node，沿途update res
- 547. Friend Circles [==Union Found==] 标准Union Found, 小优化：j从i+1开始跳过已经计算部分
- 684. Redundant Connection [==Union Found==] 标准UF，遇到第一个same root就是cycle发现，且是last edge to remove; `node_num==edge_num+1`来初始化
- **685. Redundant Connection II** [==Union Found==]
    > - **REQ** 明确题目要求：只有一条edge cause invalid；invalid case: **有向环 or node的indegree==2**
    > - **KEY** 三种case
        1. **only`indegree==2`** &rarr; remove 2nd edge cause `indegree == 2`
        2. **only 有向环** &rarr; remove the edge cause cycle
        3. **both 1+2** &rarr; 造成`indegree==2`的node一定在有向环中，因为**REQ**  👉 remove the edge cause 1+2
        - **对3，重点是将其转换为1 or 2**
    > - **NOTE-1** 找到`indegree==2`的俩edge后，需要**记下两个edge + "remove" 2nd edge** &rarr; **WHY "remove"**: avoid UAG 但非DAG cycle, 如`1-->2-->3<--1`
    > - **NOTE-2** 找cycle依然用UF，将DAG当成无向图处理
    > - **CASE** `[1,2],[2,3],[3,1],[4,1]` VS `[4,1],[1,2],[2,3],[3,1]`，图是一样，但加入顺序不一样，需要remove`[3,1]`,由于我们invalidate 2nd edge of `indegree==2`, 组合1 removed [4,1], lead to **case2**, 组合2 remove`[3,1]`, lead to **case 1** 👉 处理**case3**就是将其转换成case1 or case2，这也是**invalidate 2nd edge**的妙处
- 695. Max Area of Island [==Union Found==] 标准UF，但是update global size时候要比较三者
    > - BFS 的方法快且少用空间，此题不适宜UF
- **990. Satisfiability of Equality Equations** [==Union Found==] 经典Union Found，`==`就是union两个char，先遍历所以`==`建立union found graph，然后看`!=`的两个char，valid情况下二者不能指向一个parent
- **721. Accounts Merge** [==Union Found==] 经典Union-Found, 以email为node构建union found graph，同时建立graph并且union之后，需要再遍历一遍，将每个email insert into the **TreeSet** belong to its root
    > trick：Union时候可默认第一个email为root，后续的去union此root即可
- 737. Sentence Similarity II [==Union Found==]标准UF，注意union时候构造node，少走一次
- **803. Bricks Falling When Hit** [==Union Found / DFS + MUST==] 类似130的UF，先remove all brick，union剩下的brick，其中top的brick单独union m*n 代表non-dropping bricks &rarr; reversely 加回brick，看`root[m*n]`即non-dropping brick的diff即是对应drop的brick
    > - **Reverse思路** 此题用reverse思路，在174.Dungeon Game也用过；若正向走，UF中没法去除brick + DFS找dropping复杂度高 👉 考虑**reverse**，假设我们逆向加入brick，看形成的新non-dropping，这就可以用UF和DFS快速确定
    > - **DFS** DFS简洁：去除hit brick，将所有`i==0`相连的brick set to 2代表non-dropping；reversely add hit brick back，看新added的brick是否**四个方向connect non-dropping**，若不是，仅仅set to 1 as brick，otherwise，由此点做DFS找到相连区域，计入res array [link](https://leetcode.com/problems/bricks-falling-when-hit/discuss/119829/Python-Solution-by-reversely-adding-hits-bricks-back)
    > - **坑** 计数 ignore hit brick itself
- 839. Similar String Groups [==Union Found==] 标准UF，以index 为root instead of char
- **924. Minimize Malware Spread** [==Union Found==] 标准UF，前期UF typical UF 操作；
    > - 后面需要对initial pre-processing: 先得到每个malware root对应uf size; 若有share root的malware，则uf size为0
    > - 再iterate initial，按照uf size 和 index 大小 update
    > - remove a malware node是将其malware-free， 而不是移除node
- **947. Most Stones Removed with Same Row or Column** [==Union Found + MUST==] 换皮特别UF，关键是可以**UF on index instead of node**
    > - **KEY** row && col 完全可以看作**单独point**，不需要node-node进行Union；UF单一node的(row，col); 根据题意，row，col**各自相等就算connect**
    > - **NOTE** 需要区分row 和 col，所以`union(row, ~col)`
- **959. Regions Cut By Slashes** [==Union Found + MUST==] 换皮UF，经典在于**转换每个格子为四个相连的小格子{0,1,2,3}**, slash**斜着分割**了各个小格子，可用UF
    > - 每一格子中，若空，union `{0,1,2,3}`， 若'\'， union `{0,1},{2,3}`, 若`/`，union `{0,3},{1,2}`
    > - 同时考虑相连小格子的union `{0,2}`, `{1,3}`
    > - **Index conversion** 单独每个大格子可用`n*i+j`, 大格子分成4个小格子，就是再apply the same idea `4*(n*i+j)+k`
- **1391. Check if There is a Valid Path in a Grid** [==UF==] 959 变形；独特的转换：**转换每个格子 center->four edge**，👉 `each edge of a node can share with other nodes`; 其他就是正常UF，注意parent用pair，最后`check [0,0] center node root == [2*i, 2*j] center node root`
    > - **图释** 推荐959划分加入center node，即大node边小5node
    ``` c++
                [2i,2j-1]
                    |
    [2i-1,2j]---[2i,2j]---[2i,2j+1]
                    |
                [2i,2j+1]
    // 2. 类似959划分
        0
        |
    3 - 4 - 1
        |
        2
    ```
    > - **建图DFS** 同样需要转换 **每个node划分成小的3x3**, 然后依据original graph -> new 3x3 based graph; 最后DFS`[1,1]`出发找终点
- **1102. Path With Maximum Minimum Value** [==Union Found / BFS==] BFS类似Dijkstra，用max-heap替换queue，每次先greedy走大的node，update res直到遇到终点
    > - **Union Found** 非典型UF，其实是MST：
    > - 1. 将所有node按照大小排序， 保留seen list，为UF group
    > - 2. 从大的开始，update min，加入seen list，每回判断start和end是否属于一个root为结束
- *1101. The Earliest Moment When Everyone Become Friends* [==Union Found==] sort + union found, **MST**
- *1061. Lexicographically Smallest Equivalent String* [==Union Found==] 标准UF，**去除weight balanced**，因为要字母表order
- **1202. Smallest String With Swaps** [==Union Found==] 经典UF
    > - 对index进行UF instead of char.
    > - exchange的chars构成一颗树, find those trees and sort each in order
    >- 找结果时候先iterate str找出每个index的root并将其加入root‘s list，同时构建tmp str, sort tmp后按照之前index顺序swap
- *1319. Number of Operations to Make Network Connected* [==Union Found==] 标准UF，最后返回时候判断connections够不够connect n node (n-1 connections)
- **765. Couples Holding Hands** [==Union Found + MUST==] 特殊**UF**，总共N个group，2N个node，将原配和现配各自union，看最后剩下的component，minimum swap就是`N-k (k: current group count)`
    > - **WHY minimum SWAP == `N-k`**
        1. break a pair-t cycle 需要swap `t-1`次 **画图解释**
        2. 需求`k个group -> N个pair`, 假设k个group里面有`A`个非cycle的valid pair，剩下`B`个cycle
        3. **break`B` cycle to `N-(A)` valid pair** &rarr; `ni=pair size of ith cycle, tot-swap = (n1-1)+(n2-1)+...+(nb-1) = Sum(ni) - B = (N-A)-B = N-k`
    > - **启发** UF不仅适用graph，island question etc，也适用一起需要配对的问题

