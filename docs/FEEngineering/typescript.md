# 再学 Typescript

typescript = JavaScript with type checking

## tsc —init 进行 TS 配置

## built in types

any 我不在乎它的类型

unknown 我不知道它的类型

enum {Small = 1, Big = 2}

tuple

元组是 TypeScript 中一种特殊的数组类型，它**固定长度和每个位置的类型**。

元组在需要固定结构但又不想创建完整对象时非常有用，特别适合函数返回多个值、配置参数等场景。

readonly

literal types

## 函数重载

名称一样，参数和返回值不同的函数，达到编写一个函数，实现多种功能的目的

## 类

public

private 当前类 建议下划线开头

protected 当前类和子类中访问

static 通过类名访问

readonly

存取器 get set

abstract 抽象类：作为子类基类使用，规范格式

泛型类

# 范型

T type 类型占位符

K key

V value

E element

## 联合类型 union types |

## 交叉类型 intersection types &

any 类型与任何类型交叉都是 any

## never

## 索引签名

类型推导方法

在 TypeScript 中，**索引签名（Index Signatures）** 允许你定义一个对象，它的**属性名称是动态的**，但**值的类型是固定的**。

## Record

`Record<K, T>` 是 TypeScript 提供的一个非常实用的内置工具类型。它的作用是：**构造一个对象类型**，指定它的**键（Keys）**为 `K` 类型，**值（Values）**为 `T` 类型。

本质上，它是**索引签名**的一种简洁、高级的封装方式。

## import type

## **为什么需要  `declare`**

### **1.  环境声明**

`declare`  用于**环境声明**，它告诉 TypeScript：

- 这个接口/类型存在于某个地方
- 但不在这里实现具体的代码
- 通常用于描述外部库、全局变量或.d.ts 文件

## Readonly

TypeScript 的  `Readonly<T>`  是**属性只读**，不是"值和属性都不能动"。
