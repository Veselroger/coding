# <a id="home"></a> Two Pointers

Данный раздел посвящён задачам на шаблон **"Two Pointers"**.\
Продолжаем двигаться по [Roadmap](https://neetcode.io/roadmap) от NeetCode.\
Данные задачи входят в том числе в **[Leetcode Patterns](https://seanprashad.com/leetcode-patterns/)**.

**Table of Contents:**
- [Valid Palindrome](#palindrome)
- [Two Sum II](#twoSum2)
- [3Sum](#3Sum)
- [Container With Most Water](#water)
- [Trapping Rain Water](#trap)

----

## [↑](#home) <a id="palindrome"></a> Valid Palindrome
Рассмотрим задачу "[Valid Palindrome](https://leetcode.com/problems/valid-palindrome/)".

Разбор задачи от NeetCode: [Valid Palindrome](https://www.youtube.com/watch?v=jJXJ16kPFWg).\
Разбор задачи от Nick White: [Valid Palindrome Solution Explained](https://www.youtube.com/watch?v=rYyn9Vc-dBQ).

Палиндром - текст, который слева и справа (то есть у нас есть **ДВЕ** стороны) читается одинаково. Читаем символы из строки по индексу слева (указатель left) и справа (указатель right). То есть классическая задача на два указателя:

![](../img/pointers/Palindrome.png)

Решение:
```java
public boolean isPalindrome(String s) {
    int left = 0, right = s.length() - 1;
    while (left < right) {
        while(left < right && !Character.isLetterOrDigit(s.charAt(left))) {
            left++;
        }
        while(right > left && !Character.isLetterOrDigit(s.charAt(right))) {
            right--;
        }
        if (Character.toLowerCase(s.charAt(left)) != Character.toLowerCase(s.charAt(right))) {
            return false;
        }
        left++;
        right--;
    }
    return true;
}
```

----

## [↑](#home) <a id="twoSum2"></a> Two Sum II
Рассмотрим задачу "[Two Sum II](https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/)".

Разбор задачи от NeetCode: [TWO SUM II](https://www.youtube.com/watch?v=cQ1Oz4ckceM).\
Разбор задачи от Nick White: [Two Sum II Solution Explained](https://www.youtube.com/watch?v=sAQT4ZrUfWo).

![](../img/pointers/TwoSum2.png)

Решение:
```java
public int[] twoSum(int[] numbers, int target) {
    int left = 0, right = numbers.length - 1;
    while (left < right) {
        int curSum = numbers[left] + numbers[right];
        if (curSum > target) {
            right--;
        } else if (curSum < target) {
            left++;
        } else {
            return new int[]{left+1, right+1};
        }
    }
    return new int[]{};
}
```

----

## [↑](#home) <a id="3Sum"></a> 3Sum
Рассмотрим задачу "[3Sum](https://leetcode.com/problems/3sum/)".

Разбор задачи от NeetCode: [3Sum](https://www.youtube.com/watch?v=jzZsG8n2R9A).\
Разбор задачи от Nick White: [LeetCode 3Sum Solution Explained](https://www.youtube.com/watch?v=qJSPYnS35SE).

Решение данной задачи так же испльзует подход с указателями:

![](../img/pointers/sum3.png)

Решение:
```java
public List<List<Integer>> threeSum(int[] nums) {
    Arrays.sort(nums);
    List<List<Integer>> result = new ArrayList<List<Integer>>();

    for (int i = 0; i < nums.length; i++) {
        if (i > 0 && nums[i] == nums[i-1]) continue; // To avoid duplicates
        // Apply two sum solution
        int l = i + 1, r = nums.length - 1;
        while (l < r) {
            int sum = nums[i] + nums[l] + nums[r];
            if (sum > 0) {
                r--;
            } else if(sum < 0) {
                l++;
            } else {
                result.add(Arrays.asList(nums[i],nums[l],nums[r]));
                l++;
                while (nums[l] == nums[l-1] && l < r) {
                    l++;
                }
            }
        }
    }
    return result;
}
```

----

## [↑](#home) <a id="water"></a> Container With Most Water
Рассмотрим задачу "[Container With Most Water](https://leetcode.com/problems/container-with-most-water/)".

Разбор задачи от NeetCode: [Container with Most Water](https://www.youtube.com/watch?v=UuiTKBwPgAo).\
Разбор задачи от Nick White: [Container With Most Water Explained](https://www.youtube.com/watch?v=6PrIRPpTI9Q).

Главный вопрос: что такое контейнер с водой? Это когда у нас есть высота слева (left) и высота справа (right). Меньшее значение ограничивает высоту. Опять получается, что надо использовать указатели. Какой указатель сдвигать? Выгднее оставлять самую высокую "стенку", поэтому будем двигать указатель, значение которого меньше.

Решение:
```java
public int maxArea(int[] height) {
    int left = 0, right = height.length - 1;
    int maxArea = 0;
    while (left < right) {
        if (height[left] < height[right]) {
            maxArea = Math.max(maxArea, height[left] * (right - left));
            left++;
        } else {
            maxArea = Math.max(maxArea, height[right] * (right - left));
            right--;
        }
    }
    return maxArea;
}
```

----

## [↑](#home) <a id="trap"></a> Trapping Rain Water
Рассмотрим задачу "[Trapping Rain Water](https://leetcode.com/problems/trapping-rain-water/)".

Разбор задачи от NeetCode: [Trapping Rain Water](https://www.youtube.com/watch?v=ZI2z5pq0TqA).

Решение:
```java
public int trap(int[] height) {
    int left = 0, right = height.length - 1;
    int leftMax = height[left], rightMax = height[right];
    int res = 0;
    while (left < right) {            
        if (leftMax < rightMax) {
            left++;
            leftMax = Math.max(leftMax, height[left]);
            res = res + (leftMax - height[left]);
        } else {
            right--;
            rightMax = Math.max(rightMax, height[right]);
            res = res + (rightMax - height[right]);
        }
    }
    return res;
}
```