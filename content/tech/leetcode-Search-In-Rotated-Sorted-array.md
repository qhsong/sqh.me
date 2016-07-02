+++
Description = "Leetcode Search in Rotated sorted array 解题报告"
Tags = ["lettcode", "解题报告"]
date = "2016-07-02T17:19:55+08:00"
title = "[leetcode]Search In Rotated Sorted array 解题报告"

+++
问题描述
====
Suppose a sorted array is rotated at some pivot unknown to you beforehand.

(i.e., `0 1 2 4 5 6 7` might become `4 5 6 7 0 1 2`).

You are given a target value to search. If found in the array return its index, otherwise return -1.

You may assume no duplicate exists in the array.


这道题是前段时间在百度实习生面试的时候的一道题，当场面试的时候没有想明白，然后就挂在了这个题目上。。

今天在Leetcode上看到这个题，总结了一下思路。

面试的时候直接提出，找到分割点，这个问题就转换为简单的二分搜索问题。结果面试官说不行，Orz。。直接上来就要二分搜索。

好吧，上来如何二分搜索呢：

我们采用如下图表示该数组，长度越长表示数字越大。

![数组图](https://sqh.me/blog/wp-content/uploads/2015/07/F1.png)

`left`, `right`分别表示两端边界，`mid`为中间元素。

情况主要分以下几种：
1. `nums[mid] >= nums[left]`

![数组图](https://sqh.me/blog/wp-content/uploads/2015/07/F2.png)

 这种时候，我们可以看到，在`left`到`mid`之间必然是一个升序数组，所以我们就比较`target`和`mid`
 * `target > nums[mid]` 说明`target`在`mid+1`和`right`之间， `left = mid + 1`
 * `target <= nums[mid]` 时，需要进一步比较`target`和`right`的值。
    + `target < nums[mid]` ，说明`target`在右半部分，`left = mid + 1`
    + 否则在左半部分，`right = mid - 1`


2. `nums[mid]< nums[left]`

![数组图](https://sqh.me/blog/wp-content/uploads/2015/07/F3.png)

这个时候，我们必然可以得出`mid`到`right`之间是一个升序的数组，同理
* `target < nums[mid]`, 说明目标数字在`left`到`mid`之间，直接`left = mid + 1`
* `target >= nums[mid]`,需要进一步比较：
     + `target < nums[mid]`时，说明`target`在右半部分，`left = mid + 1`
     + 否则，`right = mid - `


代码见[我的Github](https://github.com/qhsong/leetcode-path/blob/master/33-Search-in-Rotated-Sorted-Array/solution.c)
我并没有对逻辑进行简化，如果对逻辑进行简化的话会更简单。脑子还是不够灵活啊。。

希望你喜欢。

