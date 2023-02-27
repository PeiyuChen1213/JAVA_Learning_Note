# 常见算法总结（AI版）

## 双指针

### 解题思路

双指针算法是指使用两个指针分别从数组的两端开始移动，根据题目的要求来调整指针的移动方式。通常适用于有序数组的查找和多个数组的合并等问题。

双指针算法可以分为以下两种情况：

- 两个指针分别从数组的两端开始移动，相向而行。
- 两个指针分别从数组的同一端开始移动，相向而行或同向而行。

### 示例

#### 例子 1：两数之和 II - 输入有序数组

给定一个已按照升序排列的有序数组，找到两个数使得它们相加之和等于目标数。

解决方法：使用双指针算法，分别从数组的两端开始移动。如果两个数的和等于目标数，返回它们的下标；如果两个数的和小于目标数，则左指针右移一位；如果两个数的和大于目标数，则右指针左移一位。

```java
class Solution {
    public int[] twoSum(int[] numbers, int target) {
        int left = 0, right = numbers.length - 1;
        while (left < right) {
            int sum = numbers[left] + numbers[right];
            if (sum == target) {
                return new int[] {left + 1, right + 1};
            } else if (sum < target) {
                left++;
            } else {
                right--;
            }
        }
        return null;
    }
}
```

#### 例子 2：移除元素

给你一个数组 nums 和一个值 val，你需要原地移除所有数值等于 val 的元素，并返回移除后数组的新长度。

解决方法：使用双指针算法，分别从数组的两端开始移动。如果左指针指向的数值等于 val，与右指针指向的数值交换；否则左指针右移一位。

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int i = 0, j = nums.length - 1;
        while (i <= j) {
            if (nums[i] == val) {
                nums[i] = nums[j];
                j--;
            } else {
                i++;
            }
        }
        return i;
    }
}
```

## 递归

### 解题思路

递归是指函数自己调用自己。在解决一些复杂的问题时，可以将问题分解成若干个子问题，然后用递归的方式分别解决子问题。

递归的实现需要满足以下三个条件：

- 一个递归函数调用自己。
- 一个递归函数必须有一个终止条件。
- 递归过程中需要保存每个子问题的结果。

### 示例

#### 例子 1：二叉树的中序遍历

给定一个二叉树的根节点，返回它的中序遍历。

解决方法：使用递归的方式，对于二叉树的每个节点，先递归访问它的左子树，然后访问它本身，最后递归访问它的右子树。

```java
class Solution {
    public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> res = new ArrayList<>();
        inorderTraversal(root, res);
        return res;
    }

    private void inorderTraversal(TreeNode root, List<Integer> res) {
        if (root == null) {
            return;
        }
        inorderTraversal(root.left, res);
        res.add(root.val);
        inorderTraversal(root.right, res);
    }
}
```

#### 例子 2：斐波那契数列

求斐波那契数列的第 n 项。

解决方法：使用递归的方式，斐波那契数列的第 n 项等于第 n-1 项和第 n-2 项的和。需要注意的是，递归的效率较低，因此可以使用记忆化搜索或动态规划的方式进行优化。

```java
class Solution {
    public int fib(int n) {
        if (n == 0 || n == 1) {
            return n;
        }
        return fib(n - 1) + fib(n - 2);
    }
}
```