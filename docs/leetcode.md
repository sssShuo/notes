# leetcode笔记



## 栈

### 删除最外层的括号

示例：

- 输入："( ( ) ( ) ) ( ( ) )"	输出："( ) ( ) ( )"
- 输入："( ( ) ( ) ) ( ( ) ) ( ( ) ( ( ) ) )"	输出："( ) ( ) ( ) ( ) ( ( ) )"

`什么是栈的思路？`

1:使用if根据情况不同进行不同操作

~~~c++
class Solution {
public:
    string removeOuterParentheses(string S) {
        string Out;
        int count = 0, flag = 0;
        for (int i = 0; i < S.size(); i++) {
            if(S[i] == '(') {
                if(flag == 1) {
                    Out = Out + S[i];
                    count += 1;
                }
                else {
                    flag = 1;
                }
            }
            else if (S[i] == ')') {
                if (count != 0) {
                    Out = Out + S[i];
                    count -= 1;
                }
                else {
                    flag = 0;
                }
            }
        }
        return Out;
    }
};
~~~

2:判断左右括号数量，相同时则是到了最外层的右括号。(tips:初始时将i设为1，使用count变量来判断左右括号是否相等)

~~~c++
class Solution {
public:
    string removeOuterParentheses(string S) {
        string Out;
        int count = 1;
        for (int i = 1; i < S.size(); i++) {
            
            if(S[i] == '(')
                count ++;
            else
                count --;
            if (count != 0)
                Out.push_back(S[i]);
            else
            {
                i++;
                count = 1;
            }
        }
        return Out;
    }
};
~~~

### 使用两个栈实现队列

用两个栈实现一个队列，实现两个函数，appendTail（在尾部插入）deleteHead（头部删除），队列中没有元素时，deleteHead操作返回-1。

1: 初始时只用一个栈s1，尾部插入时直接入栈。头部删除时创建一个新栈s2，将s1元素依次出栈压入到s2中直至s1为空。之后将s2顶部元素出栈并返回，再依次压入s1。



2：使用两个栈sin和sout。sin只负责出栈，尾部插入时直接插入到sin。sout只负责出栈，当sout为空但sin不空时，将sin的数据依次出栈压入sout。

~~~c++
class CQueue {
public:
    stack<int> sin;
    stack<int> sout;
    int size;
    CQueue() {
    //可再此检测两个初始栈是否为空。
        size = 0;
    }
    
    void appendTail(int value) {
        sin.push(value);
        size ++;
    }
    
    int deleteHead() {
        if(size == 0)   return -1;
        if(sout.empty())
        {
            while(!sin.empty())
            {
                sout.push(sin.top());
                sin.pop();
            }
        }
        int result = sout.top();
        sout.pop();
        size --;
        return result;
    }
};

/**
 * Your CQueue object will be instantiated and called as such:
 * CQueue* obj = new CQueue();
 * obj->appendTail(value);
 * int param_2 = obj->deleteHead();
 */
~~~

### 删除字符串中相邻重复项

给出由小写字母组成的字符串 S，重复项删除操作会选择两个相邻且相同的字母，并删除它们。在 S 上反复执行重复项删除操作，直到无法继续删除。在完成所有重复项删除操作后返回最终的字符串。答案保证唯一。

示例

- 输入："abbaca"	输出："ca"

定义字符串sout存储输出，利用string中栈的操作，遍历输入字符串sin，如果与sout.back()元素相同则对sout进行出栈操作，如果不想等则将sin正在遍历的那一位压入栈中。

```c++
class Solution {
public:
    string removeDuplicates(string S) {
        string sout;
        for(int i=0; i<S.length(); i++)
        {
            if(sout.size() == 0) sout.push_back(S[i]);
            else
            {
            if(S[i] == sout.back())
            {
                sout.pop_back();
            }
            else 
                sout.push_back(S[i]);
        }
        }
        return sout;
    }
};
```

tips：没有第7行检验sout的size是否为0的操作时，运行会提示越界。

### 棒球比赛得分记录

给定一字符串列表，每个字符串可以是一下四种类型之一：

1.整数（一轮的得分）：直接表示您在本轮中获得的积分数。
2."+"（一轮的得分）：表示本轮获得的得分是前两轮有效 回合得分的总和。
3."D"（一轮的得分）：表示本轮获得的得分是前一轮有效 回合得分的两倍。
4."C"（一个操作，这不是一个回合的分数）：表示您获得的最后一个有效 回合的分数是无效的，应该被移除。

