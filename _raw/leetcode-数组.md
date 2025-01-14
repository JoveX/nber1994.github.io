# leetcode-数组
```go
package main

import (
	"fmt"
	"sort"
)

func main() {
	fmt.Println(twoSum1([]int{2, 11, 7, 15}, 9))
	fmt.Println(twoSum2([]int{2, 11, 7, 15}, 9))
}

//两数之和
//给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
//
//你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。
//
//示例:
//
//给定 nums = [2, 7, 11, 15], target = 9
//
//因为 nums[0] + nums[1] = 2 + 7 = 9
//所以返回 [0, 1]
//两个指针向中间逼近
func twoSum1(nums []int, target int) []int {
	lens := len(nums)
	oldNums := make([]int, lens)
	copy(oldNums[:], nums)
	left := 0
	right := lens - 1
	sortedNums := sort.IntSlice(nums)
	sort.Stable(sortedNums)
	res := []int{}
	for left < right {
		//右边的指针要移动到已经不能移动位置
		for sortedNums[left]+sortedNums[right] > target {
			right--
		}
		if sortedNums[left]+sortedNums[right] == target {
			break
		}
		//左边的指针移动到不能移动位置
		for sortedNums[left]+sortedNums[right] < target {
			left++
		}
		if sortedNums[left]+sortedNums[right] == target {
			break
		}
	}
	for k, v := range oldNums {
		if v == sortedNums[left] || v == sortedNums[right] {
			res = append(res, k)
		}
	}
	return res
}

//空间换时间
//以 差值-> 键值 作为map
func twoSum2(nums []int, target int) []int {
	myMap := map[int]int{}
	for k, v := range nums {
		if i, exist := myMap[v]; exist {
			return []int{i, k}
		}
		myMap[target-v] = k
	}
	return []int{}
}

//搜索插入位置
//给定一个排序数组和一个目标值，在数组中找到目标值，并返回其索引。如果目标值不存在于数组中，返回它将会被按顺序插入的位置。
//
//你可以假设数组中无重复元素。
//
//示例 1:
//
//输入: [1,3,5,6], 5
//输出: 2
//示例 2:
//
//输入: [1,3,5,6], 2
//输出: 1
//示例 3:
//
//输入: [1,3,5,6], 7
//输出: 4
//示例 4:
//
//输入: [1,3,5,6], 0
//输出: 0
func searchInsert(nums []int, target int) int {
	res := 0
	for _, v := range nums {
		if v == target || v > target {
			break
		}
		res++
	}
	return res
}

//在排序数组中查找元素的第一个和最后一个位置
//给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置。
//
//你的算法时间复杂度必须是 O(log n) 级别。
//
//如果数组中不存在目标值，返回 [-1, -1]。
//
//示例 1:
//
//输入: nums = [5,7,7,8,8,10], target = 8
//输出: [3,4]
//示例 2:
//
//输入: nums = [5,7,7,8,8,10], target = 6
//输出: [-1,-1]
func searchRange(nums []int, target int) []int {
	res := []int{-1, -1}
	for k, v := range nums {
		if v == target {
			if res[0] == -1 {
				res[0] = k
				res[1] = k
			} else {
				res[1] = k
			}
		} else {
			if res[0] == -1 {
			} else {
				break
			}
		}
	}
	return res
}


//旋转图像
//给定一个 n × n 的二维矩阵表示一个图像。
//
//将图像顺时针旋转 90 度。
//
//说明：
//
//你必须在原地旋转图像，这意味着你需要直接修改输入的二维矩阵。请不要使用另一个矩阵来旋转图像。
//
//示例 1:
//
//给定 matrix =
//[
//  [1,2,3],
//  [4,5,6],
//  [7,8,9]
//],
//
//原地旋转输入矩阵，使其变为:
//[
//  [7,4,1],
//  [8,5,2],
//  [9,6,3]
//]
//示例 2:
//
//给定 matrix =
//[
//  [ 5, 1, 9,11],
//  [ 2, 4, 8,10],
//  [13, 3, 6, 7],
//  [15,14,12,16]
//],
//
//原地旋转输入矩阵，使其变为:
//[
//  [15,13, 2, 5],
//  [14, 3, 4, 1],
//  [12, 6, 8, 9],
//  [16, 7,10,11]
//]

func rotate(matrix [][]int) {
        //首先根据中轴线进行翻转
        lens := len(matrix)
        for i := 0; i < lens; i++ {
                for j := 0; j+i < lens; j++ {
                        matrix[i+j][i], matrix[i][i+j] = matrix[i][i+j], matrix[i+j][i]
                }
        }
        //再进行左右互换
        for i := 0; i < lens; i++ {
                for j := 0; j < lens/2; j++ {
                        matrix[i][j], matrix[i][lens-j-1] = matrix[i][lens-j-1], matrix[i][j]
                }
        }
}

//搜索旋转排序数组
//假设按照升序排序的数组在预先未知的某个点上进行了旋转。
//
//( 例如，数组 [0,1,2,4,5,6,7] 可能变为 [4,5,6,7,0,1,2] )。
//
//搜索一个给定的目标值，如果数组中存在这个目标值，则返回它的索引，否则返回 -1 。
//
//你可以假设数组中不存在重复的元素。
//
//你的算法时间复杂度必须是 O(log n) 级别。
//
//示例 1:
//
//输入: nums = [4,5,6,7,0,1,2], target = 0
//输出: 4
//示例 2:
//
//输入: nums = [4,5,6,7,0,1,2], target = 3
//输出: -1
func search(nums []int, target int) int {
        lens := len(nums)
        left, right := 0, lens-1
        for left <= right {
                mid := (left + right) / 2
                if nums[mid] == target {
                        return mid
                } else if nums[left] <= nums[mid] {
                        if nums[left] <= target && target < nums[mid] {
                                right = mid
                        } else {
                                left = mid + 1
                        }
                } else {
                        if nums[mid] < target && target <= nums[right] {
                                left = mid
                        } else {
                                right = mid - 1
                        }
                }
        }
        return -1
}

//搜索旋转排序数组II
//假设按照升序排序的数组在预先未知的某个点上进行了旋转。
//
//( 例如，数组 [0,0,1,2,2,5,6] 可能变为 [2,5,6,0,0,1,2] )。
//
//编写一个函数来判断给定的目标值是否存在于数组中。若存在返回 true，否则返回 false。
//
//示例 1:
//
//输入: nums = [2,5,6,0,0,1,2], target = 0
//输出: true
//示例 2:
//
//输入: nums = [2,5,6,0,0,1,2], target = 3
//输出: false
//进阶:
//
//这是 搜索旋转排序数组 的延伸题目，本题中的 nums  可能包含重复元素。
//这会影响到程序的时间复杂度吗？会有怎样的影响，为什么？
func searchII(nums []int, target int) bool {
        lens := len(nums)
        left, right := 0, lens-1
        for left <= right {
                mid := (left + right) / 2
                if nums[mid] == target {
                        return true
                } else if nums[left] < nums[mid] {
                        if nums[left] <= target && target < nums[mid] {
                                right = mid
                        } else {
                                left = mid + 1
                        }
                } else if nums[left] > nums[mid] {
                        if nums[mid] < target && target <= nums[right] {
                                left = mid
                        } else {
                                right = mid - 1
                        }
                } else {
                        left++
                }
        }
        return false
}

//无重复字符的最长子串
//给定一个字符串，请你找出其中不含有重复字符的 最长子串 的长度。
//
//示例 1:
//
//输入: "abcabcbb"
//输出: 3
//解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
//示例 2:
//
//输入: "bbbbb"
//输出: 1
//解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
//示例 3:
//
//输入: "pwwkew"
//输出: 3
//解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
//     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
//
func lengthOfLongestSubstring1(s string) int {
        sa := []byte(s)
        byteMap := map[byte]int{}
        res, count := 0, 0
        for i := 0; i < len(sa); {
                if k, exist := byteMap[sa[i]]; exist {
                        if count > res {
                                res = count
                        }
                        count = 0
                        i = k + 1
                        byteMap = map[byte]int{}
                } else {
                        byteMap[sa[i]] = i
                        count++
                        i++
                }
        }
        if count > res {
                res = count
        }
        return res
}

func lengthOfLongestSubstring2(s string) int {
        sa := []byte(s)
        byteMap := map[byte]int{}
        left, right, maxLen := 0, 0, 0
        for i, v := range sa {
                if k, exist := byteMap[v]; !exist {
                        byteMap[v] = i
                } else {
                        if byteMap[v] >= left {
                                left = k + 1
                        }
                        byteMap[v] = i
                }
                if (right-left)+1 > maxLen {
                        maxLen = right - left + 1
                }
                right++
        }
        return maxLen
}

//搜索二维矩阵
//编写一个高效的算法来判断 m x n 矩阵中，是否存在一个目标值。该矩阵具有如下特性：
//
//每行中的整数从左到右按升序排列。
//每行的第一个整数大于前一行的最后一个整数。
//示例 1:
//
//输入:
//matrix = [
//  [1,   3,  5,  7],
//  [10, 11, 16, 20],
//  [23, 30, 34, 50]
//]
//target = 3
//输出: true
//示例 2:
//
//输入:
//matrix = [
//  [1,   3,  5,  7],
//  [10, 11, 16, 20],
//  [23, 30, 34, 50]
//]
//target = 13
//输出: false
func searchMatrix1(matrix [][]int, target int) bool {
        if len(matrix) == 0 || len(matrix[0]) == 0 {
                return false
        }
        n := len(matrix[0])
        m := len(matrix)
        i, j := 0, 0
        for {
                if i == m-1 || matrix[i][0] == target || matrix[i+1][0] > target {
                        break
                }
                i++
        }
        for {
                if j == n {
                        break
                }
                if matrix[i][j] == target {
                        return true
                }
                j++
        }
        return false
}

//搜索二维矩阵II
//编写一个高效的算法来搜索 m x n 矩阵 matrix 中的一个目标值 target。该矩阵具有以下特性：
//
//每行的元素从左到右升序排列。
//每列的元素从上到下升序排列。
//示例:
//
//现有矩阵 matrix 如下：
//
//[
//  [1,   4,  7, 11, 15],
//  [2,   5,  8, 12, 19],
//  [3,   6,  9, 16, 22],
//  [10, 13, 14, 17, 24],
//  [18, 21, 23, 26, 30]
//]
//给定 target = 5，返回 true。
//
//给定 target = 20，返回 false。
func searchMatrix(matrix [][]int, target int) bool {
        if len(matrix) == 0 || len(matrix[0]) == 0 {
                return false
        }
        m, n := len(matrix), len(matrix[0])
        i, j := m-1, 0
        for i >= 0 || j <= n-1 {
                if matrix[i][j] == target {
                        return true
                } else if matrix[i][j] < target {
                        if j < n-1 {
                                j++
                                continue
                        } else {
                                return false
                        }
                } else {
                        if i > 0 {
                                i--
                                continue
                        } else {
                                return false
                        }
                }
        }
        return false
}

//复原IP地址
//给定一个只包含数字的字符串，复原它并返回所有可能的 IP 地址格式。
//
//示例:
//
//输入: "25525511135"
//输出: ["255.255.11.135", "255.255.111.35"]
func restoreIpAddresses1(s string) []string {
        //递归调用
        res := []string{}
        recursiveFunc(s, "", 0, &res)
        return res
}

func recursiveFunc(s, pre string, n int, res *[]string) {
        //此时已经到达最后
        if n == 3 {
                i, _ := strconv.Atoi(s)
                if i >= 0 && i <= 255 && strconv.Itoa(i) == s {
                        *res = append(*res, pre+"."+s)
                }
                return
        }
        for index := 1; index < 4 && index < len(s); index++ {
                j, _ := strconv.Atoi(s[:index])
                if j >= 0 && j <= 255 && strconv.Itoa(j) == s[:index] {
                        if pre == "" {
                                recursiveFunc(s[index:], s[:index], n+1, res)
                        } else {
                                recursiveFunc(s[index:], pre+"."+s[:index], n+1, res)
                        }
                }
        }
}

func restoreIpAddresses(s string) []string {
        res := []string{}
        for a := 1; a < 4 && len(s) >= a; a++ {
                i, _ := strconv.Atoi(s[:a])
                if i < 0 || i > 255 || strconv.Itoa(i) != s[:a] {
                        continue
                }
                for b := a + 1; b < a+4 && len(s) >= b; b++ {
                        j, _ := strconv.Atoi(s[a:b])
                        if j < 0 || j > 255 || strconv.Itoa(j) != s[a:b] {
                                continue
                        }
                        for c := b + 1; c < b+4 && len(s) >= c; c++ {
                                k, _ := strconv.Atoi(s[b:c])
                                if k < 0 || k > 255 || strconv.Itoa(k) != s[b:c] {
                                        continue
                                }
                                l, _ := strconv.Atoi(s[c:])
                                if l < 0 || l > 255 || strconv.Itoa(l) != s[c:] {
                                        continue
                                }
                                res = append(res, s[:a]+"."+s[a:b]+"."+s[b:c]+"."+s[c:])
                        }
                }
        }
        return res
}

//电话号码的字母组合
//给定一个仅包含数字 2-9 的字符串，返回所有它能表示的字母组合。
//
//给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。
//1
//2 abc
//3 def
//4 ghi
//5 jkl
//6 mno
//7 pqrs
//8 tuv
//9 wxyz
//0
//
//示例:
//
//输入："23"
//输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
//说明:
//尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序
func letterCombinations1(s string) []string {
        if s == "" {
                return []string{}
        }
        keyMap := map[byte][]string{}
        keyMap['0'] = []string{""}
        keyMap['1'] = []string{""}
        keyMap['2'] = []string{"a", "b", "c"}
        keyMap['3'] = []string{"d", "e", "f"}
        keyMap['4'] = []string{"g", "h", "i"}
        keyMap['5'] = []string{"j", "k", "l"}
        keyMap['6'] = []string{"m", "n", "o"}
        keyMap['7'] = []string{"p", "q", "r", "s"}
        keyMap['8'] = []string{"t", "u", "v"}
        keyMap['9'] = []string{"w", "x", "y", "z"}
        if len(s) == 1 {
                return keyMap[s[0]]
        }
        nextRes := letterCombinations(string(s[1:]))
        res := []string{}
        for _, v := range keyMap[s[0]] {
                for _, val := range nextRes {
                        res = append(res, v+val)
                }
        }
        return res
}

func letterCombinations(digit string) []string {
        if digit == "" {
                return []string{}
        }
        keyMap := map[byte][]string{}
        keyMap['0'] = []string{""}
        keyMap['1'] = []string{""}
        keyMap['2'] = []string{"a", "b", "c"}
        keyMap['3'] = []string{"d", "e", "f"}
        keyMap['4'] = []string{"g", "h", "i"}
        keyMap['5'] = []string{"j", "k", "l"}
        keyMap['6'] = []string{"m", "n", "o"}
        keyMap['7'] = []string{"p", "q", "r", "s"}
        keyMap['8'] = []string{"t", "u", "v"}
        keyMap['9'] = []string{"w", "x", "y", "z"}
        res := []string{""}
        for _, v := range []byte(digit) {
                tmp := []string{}
                for _, val := range keyMap[v] {
                        for _, value := range res {
                                tmp = append(tmp, value+val)
                        }
                }
                res = tmp
        }
        return res
}

//括号生成
//给出 n 代表生成括号的对数，请你写出一个函数，使其能够生成所有可能的并且有效的括号组合。
//
//例如，给出 n = 3，生成结果为：
//
//[
//  "((()))",
//  "(()())",
//  "(())()",
//  "()(())",
//  "()()()"
//]
func generateParenthesis(n int) []string {
        res := []string{}
        dfs(n, n, "", &res)
        return res
}

func dfs(left, right int, s string, res *[]string) {
        if left < 0 || right < 0 || left > right {
                return
        }
        if left == 0 && right == 0 {
                *res = append(*res, s)
        }
        dfs(left-1, right, s+"(", res)
        dfs(left, right-1, s+")", res)
}

//二进制求和
//给定两个二进制字符串，返回他们的和（用二进制表示）。
//
//输入为非空字符串且只包含数字 1 和 0。
//
//示例 1:
//
//输入: a = "11", b = "1"
//输出: "100"
//示例 2:
//
//输入: a = "1010", b = "1011"
//输出: "10101"
func addBinary(a string, b string) string {
        arra, arrb := []byte(a), []byte(b)
        lensa, lensb := len(arra), len(arrb)
        res := []byte{}
        add := 0
        for i, j := lensa-1, lensb-1; i >= 0 || j >= 0 || add != 0; i, j = i-1, j-1 {
                sum := 0
                if i >= 0 && j >= 0 {
                        sum = int(arra[i]-'0') + int(arrb[j]-'0') + add
                } else if i >= 0 {
                        sum = int(arra[i]-'0') + add
                } else if j >= 0 {
                        sum = int(arrb[j]-'0') + add
                } else {
                        sum = add
                }
                add = sum / 2
                if sum%2 == 0 {
                        res = append([]byte{'0'}, res...)
                } else {
                        res = append([]byte{'1'}, res...)
                }
        }
        return string(res[:])
}

//最长公共前缀
//编写一个函数来查找字符串数组中的最长公共前缀。
//
//如果不存在公共前缀，返回空字符串 ""。
//
//示例 1:
//
//输入: ["flower","flow","flight"]
//输出: "fl"
//示例 2:
//
//输入: ["dog","racecar","car"]
//输出: ""
//解释: 输入不存在公共前缀。
//说明:
//
//所有输入只包含小写字母 a-z 。
//func longestCommonPrefix(strs []string) string {
//
//}

//合并有序数组
//给定两个有序整数数组 nums1 和 nums2，将 nums2 合并到 nums1 中，使得 num1 成为一个有序数组。
//
//说明:
//
//初始化 nums1 和 nums2 的元素数量分别为 m 和 n。
//你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。
//示例:
//
//输入:
//nums1 = [1,2,3,0,0,0], m = 3
//nums2 = [2,5,6],       n = 3
//
//输出: [1,2,2,3,5,6]
func merge(nums1 []int, m int, nums2 []int, n int) {
        if m == 0 {
                copy(nums1, nums2)
        }
        if n == 0 {
                return
        }
        for k, v := range nums2 {
                nums1[m+k] = v
        }
        for i := m; i < m+n; i++ {
                for j := 0; j < i; j++ {
                        if nums1[j] > nums1[i] {
                                nums1[i], nums1[j] = nums1[j], nums1[i]
                        }
                }
        }
}

//杨辉三角
//给定一个非负整数 numRows，生成杨辉三角的前 numRows 行。
//
//在杨辉三角中，每个数是它左上方和右上方的数的和。
//
//示例:
//
//输入: 5
//输出:
//[
//     [1],
//    [1,1],
//   [1,2,1],
//  [1,3,3,1],
// [1,4,6,4,1]
//]
func generate(numRows int) [][]int {
        if numRows == 0 {
                return nil
        }
        res := [][]int{}
        res = append(res, []int{1})
        for i := 1; i < numRows; i++ {
                tmp := []int{1}
                for j := 0; j < len(res[i-1])-1; j++ {
                        tmp = append(tmp, res[i-1][j]+res[i-1][j+1])
                }
                tmp = append(tmp, 1)
                res = append(res, tmp)
        }
        return res
}

//杨辉三角II
//给定一个非负索引 k，其中 k ≤ 33，返回杨辉三角的第 k 行。
//
//在杨辉三角中，每个数是它左上方和右上方的数的和。
//
//示例:
//
//输入: 3
//输出: [1,3,3,1]
//进阶：
//
//你可以优化你的算法到 O(k) 空间复杂度吗？
func getRow(rowIndex int) []int {
        if rowIndex == 0 {
                return []int{}
        }
        rowIndex++
        res := make([]int, rowIndex)
        res[0] = 1
        //次数rowIndex-1次
        for i := 1; i < rowIndex; i++ {
                preNext := 1
                for j := 1; j < i; j++ {
                        tmp := res[j]
                        res[j] = preNext + res[j]
                        preNext = tmp
                }
                res[i] = 1
        }
        return res
}
 

```
