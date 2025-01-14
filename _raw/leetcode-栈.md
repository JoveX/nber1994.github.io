# leetcode-栈
```go
package main

import "fmt"

func main() {
        a := &TreeNode{7, nil, nil}
        b := &TreeNode{3, nil, nil}
        c := &TreeNode{15, nil, nil}
        d := &TreeNode{9, nil, nil}
        e := &TreeNode{20, nil, nil}
        a.Left = b
        a.Right = c
        c.Left = d
        c.Right = e
        obj := Constructor(a)
        for obj.HasNext() {
                fmt.Println(obj.Next())
        }
}

type TreeNode struct {
        Val   int
        Left  *TreeNode
        Right *TreeNode
}

//实现一个二叉搜索树迭代器。你将使用二叉搜索树的根节点初始化迭代器。
//
//调用 next() 将返回二叉搜索树中的下一个最小的数。
//
//BSTIterator iterator = new BSTIterator(root);
//iterator.next();    // 返回 3
//iterator.next();    // 返回 7
//iterator.hasNext(); // 返回 true
//iterator.next();    // 返回 9
//iterator.hasNext(); // 返回 true
//iterator.next();    // 返回 15
//iterator.hasNext(); // 返回 true
//iterator.next();    // 返回 20
//iterator.hasNext(); // 返回 false
//
//
//提示：
//
//next() 和 hasNext() 操作的时间复杂度是 O(1)，并使用 O(h) 内存，其中 h 是树的高度。
//你可以假设 next() 调用总是有效的，也就是说，当调用 next() 时，BST 中至少存在一个下一个最小的数。

/**
 * Definition for a binary tree node.
 * type TreeNode struct {
 *     Val int
 *     Left *TreeNode
 *     Right *TreeNode
 * }
 */
type BSTIterator struct {
        stack []*TreeNode
        size  int
}

func (this *BSTIterator) Push(v *TreeNode) {
        this.stack = append(this.stack, v)
        this.size++
}

func (this *BSTIterator) Pop() *TreeNode {
        if this.size == 0 {
                return nil
        }
        res := this.stack[this.size-1]
        this.stack = this.stack[:this.size-1]
        this.size--
        return res
}

func Constructor(root *TreeNode) BSTIterator {
        myBSTIterator := new(BSTIterator)
        myBSTIterator.stack = []*TreeNode{}
        myBSTIterator.size = 0
        for root != nil {
                myBSTIterator.Push(root)
                root = root.Left
        }
        return *myBSTIterator
}

/** @return the next smallest number */
func (this *BSTIterator) Next() int {
        res := this.Pop()
        item := res.Right
        for item != nil {
                this.Push(item)
                item = item.Left
        }
        return res.Val
}

/** @return whether we have a next smallest number */
func (this *BSTIterator) HasNext() bool {
        return this.size > 0
}

/**
 * Your BSTIterator object will be instantiated and called as such:
 * obj := Constructor(root);
 * param_1 := obj.Next();
 * param_2 := obj.HasNext();
 */


//括号的分数
//给定一个平衡括号字符串 S，按下述规则计算该字符串的分数：
//
//() 得 1 分。
//AB 得 A + B 分，其中 A 和 B 是平衡括号字符串。
//(A) 得 2 * A 分，其中 A 是平衡括号字符串。
//
//
//示例 1：
//
//输入： "()"
//输出： 1
//示例 2：
//
//输入： "(())"
//输出： 2
//示例 3：
//
//输入： "()()"
//输出： 2
//示例 4：
//
//输入： "(()(()))"
//输出： 6
//
//
//提示：
//
//S 是平衡括号字符串，且只含有 ( 和 ) 。
//2 <= S.length <= 50
type Stack struct {
        stack []int
        size  int
}

func (this *Stack) push(v int) {
        this.stack = append(this.stack, v)
        this.size++
}

func (this *Stack) pop() int {
        res := this.stack[this.size-1]
        this.stack = this.stack[:this.size-1]
        this.size--
        return res
}

func scoreOfParentheses(S string) int {
        st := &Stack{[]int{}, 0}
        sa := []byte(S)
        for _, v := range sa {
                if v == '(' {
                        st.push('(' - '0')
                } else {
                        pre := st.pop()
                        if pre == '('-'0' {
                                st.push(1)
                        } else {
                                tmp := []int{}
                                for next := pre; next != '('-'0'; next = st.pop() {
                                        tmp = append(tmp, next)
                                }
                                st.push(2 * arraySum(tmp))
                        }
                }
        }
        return arraySum(st.stack)
}

func arraySum(nums []int) int {
        sum := 0
        for _, v := range nums {
                if v > 0 {
                        sum += v
                }
        }
        return sum
}

//用队列实现栈
//使用队列实现栈的下列操作：
//
//push(x) -- 元素 x 入栈
//pop() -- 移除栈顶元素
//top() -- 获取栈顶元素
//empty() -- 返回栈是否为空
//注意:
//
//你只能使用队列的基本操作-- 也就是 push to back, peek/pop from front, size, 和 is empty 这些操作是合法的。
//你所使用的语言也许不支持队列。 你可以使用 list 或者 deque（双端队列）来模拟一个队列 , 只要是标准的队列操作即可。
//你可以假设所有操作都是有效的（例如, 对一个空的栈不会调用 pop 或者 top 操作）。

type MyStack struct {
        quene1 []int
        quene2 []int
        size   int
}

/** Initialize your data structure here. */
func Constructor2() MyStack {
        return MyStack{
                []int{},
                []int{},
                0,
        }
}

/** Push element x onto stack. */
func (this *MyStack) Push(x int) {
        this.quene1 = append(this.quene1, x)
        this.size++
}

/** Removes the element on top of the stack and returns that element. */
func (this *MyStack) Pop() int {
        for i := 0; i < this.size-1; i++ {
                this.quene2 = append(this.quene2, this.quene1[0])
                this.quene1 = this.quene1[1:]
        }
        res := this.quene1[0]
        this.quene1 = this.quene2
        this.quene2 = []int{}
        this.size--
        return res
}

/** Get the top element. */
func (this *MyStack) Top() int {
        for i := 0; i < this.size-1; i++ {
                this.quene2 = append(this.quene2, this.quene1[0])
                this.quene1 = this.quene1[1:]
        }
        res := this.quene1[0]
        this.quene2 = append(this.quene2, res)
        this.quene1 = this.quene2
        this.quene2 = []int{}
        return res
}

/** Returns whether the stack is empty. */
func (this *MyStack) Empty() bool {
        return this.size < 1
}

/**
 * Your MyStack object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * param_2 := obj.Pop();
 * param_3 := obj.Top();
 * param_4 := obj.Empty();
 */

//用栈实现队列
//使用栈实现队列的下列操作：
//
//push(x) -- 将一个元素放入队列的尾部。
//pop() -- 从队列首部移除元素。
//peek() -- 返回队列首部的元素。
//empty() -- 返回队列是否为空。
//示例:
//
//MyQueue queue = new MyQueue();
//
//queue.push(1);
//queue.push(2);
//queue.peek();  // 返回 1
//queue.pop();   // 返回 1
//queue.empty(); // 返回 false
//说明:
//
//你只能使用标准的栈操作 -- 也就是只有 push to top, peek/pop from top, size, 和 is empty 操作是合法的。
//你所使用的语言也许不支持栈。你可以使用 list 或者 deque（双端队列）来模拟一个栈，只要是标准的栈操作即可。
//假设所有操作都是有效的 （例如，一个空的队列不会调用 pop 或者 peek 操作）。
type MyQueue struct {
        quene1 []int
        quene2 []int
        size   int
}

/** Initialize your data structure here. */
func Constructor() MyQueue {
        return MyQueue{
                []int{},
                []int{},
                0,
        }
}

/** Push element x to the back of queue. */
func (this *MyQueue) Push(x int) {
        this.quene1 = append([]int{x}, this.quene1...)
        this.size++
}

/** Removes the element from in front of queue and returns that element. */
func (this *MyQueue) Pop() int {
        for i := 0; i < this.size-1; i++ {
                item := this.quene1[0]
                this.quene2 = append(this.quene2, item)
                this.quene1 = this.quene1[1:]
        }
        res := this.quene1[0]
        this.quene1 = this.quene2
        this.quene2 = []int{}
        this.size--
        return res
}

/** Get the front element. */
func (this *MyQueue) Peek() int {
        for i := 0; i < this.size-1; i++ {
                item := this.quene1[0]
                this.quene2 = append(this.quene2, item)
                this.quene1 = this.quene1[1:]
        }
        res := this.quene1[0]
        this.quene2 = append(this.quene2, res)
        this.quene1 = this.quene2
        this.quene2 = []int{}
        return res
}

/** Returns whether the queue is empty. */
func (this *MyQueue) Empty() bool {
        return this.size < 1
}

/**
 * Your MyQueue object will be instantiated and called as such:
 * obj := Constructor();
 * obj.Push(x);
 * param_2 := obj.Pop();
 * param_3 := obj.Peek();
 * param_4 := obj.Empty();
 */

//柱状图中最大的柱形
//给定 n 个非负整数，用来表示柱状图中各个柱子的高度。每个柱子彼此相邻，且宽度为 1 。
//
//求在该柱状图中，能够勾勒出来的矩形的最大面积。
//
//以上是柱状图的示例，其中每个柱子的宽度为 1，给定的高度为 [2,1,5,6,2,3]。
//
//图中阴影部分为所能勾勒出的最大矩形面积，其面积为 10 个单位。
//
//示例:
//
//输入: [2,1,5,6,2,3]
//输出: 10
func largestRectangleArea(heights []int) int {
        //每次stack中剩余的最后一个元素，可以确定它之前的都比它大,所以下标是-1
        if len(heights) == 0 || heights == nil {
                return 0
        }
        heights = append(heights, -1)
        stack := []int{}
        res := 0
        for k, v := range heights {
                if len(stack) == 0 || v >= heights[stack[len(stack)-1]] {
                        stack = append(stack, k)
                } else {
                        for len(stack) != 0 && v < heights[stack[len(stack)-1]] {
                                h := heights[stack[len(stack)-1]]
                                stack = stack[:len(stack)-1]
                                left := -1
                                if len(stack) != 0 {
                                        left = stack[len(stack)-1]
                                }
                                if res < h*(k-left-1) {
                                        res = h * (k - left - 1)
                                }
                        }
                        stack = append(stack, k)
                }
        }
        return res
}

//最大矩形
//给定一个仅包含 0 和 1 的二维二进制矩阵，找出只包含 1 的最大矩形，并返回其面积。
//示例:
//输入:
//[
//  ["1","0","1","0","0"],
//  ["1","0","1","1","1"],
//  ["1","1","1","1","1"],
//  ["1","0","0","1","0"]
//]
//输出: 6
//题目难度：困难
//通过次数：3.1K
//提交次数：7.5K
//贡献者：LeetCode
//相关话题
//
//相似题目
//柱状图中最大的矩形最大正方形
func maximalRectangle(matrix1 [][]byte) int {
        if matrix1 == nil || len(matrix1) == 0 || len(matrix1[0]) == 0 {
                return 0
        }
        matrix := [][]int{}
        for k, v := range matrix1 {
                item := []int{}
                for key, val := range v {
                        item = append(item, int(val-'0'))
                        if k < 1 {
                                continue
                        }
                        if val == '1' {
                                item[key] += matrix[k-1][key]
                        }
                }
                matrix = append(matrix, item)
        }
        res := 0
        for _, v := range matrix {
                stack := []int{}
                v = append(v, -1)
                for key, val := range v {
                        if len(stack) == 0 || val >= v[stack[len(stack)-1]] {
                                stack = append(stack, key)
                        } else {
                                for len(stack) != 0 && val < v[stack[len(stack)-1]] {
                                        h := v[stack[len(stack)-1]]
                                        stack = stack[:len(stack)-1]
                                        left := -1
                                        if len(stack) != 0 {
                                                left = stack[len(stack)-1]
                                        }
                                        if res < h*(key-left-1) {
                                                res = h * (key - left - 1)
                                        }
                                }
                                stack = append(stack, key)
                        }
                }
        }
        return res
}
 

```