最后返回所有回合得分的总和

示例：

- 输入: ["5","2","C","D","+"]
  输出: 30

- 解释: 

  第1轮：你可以得到5分。总和是：5。
  第2轮：你可以得到2分。总和是：7。
  操作1：第2轮的数据无效。总和是：5。
  第3轮：你可以得到10分（第2轮的数据已被删除）。总数是：15。
  第4轮：你可以得到5 + 10 = 15分。总数是：30。

从左到右遍历ops vector，使用stack来记录每次的得分，ops是 +、 D、 C， 时进行相应的操作将本次的得分计算出压入栈。

```c++
class Solution {
public:
    int calPoints(vector<string>& ops) {
        stack<int> grad;
        int sum = 0;
        for(int i = 0; i < ops.size(); i++)
        {
            if(ops[i] == "+")
            {
                int top = grad.top();
                grad.pop();
                int secend = grad.top();
                grad.push(top);
                grad.push(top + secend);
                sum += grad.top();
            }else if(ops[i] == "D")
            {
                int top = grad.top();
                grad.push(2 * top);
                sum += grad.top();
            }else if(ops[i] == "C")
            {
                sum -= grad.top();
                grad.pop();
            }else 
            {
                int num = stoi(ops[i]);
                grad.push(num);
                sum += num;
            }
        }
        return sum;
    }
};
```

tips：最开始思路是将stack换成了int类型的vector，整个运行时间较长。不同容器的相同操作，运行时间差距较大。

### 下一个更大元素（单调栈）

给定两个 没有重复元素 的数组 nums1 和 nums2 ，其中nums1 是 nums2 的子集。找到 nums1 中每个元素在 nums2 中的下一个比其大的值。

nums1 中数字 x 的下一个更大元素是指 x 在 nums2 中对应位置的右边的第一个比 x 大的元素。如果不存在，对应位置输出 -1 。

示例：

- 输入：nums1 = [4,1,2]，nums2 = [1,3,4,2]	输出：[-1,3,-1]

1.使用遍历，因为没有重复元素，先找到nums1中元素在nums2的位置，然后向后寻找第一个大于指定元素的数。

```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        vector<int> result;
        for(int i = 0; i < nums1.size(); i++)
        {
            int loc;
            int loc2 = -1;
            for(int j = 0; i < nums2.size(); j++)
            {
                if(nums1[i] == nums2[j])
                {
                    loc = j;
                    break;
                }
            }
            for(int j = loc + 1; j < nums2.size(); j++)
            {
                if(nums2[j] > nums1[i])
                {
                    loc2 = nums2[j];
                    break;
                }
            }
            result.push_back(loc2);
        }
        return result;
    }
};
```

该方法执行时间较长36ms，超过了11%。内存占用少，超过了97%。

2.使用单调栈的方法，将nums2数组利用单调栈对每一位nums2成员找到下一个更大元素，建立map。遍历nums1的元素，直接通过map找到对应的下一个最大元素。

```cpp
class Solution {
public:
    vector<int> nextGreaterElement(vector<int>& nums1, vector<int>& nums2) {
        stack<int> istack;
        unordered_map<int,int> imap;
        vector<int> result;
        int n1 = nums1.size();
        int n2 = nums2.size();

        for(int i = 0; i < n2; i++)
        {
            while(!istack.empty() && nums2[i] > istack.top())
            {
                imap[istack.top()] = nums2[i];
                istack.pop();
            }
            istack.push(nums2[i]);
        }
        while(!istack.empty())
        {
            imap[istack.top()] = -1;
            istack.pop();
        }
        for(int i=0; i<n1; i++)
        {
            result.push_back(imap[nums1[i]]);
        }
        return result;
    }
};
```

Tips:使用map时，内部建立红黑树来实现。而unordered_map内部通过hash表实现，不要求有序性的情况下效率较高。

### 使用队列实现栈

实现以下栈的操作：

- push(x)
- pop()
- top()
- empty()

1. 使用一个队列实现，每次进行入栈操作后将该元素之前元素从头到尾都出队列插入到队尾，这样结束后形成了栈顶元素都在队头的结构，出栈时只需要将队首元素出队。

