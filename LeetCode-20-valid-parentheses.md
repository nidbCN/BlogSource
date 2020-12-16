title: '有效的括号-力扣20 栈'
author: Gaein nidb
categories:
  - 代码如诗
cover: "https://s3.ax1x.com/2020/11/12/DSu1dH.png"
tags:
  - 算法
  - 笔记
  - C语言
date: 2020-11-13 11:25:24
---
中北大学 AI+移动互联创新实验室 C语言培训作业，力扣第20题有效的括号 题解
<!-- more -->

> 这道题感觉很简单，但是吧，做起来还真不简单。对于人类来说理解判断似乎不难，但是对于计算机来说可能就比较麻烦了。起初我写了很多判断，但是依旧无法实现。经过党明**学姐**的启发，我觉得使用栈，官方题解上使用的是数组，其实数组就可以做一个简单的栈。不过数组很可能造成内存的浪费，经过李涛的启(qī)发(piàn)自己搓了一个链式栈。

# 题干

[[力扣-有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)]


> 给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串，判断字符串是否有效。
> 有效字符串需满足：
> 1. 左括号必须用相同类型的右括号闭合。
> 2. 左括号必须以正确的顺序闭合。
> 3. **注意空字符串可被认为是有效字符串。**

> 示例 1:
> ```
> 输入: "()"
> 输出: true
> ```

> 示例 2:
> ```
> 输入: "()[]{}"
> 输出: true
> ```

> 示例 3:
> ```
> 输入: "(]"
> 输出: false
> ```

> 示例 4:
> ```
> 输入: "([)]"
> 输出: false
> ```

> 示例 5:
> ```
> 输入: "{[]}"
> 输出: true
> ```

# 栈

## 思路

起初我写了很多判断，但是依旧无法实现。经过党明**学姐**的启发，我觉得使用栈，官方题解上使用的是数组，其实数组就可以做一个简单的栈。不过数组很可能造成内存的浪费，经过李涛的启(qī)发(piàn)自己搓了一个链式栈。  
做法是首先判断是否为空字符串，为空则返回`true`，不为空则遍历字符数组，当匹配到左括号`{`、`[`、`(`时候，将左括号入栈。  
继续遍历，发现右括号后将其和栈顶的左括号匹配，匹配成功则将其出栈，继续进行循环，直到读取到`'\0'`为止。  
如果
1. 没有任何括号入栈
2. 遍历结束后栈不为空
则返回`false`

## 代码

### 官方题解

```c
char pairs(char a) {
    if (a == '}') return '{';
    if (a == ']') return '[';
    if (a == ')') return '(';
    return 0;
}

bool isValid(char* s) {
    int n = strlen(s);
    if (n % 2 == 1) {
        return false;
    }
    int stk[n + 1], top = 0;
    for (int i = 0; i < n; i++) {
        char ch = pairs(s[i]);
        if (ch) {
            if (top == 0 || stk[top - 1] != ch) {
                return false;
            }
            top--;
        } else {
            stk[top++] = s[i];
        }
    }
    return top == 0;
}
```

### 我的题解

> 由于手搓了一个链式栈，所以代码很长

