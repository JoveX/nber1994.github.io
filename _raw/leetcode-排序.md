# leetcode-排序
![](/images/20190304174150834_1004108609.png)
## 各个算法的比较
![](/images/20190304174208470_1126893337.png)
## 归并排序
* 将数组打散为两两一组，两两一组进行排序，然后将各个组进行合并
![](/images/20190227145227328_376185148.png)

1. 把 n 个记录看成 n 个长度为1的有序子表；
2. 进行两两归并使记录关键字有序，得到 n/2 个长度为 2 的有序子表；
3. 重复第2步直到所有记录归并成一个长度为 n 的有序表为止。

### 归并排序非递归思路
按照先分组再合并的思路，我们设置一个逐渐变大的间隔，然后按照间隔来讲数据进行分组，并进行归并
![](/images/20190228152620799_846213437.png)
> 我们可以先将一个无序数组A，按照2位单位，分成诸多长度为2的子数组：  
如下：  
假如有数组A：[2 ,1 ,5 ,9 ,0 ,6 ,8 ,7 ,3]  
可以分成以下长度为1的子数组：  
{2}、{1}、{5}、{9}、{0}、{6}、{8}、{7}、{3}  
那么对这9个子数组进行归并排序，也即使用上面提到的代码进行排序，那么就可以得到
{1,2}、{5,9}、{0,6}、{7,8}、{3}
这样我们就有5个有序的子数组了，再讲这五个子数组两两归并，即得到：
{1,2,5,9}、{0,6,7,8}、{3}
就这样依次归并下去，即得到一个有序的数组B
{0 ,1 ,2 ,3 ,5 ,6 ,7 ,8 ,9}


```go
package main

import "fmt"

func main() {
	test_arr := []int{1, 7, 6, 1, 1, 2, 9, 5, 9, 1, 9}
	fmt.Println(bubbleSort(test_arr))
	fmt.Println(selectSort(test_arr))
	fmt.Println(insertSort(test_arr))
	fmt.Println(mergeSort(test_arr))
	fmt.Println(mergeSortUnRecursion(test_arr))
	fmt.Println(minHeapSort(test_arr))
}

//冒泡
//每一趟将最大的上升至对尾
//时间复杂度O(n2)
func bubbleSort(arr []int) []int {
	lens := len(arr)
	for i := 0; i < lens; i++ {
		for j := 0; j < lens-i-1; j++ {
			if arr[j] > arr[j+1] {
				arr[j], arr[j+1] = arr[j+1], arr[j]
			}
		}
	}
	return arr
}

//选择排序
//每次选出最小的放在队首
//time: O(n2)
func selectSort(arr []int) []int {
	lens := len(arr)
	for i := 0; i < lens; i++ {
		min := arr[i]
		minIndex := i
		for j := i; j < lens; j++ {
			if arr[j] < min {
				min = arr[j]
				minIndex = j
			}
		}
		arr[i], arr[minIndex] = arr[minIndex], arr[i]
	}
	return arr
}

//插入排序
//每次从未排序区域选出一个元素，在已排序区域找到合适位置插入
//time: O(n2)
func insertSort(arr []int) []int {
	lens := len(arr)
	for i := 1; i < lens; i++ {
		for j := 0; j < i; j++ {
			if arr[i] < arr[j] {
				arr[i], arr[j] = arr[j], arr[i]
				break
			}
		}
	}
	return arr
}

//归并排序
//将所有的元素打散为单个元素，然后进行两两合并，直到数组长度为原数组的长度
//time: O(nlogn)
//递归
func mergeSort(arr []int) []int {
	lens := len(arr)
	if lens < 2 {
		return arr
	}
	mid := lens / 2
	left := arr[:mid]
	right := arr[mid:]
	return merge(mergeSort(left), mergeSort(right))
}

//这个函数时merge两个已经排好序的两个数组
func merge(left, right []int) []int {
	res := []int{}
	for len(left) > 0 || len(right) > 0 {
		if len(left) > 0 && len(right) > 0 {
			if left[0] < right[0] {
				res = append(res, left[0])
				left = left[1:]
			} else {
				res = append(res, right[0])
				right = right[1:]
			}
		} else if len(left) > 0 {
			res = append(res, left[0])
			left = left[1:]
		} else if len(right) > 0 {
			res = append(res, right[0])
			right = right[1:]
		}
	}
	return res
}

//归并排序非递归实现
//left, mid, right三个点确定了连续两段
//该函数会合并此连续两段
func mergeByIndex(arr []int, left, mid, right int) {
	if mid > right {
		return
	}
	sortedArr := []int{}
	i := left
	j := mid + 1
	for i < mid+1 || j < right+1 {
		if i < mid+1 && j < right+1 {
			if arr[i] < arr[j] {
				sortedArr = append(sortedArr, arr[i])
				i++
			} else {
				sortedArr = append(sortedArr, arr[j])
				j++
			}
		} else if i < mid+1 {
			sortedArr = append(sortedArr, arr[i])
			i++
		} else if j < right+1 {
			sortedArr = append(sortedArr, arr[j])
			j++
		}
	}
	for k, v := range sortedArr {
		arr[k+left] = v
	}
}

func mergeSortUnRecursion(arr []int) []int {
	lens := len(arr)
	for step := 1; step < lens; step = step * 2 {
		for index := 0; index < lens; index = index + 2*step {
			mergeByIndex(arr, index, index+step-1, min(index+2*step-1, lens-1))
		}
	}
	return arr
}

func min(a, b int) int {
	if a < b {
		return a
	}
	return b
}

//堆排序 最小堆排序
//最小堆的性质是父节点小于其子节点
type minHeap struct {
	heap []int
	size int
}

func (this *minHeap) Push(v int) {
	this.heap = append(this.heap, v)
	this.size++
	this.autoFixUp()
}

func (this *minHeap) autoFixUp() {
	if 0 == this.size {
		return
	}
	v := this.heap[this.size-1]
	i := this.size - 1
	j := (i - 1) / 2
	for i > 0 {
		if this.heap[j] <= v {
			break
		}
		this.heap[i], this.heap[j] = this.heap[j], this.heap[i]
		i = j
		j = (i - 1) / 2
	}
}

func (this *minHeap) Pop() int {
	if this.size <= 0 {
		return -1
	}
	res := this.heap[0]
	this.size--
	this.autoFixDown()
	return res
}

func (this *minHeap) autoFixDown() {
	if 0 == this.size {
		return
	}
	this.heap[0] = this.heap[this.size]
	this.heap = this.heap[:this.size]
	i := 0
	j := 0
	for i < this.size {
		child := -1
		if i*2+2 < this.size {
			if this.heap[i*2+2] < this.heap[i*2+1] {
				child = this.heap[i*2+2]
				j = i*2 + 2
			} else {
				child = this.heap[i*2+1]
				j = i*2 + 1
			}
		} else if i*2+1 < this.size {
			child = this.heap[i*2+1]
			j = i*2 + 1
		}
		if child > 0 && child < this.heap[i] {
			this.heap[i], this.heap[j] = this.heap[j], this.heap[i]
			i = j
		} else {
			break
		}
	}
}

func minHeapSort(arr []int) []int {
	myHeap := &minHeap{[]int{}, 0}
	for _, v := range arr {
		myHeap.Push(v)
	}
	res := []int{}
	item := 0
	for item = myHeap.Pop(); item != -1; item = myHeap.Pop() {
		res = append(res, item)
	}
	return res
}

//计数排序
//要求数字是有范围的，将数字放入指定的新开辟的空间中
```

# 引用：
- [十大排序算法](https://www.cnblogs.com/onepixel/articles/7674659.html)
