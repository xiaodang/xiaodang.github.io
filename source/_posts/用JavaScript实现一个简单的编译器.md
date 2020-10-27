---
title: 用JavaScript实现一个简单的编译器
date: 2020-10-27 20:13:24
categories: 编程
tags: [JavaScript,编译器]
toc: true
---
今天花了一下午时间看了一个`JavaScript`编译器的实现，并且自己亲手实现了一遍。非常简单的微型编译器，但是却包含了编译器基本的工作流程。一般我们几乎不太能接触到开发编译器工作，但是通过了解编译器的实现原理对我们还是非常有帮助。

## 编译器介绍
`compiler`是将一种语言转换为另外一种语言的程序。

## 编译器原理
大部分编译器的工作可以分为三个阶段：
1. Parsing解析 - 解析分为词法分析(`Lexical Analysis`)和语法分析(`Syntactic Analysis`)。解析的主要工作是把源代码转换为更加抽象的代码(`Abstract Syntax Tree` 简称 `AST`)
2. Transformation 转换 - 转换是对`AST`进行处理，方便后续的代码生成
3. Code Generation 代码生成 - 这一步将新的`AST`转化为新的代码
``` javascript
//编译流程
function compiler(input) {
    let tokens = tokenize(input);//词法分析
    let ast = parser(tokens);//语法分析
    let newAst = transformer(ast);//ast转换
    let output = codeGenerator(newAst);//代码生成
    return output;
}
```

## 实现目标
目标是把`lisp`代码编译为`C`的代码
``` lisp
 *   算术            LISP                      C
 *   3 + (4 - "2")    (add 3 (subtract 4 "2"))    add(3, subtract(4, "2"))
```
## 解析
- - -
### 词法分析
词法分析就是把源代码中的“词素”(token)找出来，比如`3 + (4 - 2)`中的数字`3、4、2`，左括号`(`，右括号`）`，操作符`+、-`，函数名`add`。
```lisp
input: 
(add 3 (subtract 4 "2"))
```
```javascript
tokens:
[
    {
        "type": "paren",//左括号
        "value": "("
    },
    {
        "type": "name",//方法名
        "value": "add"
    },
    {
        "type": "number",//数字
        "value": "3"
    },
    {
        "type": "paren",//左括号
        "value": "("
    },
    {
        "type": "name",//-操作符 
        "value": "subtract"
    },
    {
        "type": "number",//数字
        "value": "4"
    },
    {
        "type": "string",//字符串
        "value": "2"
    },
    {
        "type": "paren",//右括号
        "value": ")"
    },
    {
        "type": "paren",//右括号
        "value": ")"
    }
]
```

```javascript
//代码
function tokenize(input) {
    let cursor = 0
    let tokens = []
    while (cursor < input.length) {
        let char = input[cursor]
        if (/\s/.test(char)) { //空格 则跳过
            cursor++;
            continue;
        }
        if (char == '(') { //匹配左括号
            tokens.push({
                type: "paren",
                value: "("
            });
            cursor++;
            continue;
        }
        if (char == ')') { //匹配右括号
            tokens.push({
                type: "paren",
                value: ")"
            });
            cursor++;
            continue;
        }
        if (/\d/.test(char)) { //匹配数字
            let value = '';
            while (/\d/.test(char)) {
                value = value + char;
                char = input[++cursor];
            }
            tokens.push({
                type: "number",
                value: value
            })
            continue;
        }
        if (/[a-zA-Z]/.test(char)) { //匹配变量名
            let value = '';
            while (/[a-zA-Z]/.test(char)) {
                value = value + char;
                char = input[++cursor];
            }
            tokens.push({
                type: "name",
                value: value
            })
            continue;
        }
        if (char == "\"") { //匹配字符串
            let value = '';
            let char = input[++cursor];
            while (char != "\"") {
                value = value + char;
                char=input[++cursor];
            }
            cursor++;
            tokens.push({
                type: "string",
                value: value
            });
            continue;
        }
        throw new Error(`unknow character:${char}`)
    }
    return tokens;
}

```
### 语法分析
词法分析的结果只是找出了所有的`token`，但是并没有表述出token之间的关系。语法分析的目的就是创建抽象的语法树`AST`，之所以叫树，是因为有了层级和节点的关系，能够对源代码的结构有更好的表示。

``` json
input: tokens
output:
{
    "type": "program",
    "body": [
        {
            "type": "CallExpression",
            "name": "add",
            "parameters": [
                {
                    "type": "NumberLiteral",
                    "value": "3"
                },
                {
                    "type": "CallExpression",
                    "name": "subtract",
                    "parameters": [
                        {
                            "type": "NumberLiteral",
                            "value": "4"
                        },
                        {
                            "type": "StringLiteral",
                            "value": "2"
                        }
                    ]
                }
            ]
        }
    ]
}
```

