# 排序算法

## 冒泡排序

冒泡排序通过遍历数组中所有元素的，将最大（小）的一个值放入数组尾（头）部，然后再次遍历数组中剩余的所有元素将最大元素放入数组尾部，直到将所有元素遍历结束，完成排序。

### 代码
``` java
    private void bubbleSort(int[] nums) {
        for (int i = 0; i < nums.length; i++) { // 4
            for (int j = 0; j < nums.length - i - 1; j++) { // 3
                if (nums[j] < nums[j+1]) { // 1
                    swap(nums, j, j + 1); // 2
                }
            }
        }
        println(nums);
    }
```

### 解析
1. 比较数组当前位置元素值和下一位置元素值的大小
2. 如果当前位置元素值大（小）于下一位置元素值，则交换两个位置的元素，使值较大（小）的元素向后移动
3. 数组位置下标向后移动，重复步骤1，2。直到将数组中最大的数字移动到数组尾部。
4. 再次遍历剩余数组，重复步骤1，2，3。将最大的元素移动到数组尾部
5. 完成排序。
---
## 选择排序
选择排序是通过遍历数组中所有的元素，获取该数组中元素值最大（小）的位置下标，然后将其与数组头部元素位置交换，然后再次遍历数组中剩余所有元素，再次选取最大（小）的元素下标与数组头部元素交换位置，直到将所有元素遍历结束，完成排序。

### 代码
``` java
private void selectSort(int[] nums) {
        int minIndex;
        for (int i = 0; i < nums.length; i++) { // 6
            minIndex = i; // 1
            for (int j = i + 1; j < nums.length; j++) { // 4
                if (nums[minIndex] > nums[j]) { // 2
                    minIndex = j;   // 3
                }
            }
            if (minIndex != i) {  // 5
                swap(nums, i, minIndex); // 5
            }
        }
        println(nums);
    }
```

### 解析
1. 记录数组当前下标为最小值下标
2. 将当前记录的最小值与当前数组值比较
3. 如果当前数组值小于当前记录的最小值，则重新记录当前数组下标为最小值下标。
4. 继续遍历，重复步骤1，2。可以获取到当前改数组中最小值的下标。
5. 如果最小值下标已经改变，则将数组头部值与最小值交换位置，此时数组头部值为该数组中的最小值。
6. 继续遍历剩余数组，重复步骤1，2，3，4，5。
7. 完成排序
---
## 插入排序
插入排序基本思想是将一个记录插入到已经排好序的有序表。

通过比较数组中某个元素与该元素位置之前的元素值大小来确定该元素的位置，如：该元素前边的数组已经有序则从该元素的位置向前遍历，如果前边的元素值大于该元素值则交换它们的位置，一直到找到第一个小于该元素的值然后停止遍历。

继续遍历剩余元素，重复上述步骤完成排序。
### 代码
``` java
    private void insetSort(int[] nums) {
        for (int i = 0; i < nums.length; i++) { // 7
            int index = i; // 1
            for (int j = i - 1; j >= 0; j--) { // 6
                if (nums[j] > nums[index]) { // 2
                    swap(nums, j, index); // 3
                    index = j; // 4
                } else {
                    break; // 5
                }
            }
        }
        println(nums);
    }
```

### 解析
1. 记录当前数值下标
2. 比较当前数值与前一位置数值的大小
3. 如果前一数值大于当前数值，则交换两者的位置
4. 如果前一数值大于当前数值，并交换位置后 重新校正当前数值下标
5. 如果前一数值小于等于当前数值，则结束本次遍历
6. 继续向前遍历，直到找到数值中小于当前数值的值
7. 继续向后遍历将后续的值不断插入到前边的有序表中
---
## 快速排序
快速排序通过一次排序将数组分为两个部分一部分比基准值大，一部分比基准值小，然后对这两个部分继续选择基准值重新分为比基准值大的部分和比基准值小的部分。一直递归到无法分割，排序结束

### 代码
``` java
    private void sort(int[] nums) {

        quickSort(nums, 0, nums.length - 1);

        print(nums);
    }

    private void quickSort(int[] nums, int left, int right) {
        if (left > right) {
            return;
        }

        int index = quickSortIndex(nums, left, right); // 0
        quickSort(nums, left, index - 1); // 10
        quickSort(nums, index + 1, right); // 10
    }
    
    private int quickSortIndex(int[] nums, int left, int right) {
        int start = left;
        int end = right;
        int key = nums[start]; // 1

        while (start < end) { // 8
            // 2
            while (start < end && nums[end] > key) {
                end--; // 3
            }
            if (start < end) {
                nums[start] = nums[end]; // 4
            }
            // 5
            while (start < end && nums[start] <= key) { 
                start++; // 6
            }
            if (start < end) {
                nums[end] = nums[start]; // 7
            }
        }
        nums[end] = key; // 9
        return end;
    }
```
### 解析
0. 目的是将该数组根据基准值划分为左右两部分，使得左侧都比基准值小，右侧都比基准值大，并返回基准值的下标
1. 从数组中获取一个基准值
2. 从数组后开始向前遍历
3. 如果左下标小于右下标并且右边下标位置的值大于基准值，则右下标向左移
4. 此时右下标值小于基准值，则交换左右下标位置的数值，使得小于基准值的该数移动到基准值左侧
5. 从数组前向后遍历
6. 如果左下标小于右下标并且左边下标位置的值小于基准值，则左下标向右移
7. 此时左下标值大于基准值，则交换左右下标位置的数值，使得大于基准值的该数移动到基准值右侧
8. 当数组左侧下标小于右侧下标的时候重复执行上述步骤2-7，直至该数组左侧值都小于基准值，右侧值都大于基准值
9. 将基准值填入数组中
10. 对基准值两侧的数组重新进行选的基准值并划分的操作，直到不可划分为止
11. 排序结束

---
## 通用代码

```java
    // 单行输出数组
    private static void print(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            if (i != nums.length - 1) {
                System.out.print(nums[i] + ",");
            } else {
                System.out.print(nums[i] + "\n");
            }
        }
    }
    
    // 多行输出数组
    private static void println(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            System.out.println(nums[i]);
        }
    }

    // 交换数组中两个位置的数值
    private static void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
```