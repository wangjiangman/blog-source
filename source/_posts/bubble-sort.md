title: "冒泡排序"
date: 2013-04-23
tags: 算法
categories: 技术
---

冒泡排序是一个常见的交换排序方法。冒泡的算法大意：在未排序的队列中从头遍历，开始比较相邻的2个元素，如果这个元素不符合要求的顺序，则交换位置，经过一遍比较之后，最小的元素就会到达序列的首位；然后将剩下的序列作为未排序的序列，重复上面的算法，直到整个序列排序完成。由于在排序的过程中，一直向前移动，看起来有点像水里的气泡向水面漂浮一样，故得此名。<!--more-->

冒泡排序的时间复杂度为 O(n<sup>2</sup>)。下面是一个简单的java代码例子

``` java
public void sort(int[] input) {
	for (int i = input.length - 1; i >= 1; i--) {
		for (int j = i; j >= 1; j--) {
			if (input[j] < input[j-1]) {
				int tmp = input[j];
				input[j] = input[j-1];
				input[j-1] = tmp;
			}
		}
	}
}
```

有时候，没必要非得2个相邻的元素比较，只要每一遍将最小/大交换到未排序序列的首/尾就好了。下面是一个伪实现：

``` java
public void sort(int[] input) {
	for (int i = input.length -1; i >= 0; i--) {
		for (int j = i-1; j >= 0; j--) {
			if (input[i] < input[j]) {
				int tmp = input[i];
				input[i] = input[j];
				input[j] = tmp;
			}
		}
	}
}
```

冒泡排序是一种交换排序，其重点在于交换。另一个重点在于相邻的元素，不停的比较相邻的元素。