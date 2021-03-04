---
layout:     post
title:      "四种随机生成迷宫的算法"
subtitle:   "深搜遍历、递归分割、并查集和最小生成树原理"
date:       2020-08-09 12:00:00
author:     "heapify"
header-img: "img/home-bg.jpg"
catalog: true
tags:
    - 过程生成
    - 无向图
    - 最小生成树
    - 递归分割
    - A* 搜索
---

#### —— 前言 ——

Computer Scientists 擅长把组合数学里面的东西运用到编程中来，有些可以对算法起到加速效果，而更多情况下是为了实现[程序化生成技术](https://indienova.com/indie-game-development/procedural-narrative-generation-and-ai/) (Procedural Content Generation)。这次想带来的迷宫生成算法，难度虽然不高，却都是**图论在游戏编程里的精彩应用** —— 这些关卡如果纯粹靠人力来设计，不仅耗时而且其细节丰富程度与算法生成的迷宫相去甚远。这些算法，尽管过程看起来像随机的，它们都有各自的规则来确保生成的迷宫满足无环路和连通性两点要求。换一个方式来讲，挑选迷宫中任两个点，必可以寻找到通路，并且这个通路是唯一的。<!-- Python 这门语言仅适合做演示；商业游戏开发，请选用效率更高的静态语言。 -->

组合数学与图论研究数据在内存中不同的存储方式及相邻数据之间的关系，这些概念对执行效率存在显著的影响。一般来说，效率受到了两种因素的制约：一方面来自数据结构自身的查找与合并等实现方法；另一种是宏观流程 —— 俗称的*算法套路*。数据结构是算法的基本单元，其方法的实现影响着全局的效率。宏观流程尽管可以在算法书中查找到套路，细节上还是有许多可以优化的地方，不同的人写起来差别很大（快慢指针、双指针、滑动窗口、二分查找… 这里面学问多多，不太容易展开来讲）。这种细节上的问题，不写几个练手的项目出来，怎么能找到差距呢？

#### —— 准备工作 ——

老规矩，一切开始之前，先定义用来表示图的抽象数据类型 (ADT) 及其类方法。这些 ADT 是组织程序逻辑与实现任何算法的基础。在此只提供设计方案，如有兴趣请查看 `Maze.structs`。
```python
from copy import deepcopy

class Maze(object):
    """Essentially an undirected graph."""
    
    def __init__(self):
        self.__nodes = []
        self.__walls = []
    
    @property
    def nodes(self):
        return deepcopy(self.__nodes)
    
    @property
    def walls(self):
        return deepcopy(self.__walls)
    
    def InsertNode(self, node):
        pass  # 此处略去一些代码
    
    def SetVisited(self, vect):
        pass  # 此处略去一些代码
    
    def hasNode(self, vect):
        vects = [node.current for node in self.nodes]
        return vect in vects
    
    def GetNode(self, vect):
        for node in self.__nodes:
            if node.current == vect:
                return node
        print("Node not found!")
    
    def SetConnected(self, vect_1, vect_2):
        pass  # 此处略去一些代码
    
    def SetApart(self, vect_1, vect_2):
        pass  # 此处略去一些代码
    
    def __str__(self):
        pass  # 此处略去一些代码

class Node(object):
    """Data structure for representing each cell."""
    
    def __init__(self, current, neighbors):
        self.__visited   = False  # by default set it unvisited
        self.__current   = current
        self.__neighbors = neighbors
    
    @property
    def visited(self):
        return self.__visited
    
    @visited.setter
    def visited(self, value):
        self.__visited = value
    
    @property
    def current(self):
        return self.__current  # deepcopy() removed
    
    @property
    def neighbors(self):
        return self.__neighbors  # deepcopy() removed
    
    def AddNeighbor(self, new):
        pass  # 此处略去一些代码
    
    def DelNeighbor(self, target):
        pass  # 此处略去一些代码
    
    def ListDisconnected(self):
        """Enumerates a node's unvisited neighbors."""
        for valid in validNeighbors(self.current):
            if valid not in self.neighbors:
                yield valid
    
    def __eq__(self, other):
        if isinstance(other, Node):
            return self.current == other.current
        else:
            return NotImplemented
    
    def __repr__(self):
        name    = self.current
        content = self.neighbors
        return '{!r}: {!r}'.format(name, content)
```
在生成迷宫的算法里，只要两个节点被打通，便可以双向遍历，有向图与无向图皆可。这种关系是通过 `Maze.SetConnect()` 与 `Maze.SetApart()` 来设定的，这两个方法会进一步调用 `Node.AddNeighbor()` 和 `Node.DelNeighbor()`。一个节点类的实例除了包含有 `current` 和 `neighbors` 两个属性，还多了一个 `visited` 用来表示该节点是否被访问过，这个属性靠 `Maze.SetVisited()` 来操控。以防万一，我还定义了墙类，用一面墙的两个端点坐标来定义渲染的起始点和终点，虽然没啥用途。

此外我们需要两种不同的方式渲染迷宫：一种是在相互打通的节点之间渲染通道（我把它称作*基于节点的渲染*），另一种则是在未打通的节点之间渲染一面墙（我把它称作*基于墙的渲染*）。在本文讨论的四种算法里，深度优先遍历和普里姆算法的初始状态要求所有节点互不联通，其每一步状态更适合用基于节点的渲染；递归分割的初始状态是所有相邻节点都相互联通的图（没有墙），适合用基于墙的方式渲染。

原则上讲，并不存在某一种生成过程必须用哪种渲染，但合适的渲染方式让一个算法更易于理解。至少在前两种算法里，节点属性 `visited` 是有用的，也可以不用，因为 “被访问过” 与 “建立连接” 是同一事实的两种表现。

为得到流畅的播放效果，需要把计算和渲染分开。也就是每对迷宫进行一步操作，就将当前状态生成一个深拷贝缓存起来。待所有生成步骤完成以后，再逐帧渲染。为此我们需要一个类来存储迷宫的每一步状态：
```python
class Frame(object):
    """Consists of a vector instance representing
    the operational unit and a Maze instance."""
    def __init__(self, head, maze):
        self.__head = head  # the cell that is leading the generation
        self.__maze = maze  # current status of the graph
    
    @property
    def head(self):
        return self.__head  # deepcopy() removed
    
    @property
    def maze(self):
        return self.__maze  # deepcopy() removed
```
同理用 A-Star 算法 [Du and Swamy, 2016] 求解后的路径生成过程也要用一个 `Path` 类来存储每一帧内容：
```python
class Path(object):
    """Data structure for storing each frame
    in the path generation animation."""
    def __init__(self, visited, path):
        self.__visited = visited
        self.__path = path
    
    @property
    def visited(self):
        return self.__visited  # deepcopy() removed
    
    @property
    def path(self):
        return self.__path  # deepcopy() removed
```
以上定义里面，`self.__head` 是最后被更新的节点坐标，`self.__maze` 是当前状态下的迷宫。前两个算法使用黑墙 + 白色通道的表示方法来演示生成过程，并使用一个红色的方块来表示新加入到迷宫中的单元；第三、四种算法的墙与通道的颜色刚好颠倒。以上这种表示方法，是受到了维基百科上某动画的启发，也是油管上许多大神的风格。如有相似，绝无盗用。

准备工作完毕，以下是技术细节。

#### —— 实现细节 ——

【网格设计】

为解释本文所有算法，先要定义几个名词。本着视觉上舒服的原则，我们把一块 600x400 像素的画布分割成 30x20 个大小为 20x20 像素的单元。画布尺寸由全局变量 `PYGAMEWIDTH` 与 `PYGAMEHEIGHT` 给出，单元格大小为 `CELLSIZE` 且必须能够整除画布的长和宽。在定义了以上变量以后，
```python
from itertools import product

X_GRID = range(0, PYGAMEWIDTH, CELLSIZE)
Y_GRID = range(0, PYGAMEHEIGHT, CELLSIZE)
```
便给出了每一个单元格左侧和下方两条线的位置，于是便有了两种渲染网格的方式。

最直接的方式是渲染单元格之间的边缘，
```python
def drawGrid():
    """渲染单元格边界"""
    for X in X_GRID:
        pygame.draw.line(screen, color_GRIDS, (X, 0), (X, PYGAMEHEIGHT))
    for Y in Y_GRID:
        pygame.draw.line(screen, color_GRIDS, (0, Y), (PYGAMEWIDTH, Y))
```
其效果如图：

![cell borders](/img/in-post/post-maze-algorithms/drawGrid_with_Title.png)

另一种是渲染单元格的中心，
```python
def drawCenters():
    """渲染单元格中心"""
    radius = int(0.1 * CELLSIZE)
    for X, Y in product(X_GRID, Y_GRID):
        x, y = X + CELLSIZE//2, Y + CELLSIZE//2
        pygame.draw.circle(screen, color_GRIDS, (int(x), int(y)), radius)
```
其效果如图所示：

![cell centers](/img/in-post/post-maze-algorithms/drawCenters_with_Title.png)

【深度优先遍历】
—— *生成的迷宫拥有狭长的通道，分支和主路容易辨认，角色扮演 (RPG) 类游戏里常见的地图生成算法*

深度优先 (DFS) 构建迷宫的思想就是，每次把新找到的未访问迷宫单元作为优先，寻找其相邻的未访问过的迷宫单元，直到所有的单元都被访问到。通俗的说，就是从起点开始随机走，走不通了就返回上一步，从下一个能走的地方再开始随机走。把维基百科上的算法套路总结如下：
1. 将起点作为当前迷宫单元并标记为已访问
2. 当还存在未标记的迷宫单元，进行循环
   1. 如果当前迷宫单元有未被访问过的的相邻的迷宫单元
      1. 随机选择一个未访问的相邻迷宫单元
      2. 将当前迷宫单元入栈
      3. 移除当前迷宫单元与相邻迷宫单元的墙
      4. 标记相邻迷宫单元并用它作为当前迷宫单元
   2. 如果当前迷宫单元不存在未访问的相邻迷宫单元，并且栈不空
      1. 栈顶的迷宫单元出栈
      2. 令其成为当前迷宫单元

很多文章都指出了 DFS 这种算法生成的迷宫难度不大，得出这种结论，都假设玩家能看到俯视图。事实表明 A-Star 算法在搜索路径的过程中，在这种迷宫上面花费的时间是最不确定的。一旦进入有 dead end 的支路，回溯需要比其它类型的迷宫更长的时间。一个看不到俯视图的玩家走迷宫的策略无异于计算机查找路径的算法（不走到头，怎么知道该返回呢？），可见 DFS 迷宫其实是挺费时间的。依我看，每一种类型的迷宫都只能适用某一种特定的游戏，没法说哪种更难。

渲染效果如图：

![DFS](/img/in-post/post-maze-algorithms/DFS_MiniTool.gif)

【普里姆算法】
—— *生成的迷宫拥有大量细小的分支，形态更加自然，常见于标准的迷宫游戏*

要解释普里姆 (Prim) 算法，就不得不提一下最小生成树 [Greenberg, 1998] 原理。
> 公司里有几台电脑。只要两台之间形成通路（途经其它电脑也算），就可以相互通信。求让所有电脑都能通信之最省线缆的连法？

这一类问题的解答被称为*最小生成树*，并把该问题称作一个*最小生成树问题*。其数学的表述如下：
> 最小生成树是一副连通加权无向图中一棵权值最小的生成树。

从定义看出，最小生成树是最小权值生成树的简称。当图的每一条边的权值都相同时，该图的所有生成树都是最小生成树。如果图的每一条边的权值都互不相同，那么最小生成树将只有一个。

在求解最小生成树问题的两种算法里，Prim 算法 [Ramadhan et al., 2018] 的时间复杂度与所用数据结构有关 —— 用优先队列实现出来的时间复杂度为 O[E logE]，用斐波那契堆实现出来的则是 O[E+VlogV]，其中 E 为图的边数、V 为顶点个数。好了，那怎么用这种算法来生成迷宫呢？

一般的算法套路 [Kozlova et al., 2015] 为：
1. 让迷宫全是墙.
2. 选一个单元格作为迷宫的通路，然后把它的邻墙放入列表
3. 当列表里还有墙时
   1. 从列表里随机选一个墙，如果这面墙分隔的两个单元格只有一个单元格被访问过
      1. 那就从列表里移除这面墙，即把墙打通，让未访问的单元格成为迷宫的通路
      2. 把这个格子的墙加入列表
   2. 如果墙两面的单元格都已经被访问过，那就从列表里移除这面墙

原始套路里把墙放入一个列表里面进行维护的做法不太方便，维基百科提出了改进建议：
> 我们可以维护一个迷宫单元格的列表，而不是边的列表。在这个迷宫单元格列表里面存放了未访问的单元格，我们在单元格列表中随机挑选一个单元格，如果这个单元格有多面墙联系着已存在的迷宫通路，我们就随机选择一面墙打通。这会比基于边的版本分支稍微多一点。

渲染效果如图：

![Prim](/img/in-post/post-maze-algorithms/Prim_MiniTool.gif)

【递归分割】
—— *分治法的一种，四种生成算法里，原理最简单的一个*

递归分割 [Buck, 2011] 是本文所提四种算法中唯一不需要对节点属性 `visited` 进行修改的方法。它要求初始状态下，所有相邻节点均处于打通的状态。其算法套路如下：
1. 选择一个点 (`pivot`) 将画布划分为四个区域，可以从定义单元格边界的数组 `X_GRID` 与 `Y_GRID` 随机选择，但是必须要跳过第一个值。
2. 从 `pivot` 出发往东、西、南、北四个方向，延伸出四条分割线，随机挑选其中的三个设置开口。开口宽度为一个单元格大小。
3. 将西北、东北、西南、东南四个子区域的迷宫（即表示图的数据结构）作为参数传递给同一函数。当子区域迷宫宽度（或高度）只有一个单元格大小，触发 base case，递归调用结束。
4. 当四个子区域的递归调用都回溯到最浅一层后，返回当前状态的迷宫。

推导时间复杂度是 CS 中较难的部分，分治方法被用于归并排序、快速排序和许多并行设计的场景。在快速排序里面，如果 `pivot` 的值和数组的最小或最大值靠得太近，就不能够很好地发挥分治方法的加速效果。

类似地，假设对一个 MxN 的迷宫，`pivot` 的坐标刚好离画布的某一角有一个单元格的距离。在这种最坏的情形下，会得到一个大小为 (M-1)(N-1) 的子问题（四个子区域中最大的）。如果我们每一步递归调用都假设最坏的情况，如此继续下去，得到的时间复杂度上限为 O[M<sup>2</sup>N<sup>2</sup>]。用节点个数表示，那就是 O[V<sup>2</sup>]。

经试验，每一次分割以后，挖洞的墙壁数量不少于三个时才能保证不生成大量封闭的区域。开四个洞会形成环路，不满足 “任意两点间的路径唯一” 的要求。

渲染效果如图：

![Recursive Division](/img/in-post/post-maze-algorithms/Divide_MiniTool.gif)

【克鲁斯卡尔算法】
—— *需要自定义数据结构来加速计算过程，四种算法里，生成速度最快*

之所以要把这种算法放在最后才讲，是因为
1. 需要定义新的数据结构 —— 并查集 (Disjoint Set)
2. 初始化过程中需要同时生成墙和节点

上面的三种算法要么只需要墙、要么只需要节点。

在 CS 里，定义数据结构无非只有两种需求：
1. 它可以让已有的版本再快一点儿（不定义也能解决）
2. 它是针对你所需要解决的问题的唯一实现方法

并查集在迷宫生成问题里的应用主要还是出于效率上的考虑，事实上第二点原因我目前还没遇见过。包括前面定义的迷宫类、墙类、节点类，没它们算法一样也能实现。只是那样做代码的可读性大幅降低，增加了维护成本。

话虽这么说，有些时候看似最适合某个问题的数据结构，却不是最高效的实现方法（参见[约瑟夫环问题](https://cp-algorithms.com/others/josephus_problem.html)）。

一个并查集本质上就是由集合组成的列表，每一个集合需要一个父元素（可以用列表索引）方便查找。但如果不封装任何方法，全靠列表自带方法，实现起来不够优雅，定义专属的数据结构显得尤为必要。此处只提供框架：
```python
class DisjointSet(object):
    """Disjoint set data structure."""
    
    def __init__(self, dset=None):
        """Two ways of initialization."""
        if dset is None:  # not provided
            dset = []
        self.__sets = dset
    
    @property
    def sets(self):
        return self.__sets
    
    def InsertSet(self, new):
        pass  # 此处略去一些代码
    
    def LocateSet(self, target):
        pass  # 此处略去一些代码
    
    def RemoveSet(self, value):
        pass  # 此处略去一些代码
    
    def AreSeparated(self, v_1, v_2):
        pass  # 此处略去一些代码
    
    def QuickFind(self, v_1, v_2):
        i, j = None, None
        for k, set_ in enumerate(self.__sets):
            if v_1 in set_: i = k
            if v_2 in set_: j = k
            if i is not None and j is not None:
                return i, j
        raise Exception("At least one element is not found!")
    
    def JoinSets(self, v_1, v_2):
        pass  # 此处略去一些代码
    
    def __len__(self):
        return len(self.__sets)
    
    def __str__(self):
        output = 'Sets:\n'
        for set_ in self.__sets:
            output += (str(set_) + '\n')
        return output
```
`QuickFind()` 的实现方法，需要考虑路径压缩等问题。这个方法写好了，所有涉及到两个集合操作的方法效率都能提高。`__len__()` 这个方法允许我们对算法终止条件进行检查。此外在迷宫类里我们需要补充定义 `InsertWall()` 和 `RemoveWall()`。

尽管也是最小生成树的一种算法，与 Prim 算法不同的是，要表示 Kruskal 的每一步状态却需要基于墙方式的渲染。其算法套路如下：
1. 创建所有墙的列表，并且创建所有单元的集合，每个集合中只包含一个单元。
2. 以随机的顺序选取每一个墙：
   * 如果被当前的墙分隔的两个单元属于不同的集合：
     1. 去除当前的墙。
     2. 把单元加入到之前分隔的单元。

不难证明，要想保证生成的迷宫满足连通性和无环路两个要求，推倒的墙数量刚好比单元格数量少一个。

渲染效果如图：

![Kruskal](/img/in-post/post-maze-algorithms/Kruskal_MiniTool.gif)

【关于渲染】

虽然 Python 一向以低执行效率著称，不过在看到一些大牛制造的产品之后，不得不承认用 Python 制作出来 2D Graphics 是最漂亮的。这迫使我们把算法与数据结构都优化得非常到位。

如果用有向图代表迷宫，那么遍历图中每一个节点，渲染它与联通状态下的节点之间的通道，是没有问题的做法。对于无向图，这种做法会造成大量重复的渲染。如果用一个 `rendered = []` 来存储已经渲染过的节点，每遍历一个节点都需要检查这个条件，那么随着长度的增加，判断数组元素是否存在会越来越慢。我们需要精巧地设计一个规则让程序在遍历的时候跳过一些单元格，无需借助额外的变量或数组就可以做到。

最后我想到了通过坐标反向求取索引、再判断奇偶性的方法：
```python
def isStaggered(node):
    """Determines whether a node has
    different row and column parities."""
    
    # obtain grid coordinate (the ll corner)
    cell_x, cell_y = node.current.x, node.current.y
    grid_x, grid_y = cell_x - CELLSIZE//2, cell_y - CELLSIZE//2
    
    # locate them from the grid arrays
    index_x, index_y = X_GRID.index(grid_x), Y_GRID.index(grid_y)
    
    return (index_x + index_y) % 2
```
由于都是加减乘除运算，对于每一个节点，调用 `isStaggered()` 的时间可以忽略不计。我们在函数 `drawMazeNodes()` 与 `drawMazeWalls()` 的定义里筛选出需要渲染的节点并使用列表生成式 (List Comprehensions) 和生成器 (Generators) 来加速，乐观地期待，渲染每一帧的时间可以缩短到原来的一半。详见 `Maze.algorithms`。

#### —— 附录 ——

A-Star 算法搜索四种迷宫的平均用时、总移动步数和唯一路径长度：
```
|  Maze     | Time [sec] |   CV   |   UP   | Efficiency [%] |
|:---------:|:----------:| ------:| ------:|:--------------:|
|  DFS      |  0.389677  | 250.8  | 173.8  |    69.2982     |
|  Prim     |  0.107477  | 102.8  |  55.0  |    53.5019     |
|  Division |  0.538985  | 278.2  |  91.4  |    32.8541     |
|  Kruskal  |  0.234977  | 219.2  |  71.0  |    32.3905     |
```
* CV: the number of cells visited by A-Star Search
* UP: the number of cells in the unique path

此处的效率为路径查找效率而非时间效率，可通过 UP/CV x 100% 计算出。

【参考文献】
* Buck, J. (2011). Maze generation: Recursive division.
* Du, K. L., & Swamy, M. N. S. (2016). Search and optimization by metaheuristics. Techniques and Algorithms Inspired by Nature; Birkhauser: Basel, Switzerland.
* Greenberg, H. J. (1998). Greedy algorithms for minimum spanning tree. University of Colorado at Denver.
* Kozlova, A., Brown, J. A., & Reading, E. (2015, August). Examination of representational expression in maze generation algorithms. In 2015 IEEE Conference on Computational Intelligence and Games (CIG) (pp. 532-533). IEEE.
* Ramadhan, Z., Siahaan, A. P. U., & Mesran, M. (2018, July). Prim and Floyd-Warshall Comparative Algorithms in Shortest Path Problem. In Proceedings of the Joint Workshop KO2PI and The 1st International Conference on Advance & Scientific Innovation (pp. 47-58).
