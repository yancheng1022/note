---
title: leetcode刷题
date: 2024/05/22
updated: 2024/07/04
categories:
  - coding
tags:
  - 技术文章
  - leetcode
  - 算法
---

# 80、删除有序数组中的重复项

给你一个有序数组 nums ，请你 原地 删除重复出现的元素，使得出现次数超过两次的元素只出现两次 ，返回删除后数组的新长度。
不要使用额外的数组空间，你必须在 原地 修改输入数组 并在使用 O(1) 额外空间的条件下完成

```java
class Solution {  
    public int removeDuplicates(int[] nums) {  
        if (nums.length == 2) return 2;  
        int i = 2, j = 2;  
        while (j < nums.length) {  
            if (nums[i - 2] != nums[j]) {  
                nums[i] = nums[j];  
                i++;  
            }  
            ++j;  
        }  
        return i;  
    }  
}
```


# 136、只出现一次的数字

给你一个 **非空** 整数数组 `nums` ，除了某个元素只出现一次以外，其余每个元素均出现两次。找出那个只出现了一次的元素。

你必须设计并实现线性时间复杂度的算法来解决此问题，且该算法只使用常量额外空间。

```java
class Solution {
    public int singleNumber(int[] nums) {
        for (int i = 1; i < nums.length ; i++){
            // （1）相同值异或，结果为0   （2）任何数与0疑惑都为该数
            nums[0] = nums[0] ^ nums[i];
        }
        return nums[0];
    }
}
```


# 349、两个数组的交集

给定两个数组 nums1 和 nums2 ，返回 它们的 
交集
输出结果中的每个元素一定是 唯一 的。我们可以 不考虑输出结果的顺序 。

示例：
输入：nums1 = [1,2,2,1], nums2 = [2,2]
输出：[2]

```java
class Solution {  
    public int[] intersection(int[] nums1, int[] nums2) {  
        // 排序  
        Arrays.sort(nums1);  
        Arrays.sort(nums2);  
  
        int a = nums1.length;  
        int b = nums2.length;  
        int i = 0, j = 0;  
  
        // hashset 存储时会去重  
        HashSet<Integer> set = new HashSet<>();  
        while (i < a && j < b) {  
            if (nums1[i] == nums2[j]) {  
                set.add(nums1[i]);  
                i++;  
                j++;  
                // 谁小，谁的指针需要往后移动  
            } else if (nums1[i] < nums2[j]) {  
                i++;  
            } else {  
                j++;  
            }  
        }  
        int[] res = new int[set.size()];  
        int index = 0;  
        for (int num : set) {  
            res[index++] = num;  
        }  
        return res;  
    }  
}
```