```c
/*AI+移动互联创新实验室 C语言培训练习题 有效的括号 | 力扣-20*/
#include <stdio.h>
#include <stdbool.h>
#include <string.h>
#include <malloc.h>

// 结构体栈
typedef struct _Node
{
    char value;
    struct _Node *next;
    struct _Node *last;
} Node;

// 结构体栈
typedef struct _Stack
{
    Node *top;
} Stack;

// 函数声明
void stackPush(Stack *st, char val);
char stackPop(Stack *st);
bool isOdd(int num);
Stack *stackInit(char in);
void bracketsTableInit();
char bracketsTable(char ch);

// 定义对应表
char table[126] = {
    0,
};

// bool isValid(char *s);

// int main(int argc, char const *argv[])
// {
//     char ch[] = "()[]{}";
//     if (isValid(ch))
//     {
//         printf("%s \n", "有效");
//     }
//     else
//     {
//         printf("%s \n", "无效");
//     }
//     return 0;
// }
// 测试用代码

bool isValid(char *s)
{
    int LENGTH = strlen(s);
    bool ret = true;

    if (!isOdd(LENGTH))
    {
        bracketsTableInit();
        if (bracketsTable(*s))
        {
            // 匹配到左括号
            Stack *st = stackInit(*s); // 入栈
            s++;                       // 移到下一个字符
            bool hasR = false;         // 默认有对应的右边为false
            for (int i = 1; i < LENGTH; i++, s++)
            {
                // 循环遍历字符数组
                if (bracketsTable(*s))
                {
                    // 左括号入栈
                    stackPush(st, *s);
                }
                else
                {
                    //右括号，设置hasR为true并且出栈
                    hasR = true;
                    char inSt = bracketsTable(stackPop(st));
                    // printf("%c %c\n",inSt,*s );
                    if (inSt != *s)
                    {
                        // 无法匹配
                        ret = false;
                        break;
                    }
                    else
                    {
                        // 匹配并跳到下一循环
                        continue;
                    }
                }
            }
            if (!hasR)
            {
                // 未找到右括号
                ret = false;
            }
            if (st->top != NULL)
            {
                // 栈未出空
                ret = false;
            }
        }
        else
        {
            ret = false;
        }
    }
    else
    {
        ret = false;
    }

    return ret;
}

Stack *stackInit(char in)
{
    Node *newNode = (Node *)malloc(sizeof(Node));  // 新建栈节点
    Stack *newSt = (Stack *)malloc(sizeof(Stack)); // 新建栈
    newNode->value = in;                           // 节点值
    newNode->next = NULL;                          // 置空节点next
    newNode->last = NULL;                          // 置空节点last
    newSt->top = newNode;                          // 栈顶指向节点
    return newSt;
}

// 入栈
void stackPush(Stack *st, char val)
{
    Node *newNode = (Node *)malloc(sizeof(Node)); // 新建栈节点
    newNode->value = val;                         // 设置栈节点的值
    newNode->next = NULL;                         // 置空节点next
    newNode->last = NULL;
    if (st->top != NULL)
    {
        // 判空
        st->top->next = newNode; // 将节点加入栈
        newNode->last = st->top; // 设置节点的last
    }
    st->top = newNode; // 指向新节点
}

// 出栈
char stackPop(Stack *st)
{
    char ret = '\0';

    if (st->top != NULL)
    {
        if (st->top->last == NULL)
        {
            // 仅有一个栈
            ret = st->top->value; // 取值
            st->top = NULL;       // 置空
            // free(st->top);
        }
        else
        {
            Node *del = st->top;  // 临时节点
            st->top = del->last;  // 修改栈顶
            st->top->next = NULL; // 取消栈与del节点的链接
            del->last = NULL;     // 取消del节点与栈的链接
            ret = del->value;     // 取ret值
            free(del);            // 释放内存
            del = NULL;           // 设置NULL
        }
    }

    return ret;
}

// 判断是否能出栈
bool stackCanPop(Stack *st)
{
    return (st->top != NULL);
}

// 判断是否为奇数
bool isOdd(int num)
{
    return num & 1 ? true : false;
}

// 初始化对应表
void bracketsTableInit()
{
    table['('] = ')';
    table['['] = ']';
    table['{'] = '}';
}

// 查询对应空格
char bracketsTable(char ch)
{
    return table[ch];
}
```

这次只使用了C语言，因为C#反转链表应该不难，`list.Reverse();`（逃）

## 结果

仅有一次循环，使用迭代的方法还是蛮快的，完成了力扣的任务。但是递归我实在不会，甚至不能很好的区分它们。所以暂时就没有写递归方法。
![力扣](https://s3.ax1x.com/2020/11/12/DSKJ74.md.jpg)

# 最后

这道题的话还是比较简单的，毕竟画起图来感觉不难，但是实际写起来还是有一定挑战性的。通过这道题顺手复习了忘干净的链表，也是非常好的。链表的题先画图理解一下有很大帮助，尤其是自己用笔手动遍历一下，利于理解过程。