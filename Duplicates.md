# <a id="home"></a> Contains Duplicate

**Table of Content:**
- [О задаче](#about)
- [На основе Set](#set)
- [Вариации задачи](#modifications)
  - [Nearby Duplicate](#nearby)
  - [Nearby Almost Duplicate](#almostDuplicate)
  - [Find the duplicate number](#duplicateNumber)

- [Counting Sort](#counting)
----


## [↑](#home) <a id="about"></a> О задаче
Поиск дубликатов - довольно распространённая задача, которая может встретиться в работе. Данный разбор основан на материале от Влада Тен: **"[LeetCode Patterns: #1 Contains Duplicate](https://www.youtube.com/watch?v=a14p8DYFdCA&list=PLyHj6yBbnkUgC6T9RpPxEBUv3My9lPd7m&index=2)"**. 

Данную задачу можно найти непосредственно на Leetcode: **"[217. Contains Duplicate](https://leetcode.com/problems/contains-duplicate/)"**.

От нас требуется реализовать метод, который выглядит так:
```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        // Solution
    }
}
```

## [↑](#home) <a id="set"></a> На основе Set
Самый простой, логичный и лёгкий для запоминания способ - реализовать метод на основе структуры данных **Set**, т.к. данная структура не содержит дубликатов. Для этого нам понадобится пакет **java.util**:
```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        Set<Integer> seen = new HashSet<>();
        for (int num : nums) {
            if (!seen.add(num)) {
                return true;
            }
        }
        return false; 
    }
}
```
Тут всё просто. Контракт **Set** гарантирует, что если не удалось добавить элемент (т.е. элемент уже был добавлен ранее), то метод **add** вернёт нам false.

Можно сделать стильно-модно-функционально:
```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        final Set<Integer> seen = new HashSet<>();
        return Arrays.stream(nums).anyMatch(num -> !seen.add(num));
    }
}
```

Можно даже сделать так:
```java
class Solution {
    public boolean containsDuplicate(int[] nums) {
        return Arrays.stream(nums).distinct().count() != nums.length;
    }
}
```

Интересно, но по данным статистики leetcode вариант с сетом эффективнее чем стримы.

---------

## [↑](#home) <a id="modifications"></a> Вариации задачи

### [↑](#home) <a id="nearby"></a> Nearby Duplicate
Поиск дубликатов может быть усложнён. Например, требует найти не просто дубликат, а ближайший дубликат. Пример такого усложнения - задача **"[219. Contains Duplicate II](https://leetcode.com/problems/contains-duplicate-ii/)"**.

Условия очень просты:
```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        // Something smart
    }
}
```
То есть дан некоторый набор данных, но нас интересует не весь набор сразу, а только его часть. Такую часть ещё называют "окном", отсюда и название подхода - **"[Window Sliding](https://www.geeksforgeeks.org/window-sliding-technique/)"**.

```java
class Solution {
    public boolean containsNearbyDuplicate(int[] nums, int k) {
        // Limit to the window size
        HashSet<Integer> set = new HashSet<>(k+1);
        for (int i = 0; i < nums.length; i++) {
            if (set.add(nums[i])) {
                if (set.size() > k) set.remove(nums[i-k]);
            } else {
                return true;
            }
        }
        return false;
    }
}
```
Как и раньше, мы опираемся на то, что Set возвращает false - если найден дубликат. Кроме того, мы как только предельный размер окна достигнут - мы уменьшаем его размер. Наш set хранит k+1 элемент. Тут полезно посмотреть на пример из задачи:
> [1,2,3,1], k = 3

### [↑](#home) <a id="almostDuplicate"></a> Nearby Almost Duplicate
У поиск ближайших дубликатов есть интересное продолжение - **"[220. Contains Duplicate III](https://leetcode.com/problems/contains-duplicate-iii/)"**.

Из всех решений мне понравилось с точки зрения подхода решение на основе **[Navigable Set](https://docs.oracle.com/javase/8/docs/api/java/util/NavigableSet.html)**. Наиболее удобной реализацией является структура данных TreeSet, где операции выполняются за логарифмическое время. Это интересное использование Navigable интерфейса. Например, если мы знаем, что дано число num=5 и t=2, то мы можем вызвать floor(5+2) и искать число "в пол" (пол внизу, т.е. поиск вниз), т.е. получим максимальное число, которое максимум равно 5+2 или любое меньшее. Аналогично выполняется поиск "в потолок" (потолок наверху, поэтому поиск вверх), тогда для ceiling(5-2) мы получим минимум 5-2 или любое число, которое больше. Подробнее см. комментарий в коде и сам код:

```java
class Solution {
    public static boolean containsNearbyAlmostDuplicate(int[] nums, int k, int t) {
        /**
         * Для числа Num и t = 2 поиск выглядит так:
         * 
         *      ----floor----
         *      -     ↓     -
         *      -    num    -
         *      -     ↑     -
         *      ---ceiling---
         */
        NavigableSet<Long> treeSet = new TreeSet<>();
        for (int i = 0; i < nums.length; i++) {
            long num = (long) nums[i]; // To avoid overflow
            Long floor = treeSet.floor(num + t);
            if (floor != null && floor >= num)
                return true;

            Long ceil = treeSet.ceiling(num - t);
            if (ceil != null && ceil <= num)
                return true;

            treeSet.add(num);
            if (i >= k)
                treeSet.remove((long) nums[i - k]);
        }
        return false;
    }
}
```
Так же можно увидеть интересную реализацию при помощи всё того же подхода с bucket sort'а на stackoverflow в топике **"[Contains Duplicate III](https://stackoverflow.com/questions/31119971/leetcode-contains-duplicate-iii/48317895)"**.


### [↑](#home) <a id="duplicateNumber"></a> Find the duplicate number
Есть ещё более интересное продолжение - **"[287. Find the Duplicate Number](https://leetcode.com/problems/find-the-duplicate-number/)"**.

Если посмотреть в секцию "Related Topics", то мы увидим "Two Pointers". Это очень полезный подход, поэтому рекомендуется ознакомиться с таким решением. Основано оно действительно на двух указателях: медленном и быстром. Каждый указатель перемещается следующим образом: начиная с нулевого индекса мы шагаем на тот индекс, который равен значению текущего индекса. То есть если nums[0]=1, то мы шагаем на индекс 1. Быстрый указатель отличается от медленного лишь тем, что он делает за раз не 1 переход, а два.

Решение можно поделить на два этапа: обнаружение цикла и обнаружение места цикла.
```java
class Solution {
    public int findDuplicate(int[] nums) {
        int slow = nums[0];
        int fast = nums[slow];
        while (fast != slow) {
            slow = nums[slow];
            fast = nums[nums[fast]];
        }
        // Нашли цикл. Теперь ищем где он происходит. 
        slow = 0;
        while (fast != slow) {
            slow = nums[slow];
            fast = nums[fast];
        }
        return slow;
    }
}
```

---------

## [↑](#home) <a id="counting"></a> Counting Sort
Есть интересный способ решения той же задачи при условии, что мы знаем характер данных, а именно мы знаем минимальное и максимальное значение. Кроме того, этих значений не должно быть много. В этом случае можно применить принцип **[Counting Sort](https://www.youtube.com/watch?v=YEabFTMDczQ&list=PLlsmxlJgn1HLCmaF51i5xAbgv1f49CsoP&index=7)**. Можно вспомнить про это ещё по отличному видео "[Counting Sort: An Exploration of Sorting Special Input In Linear Time](https://www.youtube.com/watch?v=1mh2vilbZMg)".

В этом случае реализация может выглядеть как-то так:
```java
public static boolean containsDuplicate(int[] nums, int maxVal) {
    int[] stored = new int[maxVal + 1];
    for (int num : nums) { 
        stored[num] = stored[num] + 1;
        if (stored[num] > 1) return true;
    }
    return false;
}
```