![Push an element in stack](https://pic.leetcode-cn.com/69e61df7e86fc04ffcf6a37a5a502e40f4a651d69542e7829282b4c2013164b4-image.png)

```cpp
class MyStack {
public:
    queue<int> ique;
    /** Initialize your data structure here. */
    MyStack() {

    }
    
    /** Push element x onto stack. */
    void push(int x) {
        int size = ique.size();
        ique.push(x);
        for(int i = 0; i < size; i++)
        {
            ique.push(ique.front());
            ique.pop();
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
        int result = top();
        ique.pop();
        return result;
    }
    
    /** Get the top element. */
    int top() {
        return ique.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return ique.empty();
    }
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```

2. 使用两个队列q1和q2，每次插入时将元素插入到q2中，q2不空时将元素出队插入到q1中。pop时循环size-1次将队首数据插入到队尾，结束时队首的元素为栈顶元素，并将栈顶元素移到q2中弹出。

```cpp
class MyStack {
public:
    /** Initialize your data structure here. */
    MyStack() {

    }
    
    /** Push element x onto stack. */
    void push(int x) {
        q2.push(x);
        while(!q2.empty()){
            q1.push(q2.front());
            q2.pop();
        }
    }
    
    /** Removes the element on top of the stack and returns that element. */
    int pop() {
       int x = top();
       q2.pop();
       return x;
    }
    
    /** Get the top element. */
    int top() {
       if(q2.empty()){
           for(int i=0; i<q1.size()-1; ++i){
               q1.push(q1.front());
               q1.pop();
           }
           q2.push(q1.front());
           q1.pop();
       }
       return q2.front();
    }
    
    /** Returns whether the stack is empty. */
    bool empty() {
        return q1.empty() && q2.empty();
    }

private:
    queue<int> q1, q2;
};

/**
 * Your MyStack object will be instantiated and called as such:
 * MyStack* obj = new MyStack();
 * obj->push(x);
 * int param_2 = obj->pop();
 * int param_3 = obj->top();
 * bool param_4 = obj->empty();
 */
```

### 用栈操作构建数组

给一个目标数组`target`和一个整数`n`每次迭代，需要从`list = {1,2,3...,n}`中依序读取一个数字。使用下述操作来构建目标数组`target`

- Push
- Pop

例子：

- 输入：target = [1,3], n = 3 。输出：["Push","Push","Pop","Push"]
- 输入：target = [1,2,3], n = 3。输出：["Push","Push","Push"]

1.有点不懂这个题要干啥 0 0

```cpp
class Solution {
public:
    vector<string> buildArray(vector<int>& target, int n) {
        int size = target.size();

        vector<string> output;
        int m = 0;
        int n0 = 1;
        while(m != size)
        {
            output.push_back("Push");
            if(target[m] > n0)
            {
                output.push_back("Pop");
                n0++;
                continue;
            }
            m++;
            n0++;
        }
        return output;
    }
};
```

2.学会了C++中基于范围的for循环

```cpp
class Solution {
public:
    vector<string> buildArray(vector<int>& target, int n) {
        int cur_num = 1;
        vector<string> vec_str;
        for (int num : target) {
            while (num != cur_num) {
                vec_str.push_back("Push");
                vec_str.push_back("Pop");
                cur_num++;
            }
            vec_str.push_back("Push");
            cur_num++;
        }
        return vec_str;
    }
};

```

### 栈的最小值

请设计一个栈，除了常规栈支持的pop与push函数以外，还支持min函数，该函数返回栈元素中的最小值。执行push、pop和min操作的时间复杂度必须为O(1)。

示例：

minStack minStack = new MinStack();
minStack.push(-2);
minStack.push(0);
minStack.push(-3);
minStack.getMin();   --> 返回 -3.
minStack.pop();
minStack.top();      --> 返回 0.
minStack.getMin();   --> 返回 -2.

1.1使用单栈

使用一个变量min记录最小值，再入栈新的最小值之前将原先最小值也入栈，既每个最小值栈前相邻的是上一个最小值。出栈元素如果是最小值，则该元素出栈后将栈顶元素赋值给min，然后再将栈定元素出栈。（有些巧妙的）。

```cpp
class MinStack {
public:
    /** initialize your data structure here. */
    MinStack() {
        _min = INT_MAX;
    }
    
    void push(int x) {
        if(ista.empty())
        {
            _min = x;
            ista.push(x);
            return;
        }
        if(x <= _min)
        {
            ista.push(_min);
            _min = x;
        }
        ista.push(x);
    }
    
    void pop() {
        if(ista.size() == 1)
        {
            ista.pop();
            _min = INT_MAX;
            return ;
        }
        if(ista.top() == _min)
        {
            ista.pop();
            _min = ista.top();
        }
        ista.pop();
    }
    
    int top() {
        return ista.top();
    }
    
    int getMin() {
        return _min;
    }
    private:
        int _min;
        stack<int> ista;
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```

执行用时52ms，内存消耗14.8M。在程序设计上有优化空间可以把执行用时缩短到42ms。

1.2优化后使用单栈的算法

```cpp
class MinStack {
public:
    /** initialize your data structure here. */
    stack<int> _stack;
    int _min = INT_MAX;
    MinStack() {
        
    }
    
    void push(int x) {
        if(_min >= x){
            if(!_stack.empty()){
                _stack.push(_min);
            }
            _min = x;
        }
        _stack.push(x);
    }
    
    void pop() {
        if(_stack.empty())
            return;
        if(_stack.size() == 1)
            _min = INT_MAX;
        else if(_min == _stack.top()){//下一个元素是下一个最小值
            _stack.pop();
            _min = _stack.top();
        }
        _stack.pop();
    }
    
    int top() {
        return _stack.top();
    }
    
    int getMin() {
        return _min;
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->getMin();
 */
```

2.使用双栈，stack1依次存储元素，stack2存储改变的min值，在pop时需要进行选择，跟第1种方法相似，只是单栈变成了双栈。

```cpp
class MinStack {
public:
    /** initialize your data structure here. */
    stack<int> s;
    stack<int> Min;

    MinStack() {

    }
    
    void push(int x) {
        s.push(x);
        if(Min.empty() || x<=Min.top())
            Min.push(x);
    }
    
    void pop() {
        if(!s.empty()){
            if(s.top()==Min.top())
                Min.pop();
            s.pop();
        }
    }
    
    int top() {
        return s.top();
    }
    
    int min() {
        return Min.top();
    }
};

/**
 * Your MinStack object will be instantiated and called as such:
 * MinStack* obj = new MinStack();
 * obj->push(x);
 * obj->pop();
 * int param_3 = obj->top();
 * int param_4 = obj->min();
 */
```

### 比较含退格的字符串

给定`S`和`T`两个字符串，当他们分别输入到空白的文本编辑器后，判断两者是否相等，并返回结果。`#`表示退格字符。

注意：如果对空文本输入退格字符，文本继续为空。

示例1:

```shell
输入：S = "ab#c", T = "ad#c"
输出：true
解释：S 和 T 都会变成 “ac”。
```

示例2:

```shell
输入：S = "ab##", T = "c#d#"
输出：true
解释：S 和 T 都会变成 “”。
```

示例3:

```shell
输入：S = "a##c", T = "#a#c"
输出：true
解释：S 和 T 都会变成 “c”。
```

示例4:

```shell
输入：S = "a#c", T = "b"
输出：false
解释：S 会变成 “c”，但 T 仍然是 “b”。
```

对两个待比较字符串最相同处理：依次从头到位将字符压入栈中，当遇到`#`号时将栈顶元素弹出（需要考虑当前栈是否为空）。之后比较两个栈中元素是否相等

```cpp
class Solution {
public:
    bool backspaceCompare(string S, string T) {
        stack<int> Sstack = convert(S);
        stack<int> Tstack = convert(T);
        
        while(!Sstack.empty() && !Tstack.empty())
        {
            if(Sstack.top() == Tstack.top())
            {
                Sstack.pop();
                Tstack.pop();
            }else{
                break;
            }
        }

        return Sstack.empty() && Tstack.empty();
    }

    stack<int> convert(string s)
    {
        stack<int> istack;
        for(int i = 0; i < s.size(); i++)
        {
            if(s[i] == '#')
            {   if(istack.empty())
                    continue;
                istack.pop();
            }    
            else
                istack.push(s[i]);
            
        }
        return istack;
    }
};
```

Tips:开始没有注意到输入开头就是退格符号的问题，导致在栈pop的时候，引用地址错误。在代码27，28行加入检查处理。

