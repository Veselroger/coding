# [←](../../README.md) <a id="home"></a> Trees: Depth-First Search (DFS)

Данный раздел посвящён задачам на деревья с использованием подхода **Depth-First Search**.\
Дорожная карта от NeetCode: [NeetCode Roadmap](https://neetcode.io/roadmap).\
Задачи на LeetCode: **"[Depth-First Search](https://leetcode.com/problem-list/depth-first-search/)"**.\
Плэйлист от NeetCode: **"[Trees](https://www.youtube.com/watch?v=QfJsau0ItOY&list=PLot-Xpze53ldg4pN6PfzoJY7KsKcxF1jg)"**.\
Данные задачи входят в том числе в **[Leetcode Patterns](https://seanprashad.com/leetcode-patterns/)**.


**Table of Contents:**
- [[144] Binary Tree Preorder Traversal](#preorder)
- [[297] Serialize and Deserialize Binary Tree](#serialize)
- [[100] Same tree](#sameTree)
- [[572] Subtree of Another Tree](#subtree)
- [[101] Symmetric Tree](#symmetric)
- [[226] Invert Binary Tree](#invertTree)
- [[617] Merge Two Binary Trees](#merge)
- [[814] Binary Tree Pruning](#pruning)
- [[112] Path Sum](#pathSum)
- [[104] Maximum Depth of Binary Tree](#maxDepthDFS)
- [[543] Diameter of Binary Tree](#diameter)
- [[110] Balanced Binary Tree](#balanced)
- [[257] Binary Tree Paths](#treePaths)
- [[94] Binary Tree Inorder Traversal](#inorder)
- [[145] Binary Tree Postorder Traversal](#postorder)
- [[1448] Count Good Nodes in Binary Tree](#goodNodes)
- [[105] Construct Binary Tree From Preorder And Inorder Traversal](#construct)
- [[106] Construct Binary Tree from Inorder and Postorder Traversal](#construct2)
- [[606] Construct String from Binary Tree](#constructString)
- [[124] Binary Tree Maximum Path Sum](#maxPathSum)
- [[114] Flatten Binary Tree to Linked List](#flatten)

----

## [↑](#home) <a id="preorder"></a> 144. Binary Tree Preorder Traversal
Рассмотрим задачу [144. Binary Tree Preorder Traversal](https://leetcode.com/problems/binary-tree-preorder-traversal):
> Дано дерево. Нужно вернуть его элементы в preorder порядке

Разбор задачи от NeetCode: [Binary Tree Preorder Traversal (Iterative)](https://www.youtube.com/watch?v=afTpieEZXck)

Базовая задача для понимания **Preorder Traversal** - порядка, при котором сначала идёт root, потом left, потом right.\
Данная тактика может быть полезна в различных задачах. Например: сравнение деревьев.

Рекурсивное решение очень простое: каждая нода добавляет в результат себя.\
А затем просит тоже самое сделать сначала левый элемент, а потом правый.

Выглядит это следующим образом:

![](../img/trees/PreorderTraversal.gif)

Рекурсивное решение:
```java
public void preorderDFS(TreeNode root, List<Integer> result) {
    if (root == null) return; // Do nothing without node
    result.add(root.val);               // Handle node
    preorderDFS(root.left, result);     // Handle left
    preorderDFS(root.right, result);    // Handle right
}
    
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    preorderDFS(root, result);
    return result;
}
```

Можно выполнить и без рекурсии. Нам понадобится наши задачи складывать в стэк.\
Одну задачу мы выполняем сразу (обработка текущей ноды).\
Другую задачу мы откладываем на потом (обработка правой ноды).\
Другую задачу мы кладём на верх стопки, чтобы с неё начать.

```java
public List<Integer> preorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;

    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root);

    while(!stack.isEmpty()) {
        TreeNode node = stack.pop();
        result.add(node.val);
        if (node.right != null) stack.push(node.right);
        if (node.left != null) stack.push(node.left);
    }
    return result;
}
```

----

## [↑](#home) <a id="serialize"></a> 297. Serialize and Deserialize Binary Tree
Рассмотрим задачу [297. Serialize and Deserialize Binary Tree](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/):
> Нужно написать два метода: создание дерева из строки и создание строки из дерева.

Разбор задачи от NeetCode: [Serialize and Deserialize Binary Tree - Preorder Traversal](https://www.youtube.com/watch?v=u4JAi2JJhI8)

Сериализация дерева в строку по аналогии с задачей [Binary Tree Preorder Traversal](#preorder).\
У нас есть некоторый контейнер для результата (т.к. строка, то StringBuilder).\
На каждом вызове мы добавляем себя в результат, а потом делаем тоже самое для left и для right.

Таким образом превращение в строку выглядит следующим образом:
```java
private void buildStringFromTree(TreeNode node, StringBuilder str) {
    str.append(",");
    if (node == null) {
        str.append("N");
        return;
    }; 
    str.append(node.val);
    buildStringFromTree(node.left, str);
    buildStringFromTree(node.right, str);
}

// Encodes a tree to a single string.
public String serialize(TreeNode root) {
    if (root == null) return "";
    // We should construct string, it means do should build it step by step:
    StringBuilder str = new StringBuilder();
    buildStringFromTree(root, str);
    return str.substring(1);
}
```

Далее превратим строку в дерево.\
Делаем так же рекурсивно. Каждый заход имеет свой индекс обрабатываемого элемента в строке.\
Каждый заход инкрементирует индекс для следующего захода, а только потом обрабатывает.

Решение может выглядеть следующим образом:
```java
// Decodes your encoded data to tree.
public TreeNode deserialize(String data) {
    String[] preorder = data.split(",");
    if (preorder == null || preorder.length == 0) return null;
    // We have enough information. Lets create a tree
    return buildTreeFromString(preorder, new AtomicInteger(0));
}

private TreeNode buildTreeFromString(String[] preorder, AtomicInteger index) {
    String value = preorder[index.get()];   // Read value under the pointer
    index.incrementAndGet();                // Prepare pointer for next iteration 

    if (value.equals("N") || value.equals("")) return null;   // N means NULL
    
    // Non null node
    TreeNode node = new TreeNode(Integer.valueOf(value));
    
    // Do the same for left and right
    node.left = buildTreeFromString(preorder, index);
    node.right = buildTreeFromString(preorder, index);
    return node;
}
```

----

## [↑](#home) <a id="sameTree"></a> 100. Same tree
Рассмотрим задачу "[100. Same Tree](https://leetcode.com/problems/same-tree/)":
> Дано два дерева. Нужно определить, одинаковы ли они (по структуре и значениям). 

![](../img/trees/SameTree.png)

Разбор задачи от NeetCode: **"[Same tree](https://www.youtube.com/watch?v=vRbbcKXCxOw)"**.

Отличная задача на понимание того, как можно проходить деревья.

Один из самых простых способов - обход в глубину, он же **Depth-First Search**.\
При этом решение может быть рекурсивным:
- берём ноды из первого дерева и из второго
- проверяем ноду из одного дерева (**first**) и ноду из другого (**second**)
- проверяем поддеревья слева
- проверяем поддеревья справа

```java
public boolean isSameTree(TreeNode first, TreeNode second) {
    // Case #1: both nodes are null. It means that these places inside trees are equals
    if (first == null && second == null) return true;
    // Case #2: node is absent ONLY in one tree. OR values are different. NOT the same tree.
    if (first == null || second == null || first.val != second.val) return false;
    // Delegate the same check to left part AND to right part. Recursively.
    return isSameTree(first.left, second.left) && isSameTree(first.right, second.right);
}
```

Интересно, что DFS решение может быть не рекурсивным. Для этого нам понадобится стэк:
```java
Deque<TreeNode> stack = new ArrayDeque<>();
```

Однако, данная структура данных в Java не позволяет добавлять NULL. Придётся создать специальную TreeNode на этот случай:
```java
private static final TreeNode NULL_NODE = new TreeNode();
private TreeNode node(TreeNode node) {
    if (node == null) return NULL_NODE;
    return node;
}
```

Логика в этом случае будет выглядеть следующим образом:

![](../img/trees/SameTree.gif)

Тогда:
```java
public boolean isSameTree(TreeNode first, TreeNode second) {
    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push( node(first) );
    stack.push( node(second) );
```

Остаётся лишь работать до тех пор, пока в стэке есть ноды для обработки:
```java
while (!stack.isEmpty()) {
    TreeNode left = stack.pop();
    TreeNode right = stack.pop();

    if (left == NULL_NODE && right == NULL_NODE) continue; // the same
    if (left == NULL_NODE || right == NULL_NODE) return false; // not the same
    if (left.val != right.val) return false;

    // Right side goes later
    stack.push( node(left.right) );
    stack.push( node(right.right) );
    // left side goes first
    stack.push( node(left.left) );
    stack.push( node(right.left) );
}
return true;
```

----

## [↑](#home) <a id="subtree"></a> 572. Subtree of Another Tree
Рассмотрим задачу "[572. Subtree of Another Tree](https://leetcode.com/problems/subtree-of-another-tree/)":
> Дано два дерева. Нужно определить, является ли одно дерево поддеревом другого. 

Разбор задачи от NeetCode: **"[Subtree of Another Tree](https://www.youtube.com/watch?v=E36O5SWp-LE)"**.

Данная задача строится на той же логике, что и [Same tree](#sameTree).\
Вспомним оригинальное решение:
```java
public boolean isSameTree(TreeNode first, TreeNode second) {
    if (first == null && second == null) return true; // both null
    if (first == null || second == null || first.val != second.val) return false; // only one null OR have diff values
    // The same logic for LEFT and RIGHT subtrees
    return isSameTree(first.left, second.left) && isSameTree(first.right, second.right);
}
```

Переиспользуем данный подход:
```java
public boolean isSubtree(TreeNode source, TreeNode target) {
    if (source == null) return false; // Can't find subtree without source tree
    if (isSameTree(source, target)) return true; // Check if root is the subtree
    return isSubtree(source.left, target) || isSubtree(source.right, target); // Check if left part or right part is subtree
}
```

Как вариант - превратить два дерева в строковое представление и искать вхождение target в source.

----

## [↑](#home) <a id="symmetric"></a> 101. Symmetric Tree
Рассмотрим задачу "[101. Symmetric Tree](https://leetcode.com/problems/symmetric-tree/)":
> Дано дерево. Нужно понять, симметричное ли оно.

Разбор задачи от NeetCode: [Symmetric Tree](https://www.youtube.com/watch?v=Mao9uzxwvmc)

Задача похожа на задачу [Same tree](#sameTree).\
Несмотря на то, что нам дано лишь одно дерево, мы рассматриваем его левую часть и правую:
```java
public boolean isSymmetric(TreeNode root) {
    return isSymmetric(root.left, root.right);
}
```

Таким образом получаем следующую картину:

![](../img/trees/Symmetric.png)

Но каждый вызов **isSymetric** происходит для элементов с разных сторон, т.е. симметрично:
```java
public boolean isSymmetric(TreeNode left, TreeNode right) {
    if (left == null && right == null) return true;
    if (left == null || right == null || left.val != right.val) return false;
    return isSymmetric(left.left, right.right) 
        && isSymmetric(left.right, right.left);
}
```

Итератиное решение тоже практически такое же.\
На этот раз воспользуемся LinkedList, т.к. он позволяет хранить null:
```java
public boolean isSymmetric(TreeNode root) {
    Deque<TreeNode> stack = new LinkedList<>(); // (!) Do not use poll
    stack.push(root.left);
    stack.push(root.right);
    while (!stack.isEmpty()) {
        TreeNode left = stack.pop();
        TreeNode right = stack.pop();

        if (left == null && right == null) continue;      // the same
        if (left == null || right == null || left.val != right.val) return false;  // not the same

        // Inner side goes later
        stack.push(left.right);
        stack.push(right.left);
        // Outer side goes first
        stack.push(left.left);
        stack.push(right.right);
    }
    return true;
}
```

----

## [↑](#home) <a id="invertTree"></a> 226. Invert Binary Tree
Рассмотрим задачу "[226. Invert Binary Tree](https://leetcode.com/problems/invert-binary-tree/)":
> Дан узел, который является корнем дерева. Нужно инвертировать дерево и вернуть новый корень.

Инвертировать означает поменять местами для каждого узла его левый и правый дочерние элементы:

![](../img/trees/invertTree.png)

Разбор задачи от NeetCode: **"[Invert Binary Tree - Depth First Search](https://www.youtube.com/watch?v=OnSn2XEQ4MY)"**.\
Ещё более подробный разбор: **"[Nikhil Lohia: Invert Binary Tree](https://www.youtube.com/watch?v=ck23lNqbLjI)"**

Рекурсивное решение звучит логично:\
Чтобы инвертировать ноду, нужно её левый и правый элемент поменять местами.\
Чтобы поменять местами элементы, нужно одну часть отсоединить (**detach**), другую поставить на её место, а потом присоединить detached часть обратно, но уже с другой стороны:

```java
public TreeNode invertTree(TreeNode node) {
    if (node == null) return null;
    // Invert node means swap left and right
    TreeNode detached = node.left;
    node.left = node.right;
    node.right = detached;
    invertTree(node.left);
    invertTree(node.right);   
    return node;   
}
```

Т.к. мы использовали рекурсивный DFS, то можно его переписать и итеративно:
```java
public TreeNode invertTree(TreeNode root) {
    if (root == null) return null;
    Deque<TreeNode> stack = new ArrayDeque<>();
    stack.push(root); // put root to the stack
        
    while (!stack.isEmpty()) {
        TreeNode element = stack.pop();
        TreeNode detached = element.left;
        element.left = element.right;
        element.right = detached;
        // Push left and right nodes to the stack (if not null, i.e. we can do some work)
        if (element.right != null) stack.push(element.right);
        if (element.left != null) stack.push(element.left);
    }
    return root;  
}
```

Чтобы оценить сложность нужно сформулировать правильно описание решения: для каждой ноды поменять местами левую и правую стороны. Для каждой ноды, то есть для каждого n. Получается, что сложность алгоритма будет O(n).

----

## [↑](#home) <a id="merge"></a> 617. Merge Two Binary Trees
Рассмотрим задачу "[617. Merge Two Binary Trees](https://leetcode.com/problems/merge-two-binary-trees/)":
> Дано два дерева. Нужно сложить их элементы.

Разбор задачи от NeetCode: **"[Merge Two Binary Trees](https://www.youtube.com/watch?v=QHH6rIK3dDQ)"**.

Ещё одна задача, похожая на [Same tree](#sameTree).\
Главное понять, что по сути мы пишем BiFunction, где на входе у нас 2 изначальных ноды, а мы возвращаем новую.

Код решения:
```java
public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {
    // When we can't do nothing? When both nodes are null
    if (root1 == null && root2 == null) return null;

    // Create a new Node and merge current root1 and root2 nodes
    int root1Val = root1 != null ? root1.val : 0;
    int root2Val = root2 != null ? root2.val : 0;
    TreeNode root = new TreeNode(root1Val + root2Val);
        
    // Do the same for other parts
    root.left = mergeTrees(root1 != null ? root1.left : null,
                            root2 != null ? root2.left : null);
    root.right = mergeTrees(root1 != null ? root1.right : null, 
                            root2 != null ? root2.right : null);
    return root;
}
```

----

## [↑](#home) <a id="pruning"></a> 814. Binary Tree Pruning
Рассмотрим задачу [814. Binary Tree Pruning](https://leetcode.com/problems/binary-tree-pruning/):
> Дано двоичное дерево из нод нулей или единиц. Нужно удалить те ноды, поддеревья которых не содержат единиц.

Разбор задачи от Nick White: [Binary Tree Pruning Explained](https://www.youtube.com/watch?v=77LJc56bwnE)

![](../img/trees/Prune.gif)

Как мы видим, прежде чем нода решает, остаётся она в дереве или нет она спрашивает тоже самое у своих поддеревьев.\
Если они не остались и если нода не содержит единицу, тогда нода возвращает null, чтобы удалить себя из дерева.

Код решения:
```java
public TreeNode pruneTree(TreeNode root) {
    if (root == null) return root;
    // Before decision we should check left subtree and right subtree
    // To decide - check left subtree and right subtree
    root.left = pruneTree(root.left);
    root.right = pruneTree(root.right);

    // Leave node (i.e do not delete it) if we are "one"
    // Or if our subtree has one
    if (root.val == 1 || root.left != null || root.right != null) {
        return root;
    }
    // If not - return null that means delete this node from the tree
    return null;
}
```

----

## [↑](#home) <a id="pathSum"></a>112. Path Sum
Рассмотрим задачу "[112. Path Sum](https://leetcode.com/problems/path-sum/)":
> Дано дерево и некоторое значение. Нужно ответить, есть ли такой путь от рута к листу, сумма элементов которого равна этому значению.

Разбор задачи от NeetCode: **"[Path Sum](https://www.youtube.com/watch?v=LSKQyOz_P8I)"**.

Рассматривая ноду, мы можем суммировать уже увиденную сумму, некоторую **curSum**.\
Если у нас есть или левый или правый дочерний элемент - мы не можем остановиться и должны проверить эти пути.\
Если у нас нет дочерних элементов - мы конечный элемент пути, должны проверить curSum и targetSum.

Для начала, из изначального метода вызовем такой-же, но с информацией о накопленной сумме:
```java
public boolean hasPathSum(TreeNode root, int targetSum) {
    return hasPathSum(root, targetSum, 0);
}
```

А в нашем методе выполним вычисление:
```java
private boolean hasPathSum(TreeNode node, int targetSum, int curSum) {
    // Null node can't have the target sum
    if (node == null) return false;
        
    curSum = curSum + node.val; // Sum including the current node
    // No children == leaf node, the current sum is the final result for this current branch
    if (node.left == null && node.right == null) {
        return curSum == targetSum;
    } else {
        // Has children - ask them. One of them should return true
        return hasPathSum(node.left, targetSum, curSum) || hasPathSum(node.right, targetSum, curSum);
    }
}
```

----

## [↑](#home) <a id="maxDepthDFS"></a> 104. Maximum Depth of Binary Tree
Рассмотрим задачу "[104. Maximum Depth of Binary Tree](https://leetcode.com/problems/maximum-depth-of-binary-tree/)":
> Дано дерево. Нужно найти его максимальную глубину, т.е. длину пути состоящего из бОльшего кол-ва элементов.

Разбор задачи от NeetCode: **"[Maximum Depth of Binary Tree](https://www.youtube.com/watch?v=hTM3phVI6YQ)"**.

Решить задачу можно рекурсивно при помощи **DFS** подхода:

![](../img/trees/DFS.png)

Задача сводится к тому, что каждая нода добавляет себя как "+1" к результату, т.к. она есть в пути. А результат спрашивает у своих дочерних элементов. Причём каждую ноду интересует лишь максимальный из результатов от дочерних элементов:
```java
public int maxDepth(TreeNode root) {
    if (root == null) return 0;
    int left = maxDepth(root.left);
    int right = maxDepth(root.right);
    // Add itself as +1 to the best result
    return 1 + Math.max(left, right);
}
```

Рекурсивный DFS можно переписать в итеративном стиле:
```java
record Call(TreeNode node, int depth){}

public int maxDepth(TreeNode root) {
    if (root == null) return 0;

    Deque<Call> stack = new ArrayDeque<>();
    stack.push(new Call(root, 1)); // Root has depth 1

    int maxDepth = 0;
    while (!stack.isEmpty()) {
        Call entry = stack.pop();
            
        int depth = entry.depth();
        maxDepth = Math.max(maxDepth, depth);

        TreeNode node = entry.node();
        if (node.left != null) stack.push(new Call(node.left, depth + 1));
        if (node.right != null) stack.push(new Call(node.right, depth + 1));
    }
    return maxDepth;
}
```

----

## [↑](#home) <a id="diameter"></a> 543. Diameter of Binary Tree
Рассмотрим задачу "[543. Diameter of Binary Tree](https://leetcode.com/problems/diameter-of-binary-tree/)":
> Дано дерево. Нужно найти его диаметр, то есть максимальную длину пути между двумя элементами.

Отличная задача чтобы понять как один взгляд на дерево помогает увидеть что-то ещё.\
Чтобы решить данную задачу нужно вспомнить задачу [Maximum Depth of Binary Tree](#maxDepthDFS).

Хоть эта задача и Easy, но для решения нужно увидеть, что мы должны учитывать следующее:
1) На каждом элементе максимальный путь == сумме длин самых длинных путей слева И справа
2) Поднимаясь вверх по дереву мы продолжаем рассматривать только самый длинный путь из дочерних элементов
3) Высота считает количество узлов, а ширина - количество соединений/линий!

![](../img/trees/DiameterBinaryTree.png)

Таким образом, наше видоизменённое решение будет выглядеть так:
```java
class Solution {
    public int ans = 0;

    public int diameterOfBinaryTree(TreeNode root) {
        maxDepth(root);
        return ans;
    }

    public int maxDepth(TreeNode node) {
        if (node == null) return 0;
        int left = maxDepth(node.left);
        int right = maxDepth(node.right);
        // We know length of left and right paths
        ans = Math.max(ans, left + right);
        // Longest path == 1 (for itself) + the longest child
        return 1 + Math.max(left, right);
    }
}
```

Разбор задачи от Xavier Elon: **"[Diameter of a Binary Tree](https://www.youtube.com/watch?v=DcDjYOsjSlg)"**.

----

## [↑](#home) <a id="balanced"></a> 110. Balanced Binary Tree
Рассмотрим задачу "[110. Balanced Binary Tree](https://leetcode.com/problems/balanced-binary-tree/)":
> Дано дерево. Нужно определить, сбалансированное оно по высоте или нет. Дерево сбалансировано тогда, когда глубина поддеревьев для каждого элемента не отличается больше, чем на единицу.

Разбор задачи от NeetCode: **"[Balanced Binary Tree](https://www.youtube.com/watch?v=QfJsau0ItOY)"**.

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) return true;
        return height(root) != -1;   
    }

    public int height(TreeNode node) {
        if (node == null) return 0;
        int left = height(node.left);
        int right = height(node.right);

        // Unbalanced state was determined
        if (left == -1 || right == -1) return -1;
        // Check height difference
        if (Math.abs(left - right) > 1) return -1;
        
        return 1 + Math.max(left, right);
    }
}
```

----

## [↑](#home) <a id="treePaths"></a> 257. Binary Tree Paths
Рассмотрим задачу "[257. Binary Tree Paths](https://leetcode.com/problems/binary-tree-paths/)":
> Дано дерево. Нужно вернуть все пути от рута до листа в виде строк вида "1->2->5" в любом порядке.

Разбор задачи от Nick White: [Binary Tree Paths](https://www.youtube.com/watch?v=H2D4HcVZq_g)

Задача главного метода - проверить входные данные и предоставить начальную строку. Это позволит избежать обработку наличия или отсутствия "->" перед первым элементом: 
```java
public List<String> binaryTreePaths(TreeNode root) {
    List<String> result = new ArrayList();
    if (root == null) return result;
    dfs(root, String.valueOf(root.val), result);
    return result;
}
```

Ну а дальше привычный нам dfs:
```java
private void dfs(TreeNode node, String path, List<String> result) {
    if (node == null) return; // Can't do anything without node
    // It's leaf
    if (node.left == null && node.right == null) {
        result.add(path);
        return;
    }
    // Has children
    if (node.left != null) dfs(node.left, path + "->" + String.valueOf(node.left.val), result);
    if (node.right != null) dfs(node.right, path + "->" + String.valueOf(node.right.val), result);
}
```

----

## [↑](#home) <a id="inorder"></a> 94. Binary Tree Inorder Traversal
Рассмотрим задачу "[94. Binary Tree Inorder Traversal](https://leetcode.com/problems/binary-tree-inorder-traversal/)".

Разбор задачи от NeetCode: [Binary Tree Inorder Traversal](https://www.youtube.com/watch?v=g_S5WuasWUE)

Как всегда, разделим логику на главный метод и вспомогательный:
```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    inorderTraversal(root, result);
    return result;
}
```

Наш вспомогательный метод:
```java
private void inorderTraversal(TreeNode node, List<Integer> result) {
    if (node == null) return;
    // "In Order" means that left child should go first
    if (node.left != null) inorderTraversal(node.left, result);
    result.add(node.val);
    // "In Order" means that right child should go last
    if (node.right != null) inorderTraversal(node.right, result);
}
```

Есть ещё итеративное решение при помощи стэка.\
Разбор можно посмотреть у Tushar Roy: **[Iterative Inorder Traversal of Binary Tree](https://www.youtube.com/watch?v=nzmtCFNae9k)**

Код:
```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;
        
    Deque<TreeNode> stack = new ArrayDeque<>();
    TreeNode cur = root;
    while(cur != null || !stack.isEmpty()) {
        if (cur != null) {
            stack.addLast(cur);
            cur = cur.left;
        } else {
            // Return through the stack
            cur = stack.removeLast();
            result.add(cur.val);
            cur = cur.right;
        }  
    }
    return result;
}
```

Ещё одно итеративное решение основано на том факте, что мы всегда стараемся максимально глубоко опуститься по левой стороне:
```java
public List<Integer> inorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    Deque<TreeNode> stack = new ArrayDeque<>();
    
    TreeNode cur = root;
    while(cur != null || !stack.isEmpty()) {
        // Go down to the left
        while (cur != null) {
            stack.addLast(cur);
            cur = cur.left;
        }
        cur = stack.removeLast();
        // We are in the middle of L -> Parent -> R
        result.add(cur.val);
        // Try to check right part
        cur = cur.right;
    }
    return result;
}
```

----

## [↑](#home) <a id="postorder"></a> 145. Binary Tree Postorder Traversal
Рассмотрим задачу "[145. Binary Tree Postorder Traversal](https://leetcode.com/problems/binary-tree-postorder-traversal/)".

Разбор задачи от NeetCode: [Binary Tree Postorder Traversal](https://www.youtube.com/watch?v=QhszUQhGGlA)

Рекурсивное решение такое же, как и у [Inorder Traversal](#inorder), только родитель добавляется после left и right.\
Куда интереснее посмотреть на итеративное решение.

Чтобы не ошибиться с решением стоит вспомнить ещё раз, что Deque, которая нам понадобится для решения, это двусторонняя очередь. Её рекомендуют использовать в том числе для стэков. Важно, что push, pop и peek соответствуют методам addFirst, removeFirst и peekFirst.

Код решения:
```java
public List<Integer> postorderTraversal(TreeNode root) {
    List<Integer> result = new ArrayList<>();
    if (root == null) return result;
        
    TreeNode current = root; // Point to the current node for handling
        
    Deque<TreeNode> stack = new ArrayDeque<>();
    while (current != null || !stack.isEmpty()) {
        if (current != null) {
                stack.push(current);
                current = current.left; // Continue left path
        } else {
            // Don't have current == don't have more left nodes
            TreeNode next = stack.peek().right;
            if (next == null) {
                // Don't have left node, don't have right node. Our root is on the stack
                // Remove it and put into the result
                next = stack.pop();
                result.add(next.val);
                // Return through the right path
                while (!stack.isEmpty() && next == stack.peek().right) {
                    next = stack.pop();
                    result.add(next.val);
                }
            } else {
                current = next;
            }
        }
    }
    return result;
}
```
Идея алгоритма заключается в том, чтобы спускать по левой ветке вниз пока можно, т.к. левая нода добавляется в результат самой первой. Как только мы упираемся в конец (current становится null), мы должны вернуться наверх и переключиться на правую ветку (если можем).

Т.к. мы без рекурсии, то эффект рекурсии мы иммитируем при помощи стэка. Для начала мы смотрим на то, есть ли у элемента на стэке правая нода. Если правая нода есть - мы переключаемся на неё, т.е. делаем current = temp. Если же правой ноды нет, это значит, что идти больше некуда и мы можем смело добавлять элемент из стэка в результат. Возвращаемся по правому пути обратно. 

----

## [↑](#home) <a id="goodNodes"></a> 1448. Count Good Nodes in Binary Tree
Рассмотрим задачу [1448. Count Good Nodes in Binary Tree](https://leetcode.com/problems/count-good-nodes-in-binary-tree/):
> Дан корень дерева. Нужно найти КОЛИЧЕСТВО "good nodes". Good Node - это такая нода, путь до которой от корня дерева не содержит нод, значение которых выше, чем у good node.

Разбор задачи от NeetCode: [Count Good Nodes in a Binary Tree](https://www.youtube.com/watch?v=7cp5imvDzl4)

Для решения нам понадобится DFS подход:
```java
public int goodNodes(TreeNode root) {
    return dfs(root, root.val);
}
```

Сам же рекурсивный DFS будет выглядеть следующим образом:
```java
private int dfs(TreeNode node, int maxValue) {
    int result = 0;
    if (node == null) return result;

    if (node.val >= maxValue) result++;
    maxValue = Math.max(maxValue, node.val);

    result = result + dfs(node.left, maxValue);
    result = result + dfs(node.right, maxValue);
    return result;
}
```

----

## [↑](#home) <a id="construct"></a> 105. Construct Binary Tree From Preorder And Inorder Traversal
Рассмотрим задачу [105. Construct Binary Tree From Preorder And Inorder Traversal](https://leetcode.com/problems/construct-binary-tree-from-preorder-and-inorder-traversal/):
> Дано два массива чисел, представляющих preorder и inorder представления дерева. Нужно сконструировать дерево.

Разбор задачи от NeetCode: [Construct Binary Tree From Preorder And Inorder Traversal](https://www.youtube.com/watch?v=ihj4IQGZ2zc)\
Разбор задачи от Nikhil Lohia: [Create Binary Tree from pre-order and in-order traversal](https://youtu.be/PbPS460rbMo?si=-_RvTJPLy4Z7HeVd&t=727)

Решение:
```java
public TreeNode buildTree(int[] preorder, int[] inOrder) {
    Map<Integer, Integer> inOrderIndexMap = new HashMap<>();
    for (int i = 0; i < inOrder.length; i++) {
        inOrderIndexMap.put(inOrder[i], i);
    }
    return buildTree(preorder, inOrderIndexMap, 0, 0, inOrder.length - 1);
}

private TreeNode buildTree(int[] preOrder, Map<Integer, Integer> indexMap, int rootIndex, int left, int right) {
    int rootValue = preOrder[rootIndex];
    TreeNode root = new TreeNode(rootValue);
        
    int mid = indexMap.get(rootValue);
    if (mid > left) {
        // Skip visited root index (i.e. increment it). Keep left border, restrict right border
        root.left = buildTree(preOrder, indexMap, rootIndex + 1, left, mid - 1);
    }
    if (mid < right) {
        // Mid is known according to the whole range. We should adapt it.
        // left == how many elements were skipped from the left
        int elementsFromLeft = mid + 1 - left;
        root.right = buildTree(preOrder, indexMap, rootIndex + elementsFromLeft, mid + 1, right);
    }
    return root;
}
```

----

## [↑](#home) <a id="construct2"></a> Construct Binary Tree from Inorder and Postorder Traversal
Рассмотрим задачу [Construct Binary Tree from Inorder and Postorder](https://leetcode.com/problems/construct-binary-tree-from-inorder-and-postorder-traversal):
> Дано два массива чисел, представляющих postorder и inorder представления дерева. Нужно сконструировать дерево.

Разбор задачи от NeetCode: [Construct Binary Tree from Inorder and Postorder Traversal](https://www.youtube.com/watch?v=vm63HuIU7kw)\
Описание решения так же можно прочитать у [TakeUForward](https://takeuforward.org/data-structure/construct-binary-tree-from-inorder-and-postorder-traversal/)

Нам придётся из основного метода вызывать вспомогательный:
```java
public TreeNode buildTree(int[] inorder, int[] postorder) {
        Map<Integer, Integer> map = new HashMap<>();
        // We will use postorder to find root, and inorder to find children
        // We will take value from postorder and search it in inorder. So create the map:
        for (int i = 0; i < inorder.length; i++) {
            map.put(inorder[i], i);
        }
        return buildTree(inorder, 0, inorder.length-1, 
                        postorder, 0, postorder.length-1,map);   
}
```

Вспомогательный метод:
```java
private TreeNode buildTree(int[] inorder, int is, int ie, 
                            int[] postorder, int ps, int pe,
                            Map<Integer,Integer> map) {
    // At first, check that we are still in the boundaries
    if (is > ie || ps > pe) return null;
    // Take last postorder range element as root
    TreeNode root = new TreeNode(postorder[pe]);
    // Now we can find root element index to split inorder to left and right parts
    int rootIndex = map.get(root.val);
    // It's easy to split inorder to left and right by rootIndex
    // [is, rootIndex-1][rootIndex][rootIndex+1, ie]
        
    // Calculate how many elements are on the left side (we need to know it to split postorder to left and right)
    int elOnLeft = rootIndex - is;

    // Keep postorder start (ps) and reduce right as: ps + elOnLeft - 1 (because of zero based index)
    TreeNode leftchild = buildTree(inorder, is, rootIndex - 1, 
                                    postorder, ps, ps + elOnLeft - 1, map);
    // Reduce postorder end by 1 (i.e. by root)
    // Shift postorder start (ps) by element that was separated as left part
    TreeNode rightchild = buildTree(inorder, rootIndex + 1, ie, 
                                    postorder, ps + elOnLeft, pe - 1, map);
    // Just set result to proper side field
    root.left = leftchild;
    root.right = rightchild;
    return root;
}
```

----

## [↑](#home) <a id="constructString"></a> 606. Construct String from Binary Tree
Рассмотрим задачу [606. Construct String from Binary Tree](https://leetcode.com/problems/construct-string-from-binary-tree/):
> Дано дерево. Нужно вернуть строку в виде Preorder представления, но обрамляя скобками child ноды.

Разбор задачи от NeetCode: [Construct String from Binary Tree](https://www.youtube.com/watch?v=b1WpYxnuebQ)

Нам понадобится дополнительный метод, чтобы его можно было вызывать рекурсивно:
```java
public String tree2str(TreeNode root) {
    StringBuilder result = new StringBuilder();
    buildString(result, root);
    return result.substring(1, result.length() - 1); 
}
```

Сам метод будет выглядеть так:
```java
private void buildString(StringBuilder result, TreeNode node) {
    result.append("(");
    result.append(node.val);
        
    if (node.left == null && node.right != null) {
        result.append("()");
    }
    if (node.left != null) buildString(result, node.left);
    if (node.right != null) buildString(result, node.right);

    result.append(")");
}
```

----

## [↑](#home) <a id="maxPathSum"></a> 124. Binary Tree Maximum Path Sum
Рассмотрим задачу [124. Binary Tree Maximum Path Sum](https://leetcode.com/problems/binary-tree-maximum-path-sum/):
> Дано дерево. Нужно найти сумму значений максимального по сумме значений пути

Разбор задачи от NeetCode: [Binary Tree Maximum Path Sum - DFS](https://www.youtube.com/watch?v=Hr5cWUld4vU)\
Разбор задачи от Nick White: [Binary Tree Maximum Path Sum (Algorithm Explained)](https://www.youtube.com/watch?v=mOdetMWwtoI)\
Разбор задачи от Michael Muinos: [Binary Tree Maximum Path Sum (Animated Walkthrough)](https://www.youtube.com/watch?v=6cA_NDtpyz8)

Для решения данной задачи нужно для начала вспомнить задачу [Path Sum](#pathSum).\
А так же стоит рассмотреть 3 случая:

![](../img/trees/MaxPathSum.png)

Решение:
```java
public int maxPathSum(TreeNode root) {
    int[] summ = {Integer.MIN_VALUE}; 
    maxPathSum(root, summ);
    return summ[0];
}

private int maxPathSum(TreeNode node, int[] summ) {
    if (node == null) return 0;
    // Avoid negative values. 0 means "do not go this way"
    int leftMax = maxPathSum(node.left, summ);
    int rightMax = maxPathSum(node.right, summ);
    
    // Calculate the best sum for this moment
    int left = Math.max(0, leftMax);
    int right = Math.max(0, rightMax);
    summ[0] = Math.max(summ[0], left + right + node.val);
    
    // Node on top of us can select only one path. Return the best path
    return Math.max(left, right) + node.val;
}
```

----

## [↑](#home) <a id="flatten"></a> 114. Flatten Binary Tree to Linked List
Рассмотрим задачу [114. Flatten Binary Tree to Linked List](https://leetcode.com/problems/flatten-binary-tree-to-linked-list/):
> Дано двоичное дерево. Нужно из его стуктуры сделать подобие LinkedList.

Разбор решения от NeetCode: [Flatten Binary Tree to Linked List](https://www.youtube.com/watch?v=rKnD7rLT0lI)\

![](../img/trees/Flatten.png)

```java
public void flatten(TreeNode root) {
    flat(root);
}

private TreeNode flat(TreeNode node) {
    if (node == null) return null;
    TreeNode leftTail = flat(node.left);
    TreeNode rightTail = flat(node.right);
    // I.E. if left branch should be moved
    if (node.left != null) {
        leftTail.right = node.right;
        node.right = node.left;
        node.left = null;
    }
    TreeNode last;
    if (rightTail != null) {
        last = rightTail;
    } else if (leftTail != null) {
        last = leftTail;
    } else {
        last = node;
    }
    return last;
}
```

Есть ещё одно хитрое решение при помощи [Morris Traversal Algorithm](https://youtu.be/NOKVBiJwkD0?si=uclTUUfDYvnIoNkE&t=705):

![](../img/trees/MorrisTraversal.png)

```java
public void flatten(TreeNode root) {
    if (root == null) return;
    while (root != null) {
        // Move the left branch to the right side
        if (root.left != null) {
            // Remember left pointer value before removal
            TreeNode left = root.left; 
            // Find end of the left part of tree
            TreeNode cur = left;
            while (cur.right != null) cur = cur.right;
            // Connect left to right
            cur.right = root.right;
            // Clean left pointer
            root.left = null;
            // Set right pointer to the left beginning
            root.right = left; 
        }
        root = root.right; // Go next to the right
    }
}
```

----
