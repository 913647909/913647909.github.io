---
title: 浅入浅出一下编译器
mathjax: false
date: 2024-01-17 11:45:14
categories: [技术]
tags: [编译器]
---
## 前言
一开始想要写这个话题的时候我是拒绝的，因为这首先肯定要自己先学一下。学了一下之后呢，感觉这个内容很油，很润。作为常年横亘在程序员面前的一座大山，其高度令人望而却步，但是在尝试了几次之后呢，吾辈也获得了一些心得值得记录一下。

从最简单的角度来说，编译器也是一个程序，所以其肯定有：数据结构 + 算法 组成。所以我们只要关注每个编译阶段的输入和输出内容的格式，就可以很容易的掌握每个阶段发生了什么。对于浅入浅出编译器来说这样的理解应该也就足够了。

一开始想唧唧歪歪的很多，但是当看完《Programming Language Pragmatics》的前2章之后觉得就拿着这本书按图索骥再结合一点《Crafting Interpreters》的实战代码就足够了。

那么就让我们开始罢。

## 第一步
把大象放冰箱分几步？大致来讲可以分为这么三步：
1. 扫描源代码
2. 解析成语法树
3. 遍历语法树求值

第一步当然是最简单的，只需要从代码的第一个字符开始逐个扫描然后转换成token列表就可以了。
```java
    Scanner scanner = new Scanner(source);
    List<Token> tokens = scanner.scanTokens();
```
也就是从`char[] ==> List<Token>`

Token的格式如下：
```java
class Token {
  final TokenType type; // token类型
  final String lexeme; // 字符串值
  final Object literal; // 实际的值
  final int line; // 行标记（报错和恢复使用）
}
```
TokenType有哪几种呢？
1. 单字符标记
2. 双字符标记
3. 字符串（变量名，字符串，数字。。。）
4. 关键字
```java
enum TokenType {
  // Single-character tokens.
  LEFT_PAREN, RIGHT_PAREN, LEFT_BRACE, RIGHT_BRACE,
  COMMA, DOT, MINUS, PLUS, SEMICOLON, SLASH, STAR,

  // One or two character tokens.
  BANG, BANG_EQUAL,
  EQUAL, EQUAL_EQUAL,
  GREATER, GREATER_EQUAL,
  LESS, LESS_EQUAL,

  // Literals.
  IDENTIFIER, STRING, NUMBER,

  // Keywords.
  AND, CLASS, ELSE, FALSE, FUN, FOR, IF, NIL, OR,
  PRINT, RETURN, SUPER, THIS, TRUE, VAR, WHILE,

  EOF
}
```
那么如何把连续的字符串数组转换成一个token列表呢？当然这并不难，只是没有接触过可能就很难想到。最简单的实现就是使用“双指针”法，在字符数组上放置两个标记位，左边标记起始位置，右边逐位向后移动并使用switch case来匹配属于哪个token类型。大致的感觉如下：
```java
    char c = advance();
    switch (c) {
      case '(': addToken(LEFT_PAREN); break;
      case '!':
        addToken(match('=') ? BANG_EQUAL : BANG);
        break;
      case '"': string(); break;
    }
```
这部分并不复杂，只是一个体力活（因为要case所有的token type）。完成之后就获得了一个`List<Token>`列表，就可以进行下一步了。

## 第二步：解析语法树
这一步属于是注入灵魂的一步，我们需要把`List<Token>`转换成语法树，重点是让前面那些没有意义的token变成有意义的语句，最后才能在第三步中用来执行。当然这一步也是比较困难的一步，按照传统会开始介绍正则表达式、上下文无关文法、BNF范式。。。但是吾辈并不想在这里搞的太复杂，当你真正跑通过一次编译过程之后再来回看这些概念性的东西才会真正开始理解，所以这里我们就直接进入操作环节。




## 资源
[craftinginterpreters web版](https://craftinginterpreters.com/)