```javascript
//代码
function parser(tokens) {
    let cursor = 0;

    function walker() {
        let token = tokens[cursor];
        if (token.type == "number") {
            cursor++;
            return {
                type: "NumberLiteral",
                value: token.value
            };
        }
        if (token.type == "string") {
            cursor++;
            return {
                type: "StringLiteral",
                value: token.value
            };
        }
        if (token.type == "paren" && token.value == "(") {
            token = tokens[++cursor];
            let node = {
                type: "CallExpression",
                name: token.value,
                parameters: []
            };
            token = tokens[++cursor]

            while (token.type != "paren" || (token.type == "paren" && token.value != ")")) {
                node.parameters.push(walker());
                token = tokens[cursor];
            }
            cursor++;
            return node;
        }
        throw new Error("unknow token");
    }

    let ast = {
        type: "program",
        body: []
    };

    while (cursor < tokens.length) {
        ast.body.push(walker());
    }
    return ast;
}

```
## AST转换
转换的作用就是对AST进行修改，使其可以表示同种语言(Babel就是把js转化为js)或者其他语言。转换的过程需要对所有的节点进行处理，这个过程按照深度优先的规则。

```json
input: ast
output:
{
    "type": "program",
    "body": [
        {
            "type": "ExpressionStatement",
            "expression": {
                "type": "CallExpression",
                "callee": {
                    "type": "Identifier",
                    "name": "add"
                },
                "arguments": [
                    {
                        "type": "NumberLiteral",
                        "value": "3"
                    },
                    {
                        "type": "CallExpression",
                        "callee": {
                            "type": "Identifier",
                            "name": "subtract"
                        },
                        "arguments": [
                            {
                                "type": "NumberLiteral",
                                "value": "4"
                            },
                            {
                                "type": "StringLiteral",
                                "value": "2"
                            }
                        ]
                    }
                ]
            }
        }
    ]
}
```

```javascript
//遍历所有的节点，vistor用来处理不同类型的节点
function traverser(ast, vistor) {
    function traverserNodeArray(array, parent) {
        array.forEach(element => {
            traverserNode(element, parent);
        });
    }

    function traverserNode(node, parent) {
        let methods = vistor[node.type];
        if (methods && methods.enter) {
            methods.enter(node, parent);
        }
        switch (node.type) {
            case "program":
                traverserNodeArray(node.body, node);
                break;
            case "NumberLiteral":
            case "StringLiteral":
                break;
            case "CallExpression":
                traverserNodeArray(node.parameters, node);
                break;
            default:
                throw TypeError(node.type);
        }
        if (methods && methods.exit) {
            methods.exit(node, parent)
        }
    }
    traverserNode(ast, null);
}

function transformer(ast) {
    let newAst = {
        type: "program",
        body: []
    };
    ast._context = newAst.body;
    traverser(ast, {
        NumberLiteral: {
            enter: (node, parent) => {
                parent._context.push({
                    type: 'NumberLiteral',
                    value: node.value
                });
            }
        },
        StringLiteral: {
            enter: (node, parent) => {
                parent._context.push({
                    type: "StringLiteral",
                    value: node.value
                });
            }
        },
        CallExpression: {
            enter: (node, parent) => {
                let expression = {
                    type: "CallExpression",
                    callee: {
                        type: "Identifier",
                        name: node.name
                    },
                    arguments: []
                }
                node._context = expression.arguments;
                if (parent.type != "CallExpression") {
                    expression = {
                        type: 'ExpressionStatement',
                        expression: expression,
                    };
                }
                parent._context.push(expression);
            }
        }
    });
    return newAst;
}
```

## 代码生成
最后一步就是代码生成，这一步会递归调用自身的每一个字节点，每个节点都会生成一个字符串，然后进行拼接。
```javascript
input: newAst
output: add(3, subtract(4, "2"));
```
```javascript
//代码
function codeGenerator(node) {
    switch (node.type) {
        case "program":
            return node.body.map(codeGenerator).join('\n');
        case "CallExpression":
            return codeGenerator(node.callee) + '(' + node.arguments.map(codeGenerator).join(', ') + ')';
        case "Identifier":
            return node.name;
        case "NumberLiteral":
            return node.value;
        case "StringLiteral":
            return `"${node.value}"`;
        case "ExpressionStatement":
            return codeGenerator(node.expression) + ";";
    }
}
```
参考:[the-super-tiny-compiler](https://github.com/jamiebuilds/the-super-tiny-compiler])