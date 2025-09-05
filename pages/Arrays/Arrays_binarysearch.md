# [←](../../README.md) <a id="home"></a> Arrays: Binary Search

Данный раздел посвящён задачам на массивы и **двоичный поиск**.\
Задачи на LeetCode: **"[Binary Search](https://leetcode.com/problem-list/binary-search/)"**.\
Плэйлист от NeetCode: **"[Binary Search](https://www.youtube.com/watch?v=4sQL7R5ySUU&list=PLot-Xpze53leNZQd0iINpD-MAhMOMzWvO)"**

**Table of Contents:**
- [[704] Binary Search](#search)
- [[744] Smallest Letter Greater Than Target](#smallest)
- [[374] Guess Number Higher or Lower](#guess)
- [[35] Search Insert Position](#insertPosition)
- [[34] Find First and Last Position of Element in Sorted Array](#firstLastPos)
- [[367] Valid Perfect Square](#perfectSquare)
- [[153] Find Minimum in Rotated Sorted Array](#rotatedMin)
- [[33] Search in Rotated Sorted Array](#rotatedSearch)
- [[875] Koko Eating Bananas](#koko)
- [[981] Time Based Key-Value Store](#time)

----

## [↑](#home) <a id="search"></a> 704. Binary Search
Рассмотрим базовую задачу на двоичный поиск: **"[704. Binary Search](https://leetcode.com/problems/binary-search/)"**:
> Дан массив целых чисел. Нужно в нём найти элемент и вернуть его индекс за O(log n).

Предположим, нам нужно найти элемент со значением 5:

![](../../img/arrays/binary/BinarySearch.gif)

Как видно, нам нужно завести два указателя, определяющие рабочий диапазон: ``(L)eft`` и ``(R)ight``.\
А так же указатель на середину ``(M)iddle``. Если в середине оказался ответ - возвращаем его.

Если искомое число меньше середины - сдвигаем правую границу левее середины.\
Если искомое число больше середины - сдвигаем левую границу правее середины.\
Конец алгоритма будет тогда, когда число найдено ИЛИ если левая граница выйдет за пределы правой.

<details><summary>Решение</summary>

```java
public int search(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int middle = left + ((right - left)/2);
        if (nums[middle] == target) return middle;
        if (target < nums[middle]) {
            right = middle - 1;
        } else {
            left = middle + 1;
        }
    }
    return -1;
}
```
</details>

Разбор задачи от NeetCode: **"[Binary Search - Leetcode 704](https://www.youtube.com/watch?v=s4DPM8ct1pI)"**.

----

## [↑](#home) <a id="smallest"></a> 744. Smallest Letter Greater Than Target
Задача на бинарный поиск может быть подана в других терминах.\
Например: [744. Find Smallest Letter Greater Than Target](https://leetcode.com/problems/find-smallest-letter-greater-than-target/):
> Необходимо найти наименьшений символ, который лексикографически больше, чем указанный target. Исходная строка отсортирована.

```java
public char nextGreatestLetter(char[] letters, char target) {
    int n = letters.length;
    // Can't satisfy "lexicographically greater than target"
    if (target >= letters[n-1]) return letters[0];
 
    int ans = -1;
    int left = 0, right = n - 1;
    while (left <= right) {
        int mid = left + (right - left)/2;
        // "if lexicographically greater than target"
        if (letters[mid] > target) {
            ans = mid;
            // Try to find in a new range of smaller values
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    return letters[ans];
}
```

----

## [↑](#home) <a id="guess"></a> 374. Guess Number Higher or Lower
Рассмотрим другую задачу на двоичный поиск: **"[374. Guess Number Higher or Lower](https://leetcode.com/problems/guess-number-higher-or-lower/)"**:
> Ведущий загадывает число между 1 и N. Нужно отгадать при помощи некоторого API.

Данная задача является почти копией задачи [Binary Search](#search).\
Отличие лишь только в том, что у нас не массив, а само число. И результат мы получаем через "API".

<details><summary>Решение</summary>

```java
public int guessNumber(int n) {
    int left = 1, right = n;
    while (left <= right) {
        int mid = left + ((right - left)/2);
        int api = guess(mid);
        if (api == 0) return mid;
        if (api == 1) {
            left = mid + 1; //num (i.e. mid) is lower than the picked number
        } else {
            right = mid - 1; //num (i.e. mid) is higher than the picked number 
        }
    }
    return 0;
}
```
</details>

Разбор задачи от NeetCode: **"[Guess Number Higher or Lower - Leetcode 374](https://www.youtube.com/watch?v=xW4QsTtaCa4)"**

----

## [↑](#home) <a id="insertPosition"></a> 35. Search Insert Position
Рассмотрим другую задачу на двоичный поиск: **"[35. Search Insert Position](https://leetcode.com/problems/search-insert-position/)"**:
> Дан массив целых чисел. Нужно найти элемент или индекс, где он мог бы стоять. Сделать это за O(log n).

Данная задача похожа на задачу [Binary Search](#search), но она про понимание того, где в какой момент находятся границы диапазона поиска.

![](../../img/arrays/binary/InsertPosition.png)

<details><summary>Решение</summary>

```java
public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left <= right) {
            int mid = left + ((right - left)/2);
            if (target == nums[mid]) return mid;
            if (target < nums[mid]) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        return left;
    }
```
</details>

Разбор задачи от NeetCode: **"[Search Insert Position - Binary Search - Leetcode 35](youtube.com/watch?v=K-RYzDZkzCI)"**.

----

## [↑](#home) <a id="firstLastPos"></a> 34. Find First and Last Position of Element in Sorted Array
Рассмотрим другую задачу на двоичный поиск: **"[34. Find First and Last Position of Element in Sorted Array](https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array)"**:
> Дан упорядоченный массив чисел. Нужно найти в нём некоторое число и указать первый индекс в массиве с ним и последний.

Для решения данной задачи следует немного шире посмотреть на двоичный поиск:

![](../../img/arrays/binary/bsearch.png)

Для начала, вспомним базовую версию [Binary Search](#search):
```java
public int search(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    while (left <= right) {
        int middle = left + (right - left)/2;
        if (target < nums[middle]) {
            right = middle - 1; // go left
        } else if (target > nums[middle]) {
            left = middle + 1; // go right
        } else {
            return middle; // found
        }
    }
    return -1;
}
```

А что, если массив будет содержать дубликаты? Как найти в таком случае именно **первый** элемент?\
Т.к. мы ищем **первый** элемент, тогда в случае нахождения нужного **middle** мы должны сместить указать right влево, **НО** при этом мы должны запомнить найденный индекс. Потому что первый найденный индекс может оказаться именно первым вхождением или вообще единственным.\
Тогда:
```java
public int search(int[] nums, int target) {
    int left = 0, right = nums.length - 1;
    int index = -1;
    while (left <= right) {
        int middle = left + (right - left)/2;
        if (target < nums[middle]) {
            right = middle - 1; // go left
        } else if (target > nums[middle]) {
            left = middle + 1; // go right
        } else {
            index = middle;
            right = middle - 1; // go left to first occurence
        }
    }
    return index;
}
```

Если посмотреть, то это как раз то, что нам нужно.\
Ведь нам надо найти самый первый и самый последний элемент.
Получается, нам нужно наш метод **search** делать с креном или влево или вправо.\
Тогда:

```java
public int search(int[] nums, int target, boolean leftBiased) {
    int left = 0, right = nums.length - 1;
    int index = -1;
    while (left <= right) {
        int middle = left + (right - left)/2;
        if (target < nums[middle]) {
            right = middle - 1; // go left
        } else if (target > nums[middle]) {
            left = middle + 1; // go right
        } else {
            index = middle; // target was found, but ...
            if (leftBiased) {
                right = middle - 1;
            } else {
                left = middle + 1;
            }
        }
    }
    return index;
}
```

Остаётся лишь найти самый первый и самый последний индексы:
```java
public int[] searchRange(int[] nums, int target) {
    int left = search(nums, target, true);
    int right = search(nums, target, false);
    return new int[]{left, right};
}
```

----


## [↑](#home) <a id="perfectSquare"></a> 367. Valid Perfect Square
Рассмотрим другую задачу на двоичный поиск: **"[367. Valid Perfect Square](https://leetcode.com/problems/valid-perfect-square/)"**:
> Дано число N. Нужно вычислить, является ли оно квадратом какого-нибудь числа. Например, 16 является квадратом числа 4.

Данную задачу можно тоже решить при помощи двоичного поиска за O(logn).

Разбор задачи от NeetCode: **"[Valid Perfect Square - Leetcode 367](https://www.youtube.com/watch?v=Cg_wWPHJ2Sk)"**.

Среди последовательности чисел (а значит они упорядоченны/отсортированы) нужно найти одно значение.\
Это значение при умножении самого на себя должно давать искомый **target**.\
Это значит, что правая граница не может быть больше квадратного корня **target**.\
Максимальное значение в рамках Integer может быть ``46340``:

```java
public boolean isPerfectSquare(int num) {
    int limit = (int) Math.sqrt(Integer.MAX_VALUE);
    int left = 1, right = Math.min(num, limit);
```

Тогда задачей нашего двоичного числа будет в последовательности от ``1..limit`` найти нужное значение.\
Это позволит нам проверять не каждое число.\
Разница с другими задачами лишь в том, что ``middle`` не индекс и не указатель на значение, а само значение. 

<details><summary>Решение</summary>

```java
public boolean isPerfectSquare(int num) {
    int left = 1, right = Math.min(num, 46_340);
    while (left <= right) {
        int mid = left + (right - left)/2;
        long midSquare = (long)mid * (long)mid;
        if (midSquare > num) {
            right = mid - 1;
        } else if (midSquare < num) {
            left = mid + 1;
        } else {
            return true;
        }
    }
    return false;
}
```
</details>

----

## [↑](#home) <a id="rotatedMin"></a> 153. Find Minimum in Rotated Sorted Array
Рассмотрим задачу **"[153. Find Minimum in Rotated Sorted Array](https://leetcode.com/problems/find-minimum-in-rotated-sorted-array/)"**:
> Дан массив, нужно в нём найти минимум. Но массив непростой, а он провёрнут на сколько-то элементов (как конвеерная лента).

Как обычно в случае с Binary Search у нас два указателя на крайние границы: Left и Right.\
Важно помнить, что элементы упорядочены, т.е. если бы не было разворота, то left всегда был бы меньше right.\
Т.е. если left > right - это признак того, что в последовательности был разворот. Например:

![](../../img/arrays/binary/BinaryRange.png)

Если взять left и right по краям - мы увидим, что right меньше left, т.к. у нас есть разворот.\
При этом если right сдвинуть на середину, то right будет больше left, т.е. разворота нет и left - самый минимальный элемент в **ЭТОМ** диапазоне.

Данная задача имеет средний уровень сложности, т.к. надо учитывать разные случаи. Например:
1. Массив ``[3,4,5,1,2]``
Как видно, 2 < 3, а значит есть разворот. Ищем в какую сторону искать разворот. До середины у нас упорядоченный подмассив ``[3,4,5]``, тогда мы идём направо. Там у нас упорядоченный подмассив ``[1,2]``, а значит ответ 1.
2. Массив ``[3,1,2]``
Как видно, 2 < 3, а значит есть разворот. Ищем в какую сторону искать разворот. До середины есть разворот, т.к. 1 < 3. Тогда получается, что мидл и есть наш минимум.

Разбор задачи от NeetCode: [Find Minimum in Rotated Sorted Array - Binary Search](https://www.youtube.com/watch?v=nIVW4P8b1VA)

![](../../img/arrays/binary/RotatedMin.png)

<details><summary>Решение</summary>

```java
public int findMin(int[] nums) {
    int left = 0, right = nums.length - 1;
    int mem = nums[0];
    while (left <= right) {
        // Case 1: ordered sequence WITHOUT rotation
        if (nums[left] <= nums[right]) {
            // Maybe we saw already something smaller in another range
            mem = Math.min(nums[left], mem);
            break;
        }
        // Case 2: Rotation was found. Search for a rotation point
        int mid = left + ((right - left)/2);
        // middle element can be a rotation point
        mem = Math.min(nums[mid], mem);
        if (nums[left] <= nums[mid]) {
            // [left, mid] is sorted. Rotation somewhere on the right
            left = mid + 1;    
        } else {
            // Rotation somewher on the left
            right = mid - 1;
        }
    }
    return mem;
}
```
</details>

----

## [↑](#home) <a id="rotatedSearch"></a> 33. Search in Rotated Sorted Array
Рассмотрим задачу **"[33. Search in Rotated Sorted Array](https://leetcode.com/problems/search-in-rotated-sorted-array/)"**:
> Дан массив, нужно в нём найти target. Но массив непростой, а он провёрнут на сколько-то элементов (как конвеерная лента).

Данная задача похожа на задачу [Find Minimum in Rotated Sorted Array](#rotatedMin).

Выбираем середину и смотрим, не target ли это. Если нет - у нас есть две половины: левая и правая.\
Самое простое - если половина отсортирована. Тогда мы точно можем проверить, там ли число.\
Если же число не в сортированной половине - уходим искать его в другую часть.

```java
public int search(int[] nums, int target) {
    int left = 0, right = nums.length - 1;

    while (left <= right) {
        int mid = left + (right-left)/2;
        if (nums[mid] == target) return mid;
        // Only one part is rotated
        // Left part is ordered
        if (nums[left] <= nums[mid]) {
            if (target > nums[mid] || target < nums[left]) {
                left = mid + 1; // target is out of this range
            } else {
                right = mid - 1; // target is in range
            }
        } else {
            // right part is ordered
            if (target < nums[mid] || target > nums[right]) {
                right = mid - 1; // target is out of this range
            } else {
                left = mid + 1; // target is in range
            }
        }
    }
    return -1;
}
```

----

## [↑](#home) <a id="koko"></a> 875. Koko Eating Bananas
Рассмотрим задачу **"[875. Koko Eating Bananas](https://leetcode.com/problems/koko-eating-bananas/)"**:
> Дан массив целых чисел, где каждое число - кол-во бананов в ячейке. И дан ограничитель времени H.
Нужно найти такое минимальное значение K (бананов в час), которое позволит съесть все бананы и успеть во время H.
За одну единицу времени можно есть только из одной и той же ячейки.

Данная задача на применение двоичного поиска.\
Допустим, у нас есть массив ``[3,6,7,11]`` и ограничение по времени - ``8h``.\
Т.к. за раз мы можем обрабатывать только один элемент массива, то наша максимальная скорость - 11.\
Таким образом на скорости 11 мы справимся за 4 часа (т.к. у нас 4 элемента).

Нам нужно найти минимальную скорость, за которую мы успеем за данное ограничение по времени.\
При помощи двоичного поиска мы выбираем элемент по середине.\
Проверяем, подходит ли он нам, а дальше уходим либо в левую от него часть, либо в правую.

Когда мы находим mid элемент, мы проходим по всем N элементам массива и считаем, за сколько часов мы уложимся.\
Если мы всё ещё укладываемся, обновляем минимальное значение нашего K, ведь mid будет становиться всё меньше и меньше:
```java
int mid = left + ((right - left)/2);
double hours = 0;
for (int pile : piles) {
    hours = hours + Math.ceil((double)pile / mid);
}
```
Как видно, мы используем Math.ceil для округления в большую сторону.

<details><summary>Решение</summary>

```java
public int minEatingSpeed(int[] piles, int h) {
    int maxVal = 0;
    for (int pile : piles) maxVal = Math.max(maxVal, pile);

    int left = 1, right = maxVal;
    int result = right;
    while (left <= right) {
        int mid = left + ((right - left)/2);
        double hours = 0;
        for (int pile : piles) {
            hours = hours + Math.ceil((double)pile / mid);
        }
        if (hours <= h) {
            result = Math.min(result, mid);
            right = mid - 1;
        } else {
            left = mid + 1;
        }
    }
    return result;
}
```
</details>

Разбор задачи от NeetCode: **"[Koko Eating Bananas - Binary Search](https://www.youtube.com/watch?v=U2SozAs9RzA)"**.

----

## [↑](#home) <a id="time"></a> 981. Time Based Key-Value Store
Рассмотрим задачу **"[981. Time Based Key-Value Store](https://leetcode.com/problems/time-based-key-value-store/)"**:
> Нужно реализовать хранилище, где ключу сопоставлены значения с временными отметками.

Во-первых, значения в нашем хранилище представлены парами: timestamp и value.\
Самое простое, начина с Java 17, можно представить это как **record**:
```java
class TimeMap {
    record Pair(Integer timestamp, String value){} 
    
    private Map<String, List<Pair>> store = new HashMap<>();
}
```

Метод записи значения прост. Мы должны по ключу добавить в конец списка новую пару:
```java
public void set(String key, String value, int timestamp) {
    List<Pair> list = store.get(key);
    if (list == null) {
        list = new ArrayList<>();
        store.put(key, list);
    }
    list.add(new Pair(timestamp, value));
}
```

А вот метод получения будет реализовывать двоичный поиск.
```java
public String get(String key, int timestamp) {
    String result = "";
    List<Pair> list = store.get(key);
    if (list == null) return result;
    
    int l = 0, r = list.size() - 1;
    while (l <= r) {
        int m = l + (r - l)/2;
        int item = list.get(m);
        if (item.timestamp() <= timestamp) {
            // Valid answer
            result = item.value();
            // Try to find item closer to timestamp
            l = m + 1;
        } else if (item.timestamp() > timestamp) {
            // Timestamp is too big. Go left to find something smaller
            r = m - 1;
        }
    }
    return result;
}
```

----


