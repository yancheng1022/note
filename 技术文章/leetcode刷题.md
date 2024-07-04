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

# 211、添加与搜索单词 - 数据结构设计

请你设计一个数据结构，支持 添加新单词 和 查找字符串是否与任何先前添加的字符串匹配 。
实现词典类 WordDictionary ：
WordDictionary() 初始化词典对象
void addWord(word) 将 word 添加到数据结构中，之后可以对它进行匹配
bool search(word) 如果数据结构中存在字符串与 word 匹配，则返回 true ；否则，返回  false 。word 中可能包含一些 '.' ，每个 . 都可以表示任何一个字母。

示例：
输入：
["WordDictionary","addWord","addWord","addWord","search","search","search","search"]
[[],["bad"],["dad"],["mad"],["pad"],["bad"],[".ad"],["b.."]]

```java
class WordDictionary {  
  
    private Node root;  
  
    private class Node {  
        public boolean isWord;  
        public TreeMap<Character, Node> next;  
  
        public Node(boolean isWord) {  
            this.isWord = isWord;  
            next = new TreeMap<>();  
        }  
  
        public Node() {  
            this(false);  
        }  
    }  
  
    public WordDictionary() {  
        root = new Node();  
    }  
  
    public void addWord(String word) {  
        Node cur = root;  
        for (int i = 0; i < word.length(); i++) {  
            char c = word.charAt(i);  
            if (cur.next.get(c) == null) {  
                cur.next.put(c, new Node());  
            }  
            cur = cur.next.get(c);  
        }  
        cur.isWord = true;  
  
    }  
  
    public boolean search(String word) {  
        return match(root, word, 0);  
    }  
  
    private boolean match(Node node, String word, int index) {  
        if (index == word.length()) {  
            return node.isWord;  
        }  
        char c = word.charAt(index);  
        if ('.' != c) {  
            if (node.next.get(c) == null) {  
                return false;  
            }  
            return match(node.next.get(c), word, index + 1);  
        } else {  
            for (char nextChar : node.next.keySet()) {  
                if (match(node.next.get(nextChar), word, index + 1)) {  
                    return true;  
                }  
            }  
            return false;  
        }  
    }  
}
```


# 303、区域和检索 - 数组不可变

给定一个整数数组  nums，处理以下类型的多个查询:

计算索引 left 和 right （包含 left 和 right）之间的 nums 元素的 和 ，其中 left <= right
实现 NumArray 类：

NumArray(int[] nums) 使用数组 nums 初始化对象
int sumRange(int i, int j) 返回数组 nums 中索引 left 和 right 之间的元素的 总和 ，包含 left 和 right 两点（也就是 nums[left] + nums[left + 1] + ... + nums[right] )
 

输入：
["NumArray", "sumRange", "sumRange", "sumRange"]
[[[-2, 0, 3, -5, 2, -1]], [0, 2], [2, 5], [0, 5]]

