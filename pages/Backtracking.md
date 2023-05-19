# <a id="home"></a> Backtracking

Данный раздел посвящён паттерну Backtracking из **[Leetcode Patterns](https://seanprashad.com/leetcode-patterns/)**.\
У NeetCode есть плэйлист разборов на эту тему: **[Backtracking Playlist](https://www.youtube.com/watch?v=pfiQ_PS1g8E&list=PLot-Xpze53lf5C3HSjCnyFghlW0G1HHXo)**.

**Table of Contents:**
- [Word Search](#wordSearch)
- [Letter Case Permutation](#casePermutation)
- [Subsets](#subsets)
- [Subsets II](#subsets2)
- [Permutations](#permutations)
- [Permutations II](#permutations2)

----

## [↑](#home) <a id="wordSearch"></a> Word Search
Рассмотрим задачу [Word Search](https://leetcode.com/problems/word-search/).

Разбор от NeetCode: "[Word Search - Backtracking](https://www.youtube.com/watch?v=pfiQ_PS1g8E)".\
Разбор от Nick White: "[LeetCode Word Search Solution Explained](https://www.youtube.com/watch?v=m9TrOL1ETxI)"

Решение по сути состоит из двух частей. Первая - начиная с каждой ячейки, которая содержит начальную букву слова пытаемся построить всё слово целиком:
```java
public boolean exist(char[][] board, String word) {
    for (int i = 0; i < board.length; i++) {
        for (int j = 0; j < board[0].length; j++) {
            if (board[i][j] == word.charAt(0)) {
                if (search(i,j,0,board,word)) return true;
            }
        }
    }
    return false;
}
```

Сам алгоритм поиска:
```java
public boolean search(int row, int column, int letter, char[][] board, String word) {
    // We reached the end == word was found
    if (word.length() == letter) return true;
    // cell should not be out of board bounds
    if (row < 0 || row >= board.length || column < 0 || column >= board[0].length) return false;
    // also, we should not consider visited cells AND cells with wrong symbol
    if (board[row][column] != word.charAt(letter)) return false;
    // Put out symbol to hide it from other iterations
    char tmp = board[row][column];
    board[row][column] = '#';
    if (search(row+1, column, letter+1, board, word) ||
        search(row-1, column, letter+1, board, word) ||
        search(row, column+1, letter+1, board, word) ||
        search(row, column-1, letter+1, board, word) ) {
        return true;
    }
    board[row][column] = tmp;
    return false;
}
```

----

## [↑](#home) <a id="casePermutation"></a> Letter Case Permutation
Рассмотрим задачу [Letter Case Permutation](https://leetcode.com/problems/letter-case-permutation/).

Подход к решению можно посмотреть в [Решаем Литкод : Letter Case Permutation](https://www.youtube.com/watch?v=wYO4ospshr8)

Решение задачи:
```java
List<String> result = new ArrayList<>();
public List<String> letterCasePermutation(String s) {
    search(s, "", 0);
    return result;
}

public void search(String s, String cur, int ind) {
    if (ind == s.length()) {
        result.add(cur);
        return;
    }
    if (Character.isDigit(s.charAt(ind))) {
        search(s, cur + s.charAt(ind), ind+1);
    } else {
        search(s, cur + Character.toUpperCase(s.charAt(ind)), ind+1);
        search(s, cur + Character.toLowerCase(s.charAt(ind)), ind+1);
    }
}
```

----

## [↑](#home) <a id="subsets"></a> Subsets
Рассмотрим задачу [Subsets](https://leetcode.com/problems/subsets/).

Понять задачу нам снова поможет NeetCode: [Subsets - Backtracking](https://www.youtube.com/watch?v=REOH22Xwdkk).

Для решения данной задачи нам понадобится две коллекции:
```java
List<List<Integer>> result = new ArrayList<>();
LinkedList<Integer> subset = new LinkedList<>();
```

А так же сам метод поиска:
```java
public void search(int index, int[] nums) {
    if (index >= nums.length) {
        result.add(new ArrayList<Integer>(subset));
        return;
    }
    // Decision to include
    subset.addLast(nums[index]);
    search(index+1, nums);
    // Decision to not include
    subset.removeLast();
    search(index+1, nums);
}
```

Остаётся только запустить наш алгоритм:
```java
public List<List<Integer>> subsets(int[] nums) {    
    search(0, nums);
    return result;
}
```

----

## [↑](#home) <a id="subsets2"></a> Subsets II
Рассмотрим задачу [Subsets II](https://leetcode.com/problems/subsets-ii/).

Разбор от NeetCode: [Subsets II - Backtracking](https://www.youtube.com/watch?v=Vn2v6ajA7U0).

На самом деле решение - частный случай решения из первой части "[Subsets](#subsets)".\
Во-первых, чтобы проще работать с дубликатами требуется отсортировать исходный массив:
```java
public List<List<Integer>> subsets(int[] nums) {    
        Arrays.sort(nums);
```

И меняется лишь вторая часть алгоритма, где мы обрабатываем часть где мы не включаем элемент.
```java
// Decision to not include
subset.removeLast();
while (index + 1 < nums.length && nums[index] == nums[index + 1]) {
    index++;
}
search(index+1, nums);
```

----

## [↑](#home) <a id="permutations"></a> Permutations
Рассмотрим задачу [Permutations](https://leetcode.com/problems/permutations/).

Разбор от NeetCode: [Backtracking: Permutations](https://www.youtube.com/watch?v=s7AvT7cGdSo).\


Самое первое, что нам понадобится - немного изменить то, в каком виде у нас входные данные:
```java
public List<List<Integer>> permute(int[] nums) {
    // Convert array to a LinkedList
    LinkedList<Integer> current = new LinkedList<>();
    for (int num : nums) {
        current.add(num);
    }
    return permute(current);
}
```

Нужно это нам для того, чтобы создать другой метод, который будет работать уже не с массивом, а с листом:
```java
public List<List<Integer>> permute(LinkedList<Integer> current) {
    // Each permutation iteration has own portion of data
    List<List<Integer>> result = new ArrayList<>();
    // TODO
    return result;
}
```

Прежде всего в TODO опишем базовый случай, когда у нас есть только 1 элемент:
```java
// Base case: Return single element "as is" 
if (current.size() == 1) {
    result.add(new ArrayList<>(current));
    return result;
}
```
В этом случае мы просто добавляем в результат список из одного единственного элемента и возвращаем результат.

Если же у нас больше чем один элемент - мы должны выполнить столько итераций, сколько у нас есть элементов:
```java
// Iterate over all positions
for (int i = 0; i < current.size(); i++) {
    Integer n = current.removeFirst();
    // Subtask: do the same without first element
    List<List<Integer>> perms = permute(current);
    // Append detached element to all subtask results
    for (List<Integer> perm : perms) {
        perm.add(n);
    }
    // Accumulate results to the current results
    result.addAll(perms);
    // Return detached element as the last element
    current.addLast(n);
}
```

Есть ещё один хороший разбор: [Nikhil Lohia: Permutations](https://www.youtube.com/watch?v=H232aocj7bQ).

----

## [↑](#home) <a id="permutations2"></a> Permutations II
Рассмотрим задачу [Permutations II](https://leetcode.com/problems/permutations-ii/).

Разбор от NeetCode: [Backtracking: Permutations II](https://www.youtube.com/watch?v=qhBVWf0YafA).








[LeetCode 257. Binary Tree Paths](https://www.youtube.com/watch?v=H2D4HcVZq_g)
[Maximum Depth of Binary Tree](https://www.youtube.com/watch?v=hTM3phVI6YQ)