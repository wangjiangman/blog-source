title: "快速排序"
date: 2013-04-28
tags: 算法
---

快速排序也是一种交换排序。首先选定一个基准元素（一般取第一个或最后一个元素），根据基准元素，将序列分为2个子序列，左边子序列的全部元素都小于等于基准元素，右边序列的全部元素都大于等于该基准元素。然后对左右这两个序列，递归重复上面的算法，直到序列排序完成。

快速排序的时间复杂度期望是 O(n<strong><em>log</em></strong>(n))，最慢可达到 O(n<sup>2</sup>)。一般在一个乱序的大列表中，快速排序还是最快的排序算法。

<!--more-->

以下是java代码简单实现：

``` java
public class QuickSort {

	public void sort(int[] input) {
		quickSort(input, 0, input.length-1);
	}

	private void quickSort(int[] input, int low, int high) {
		if (low < high) {  
			int middle = this.getMiddle(input, low, high);
			this.quickSort(input, low, middle-1);
			this.quickSort(input, middle+1, high);
		}
	}

	private int getMiddle(int[] input, int low, int high) {
		int tmp = input[low]; // 取一个基准元素

		while (low < high) {			
			while (low < high && input[high] &gt;= tmp) {
				high--;
			}

			input[low] = input[high];

			while (low < high && input[low] < tmp) {
				low++;
			}

			input[high] = input[low];			
		}

		input[low] = tmp;		
		return low;
	}

	public static void main(String[] args) {
		int a[]={5,4,62,99,98,54,56,17,18,23,34,15,35,25,53,51};
		new QuickSort().sort(a);
		System.out.println(Arrays.toString(a));
	}
}
```