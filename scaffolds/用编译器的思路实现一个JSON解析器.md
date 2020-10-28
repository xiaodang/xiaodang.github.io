---
title: 用编译器的思路实现一个JSON解析器
date: 2020-10-28 12:08:50
categories: 编程
tags: [JavaScript,编译器]
toc: true
---
学习编译器原理的时候，想到了一个面试题，如何把字符串解析成`JSON`对象。今天动手实现一遍。
## JSON的数据类型
* 字符串
* 数字
* 对象
* 数组
* 布尔
* null
  
JSON 中的字符串必须用双引号包围。

## 思路分析
编译器的第一步是词义分析和语义分析，生成AST，我发现AST本身就是结构化的对象，和JavaScript中的JSON类似，所以只需要让生成的AST符合JSON的规范就可以。

##