```java
class NumArray {  
  
    private int[] sum;  
  
    public NumArray(int[] nums) {  
        sum = new int[nums.length + 1];  
        sum[0] = 0;  
        for (int i = 1; i < sum.length; i++) {  
            sum[i] = sum[i - 1] + nums[i - 1];  
        }  
    }  
  
    public int sumRange(int left, int right) {  
        return sum[right + 1] - sum[left];  
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

# 617、合并二叉树

给你两棵二叉树： root1 和 root2 。
想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，不为 null 的节点将直接作为新二叉树的节点。
返回合并后的二叉树。
注意: 合并过程必须从两个树的根节点开始

![image.png](https://yancey-note-img.oss-cn-beijing.aliyuncs.com/20240704163000.png)

输入：root1 = [1,3,2,5], root2 = [2,1,3,null,4,null,7]
输出：[3,4,5,5,4,null,7]

```java
class Solution {  
    // 递归  
    public TreeNode mergeTrees(TreeNode root1, TreeNode root2) {  
        if (root1 == null) return root2;  
        if (root2 == null) return root1;  
        root1.val += root2.val;  
        root1.left = mergeTrees(root1.left, root2.left);  
        root1.right = mergeTrees(root1.right, root2.right);  
        return root1;  
    }  
}
```


# 714、买卖股票最佳时机含手续费

给定一个整数数组 prices，其中 prices[i]表示第 i 天的股票价格 ；整数 fee 代表了交易股票的手续费用。
你可以无限次地完成交易，但是你每笔交易都需要付手续费。如果你已经购买了一个股票，在卖出它之前你就不能再继续购买股票了。
返回获得利润的最大值。
注意：这里的一笔交易指买入持有并卖出股票的整个过程，每笔交易你只需要为支付一次手续费。

示例 1：

输入：prices = [1, 3, 2, 8, 4, 9], fee = 2
输出：8
解释：能够达到的最大利润:  
在此处买入 prices[0] = 1
在此处卖出 prices[3] = 8
在此处买入 prices[4] = 4
在此处卖出 prices[5] = 9
总利润: ((8 - 1) - 2) + ((9 - 4) - 2) = 8

```java
class Solution {  
    public int maxProfit(int[] prices, int fee) {  
        int n = prices.length;  
        if (n < 2) {  
            return 0;  
        }  
        int noTake = 0; // 第一天不持有股票时拥有的最多金额  
        int take = -prices[0]; //第一天持有股票拥有的最多金额  
        for (int i = 1; i < n; i++) {  
            // 第i天不持有股票（第i-1天不持有，第i-1天持有第i天卖出）  
            noTake = Math.max(noTake, take + prices[i] - fee);  
            // 第i天持有（第i-1天持有第i天不动，第i-1天不持有第i天买入）  
            take = Math.max(take, noTake - prices[i]);  
        }  
        return noTake;  
    }  
}
```

# 677、键值映射

设计一个 map ，满足以下几点:

字符串表示键，整数表示值
返回具有前缀等于给定字符串的键的值的总和
实现一个 MapSum 类：

MapSum() 初始化 MapSum 对象
void insert(String key, int val) 插入 key-val 键值对，字符串表示键 key ，整数表示值 val 。如果键 key 已经存在，那么原来的键值对 key-value 将被替代成新的键值对。
int sum(string prefix) 返回所有以该前缀 prefix 开头的键 key 的值的总和。

输入：
["MapSum", "insert", "sum", "insert", "sum"]
[[], ["apple", 3], ["ap"], ["app", 2], ["ap"]]
输出：
[null, null, 3, null, 5]

```java
class MapSum {  
  
    private Node root;  
  
    private class Node {  
        public int value;  
        public TreeMap<Character, Node> next;  
  
        public Node(int value) {  
            this.value = value;  
            next = new TreeMap<>();  
        }  
  
        public Node() {  
            this(0);  
        }  
    }  
  
  
    public MapSum() {  
        root = new Node();  
    }  
  
    public void insert(String word, int val) {  
        Node cur = root;  
        for (int i = 0; i < word.length(); i++) {  
            char c = word.charAt(i);  
            if (cur.next.get(c) == null) {  
                cur.next.put(c, new Node());  
            }  
            cur = cur.next.get(c);  
        }  
        cur.value = val;  
    }  
  
    public int sum(String word) {  
        Node cur = root;  
        for (int i = 0; i < word.length(); i++) {  
            char c = word.charAt(i);  
            if (cur.next.get(c) == null) {  
                return 0;  
            }  
            cur = cur.next.get(c);  
        }  
        return sum(cur);  
    }  
  
    private int sum(Node node) {  
        if (node.next.size() == 0){  
            return node.value;  
        }  
        int res = node.value;  
        for (char c : node.next.keySet()){  
            res += sum(node.next.get(c));  
        }  
        return res;  
    }  
}
```

# 771、宝石与石头

 给你一个字符串 jewels 代表石头中宝石的类型，另有一个字符串 stones 代表你拥有的石头。 stones 中每个字符代表了一种你拥有的石头的类型，你想知道你拥有的石头中有多少是宝石。
字母区分大小写，因此 "a" 和 "A" 是不同类型的石头。

示例 1：
输入：jewels = "aA", stones = "aAAbbbb"
输出：3

```java
class Solution {  
    public int numJewelsInStones(String jewels, String stones) {  
        Set set = new HashSet();  
        // 遍历宝石  
        for (int i = 0; i < jewels.length(); i++) {  
            set.add(jewels.charAt(i));  
        }  
        int res = 0;  
        // 遍历石头  
        for (int i = 0; i < stones.length(); i++) {  
            char stone = stones.charAt(i);  
            if (set.contains(stone)) {  
                res++;  
            }  
        }  
        return res;  
    }  
}
```

