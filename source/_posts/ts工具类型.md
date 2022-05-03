---
title: ts工具类型
category:
  - 技术笔记
date: 2022-05-03 09:05:01
tag:
index_img: https://img.shuaxindiary.cn/WechatIMG117.jpeg
banner_img: https://img.shuaxindiary.cn/WechatIMG117.jpeg
---

> [banner和cover图片出处](https://twitter.com/ethfinch/media)

# 前言

记录最近重构公司一个项目的时候，对ts工具类型的一些学习，高效的使用好ts的类型推导

# 正文

- 关于什么是工具类型，就是通过一些操作对静态描述的类型结构进行增删改查、或者挑选在聚合组成一个新的类型声明的操作；社区也有很多类似的工具类型的库，这些工具类型就相当于类型声明的 *lodash*。
- 一些后文会提到的基本概念

### 关于any、unknown、never区别

#### 相同

三者都是ts的类型声明

#### 不同

- any：ts中的顶层类型，包含js中的所有值（任何类型的值），但是用他声明的变量，会失去编译器带来的类型推导、提示等，会使的ts的本身意义失效
- unknown：和any类似，但是不同的是，编译器不允许使用他声明的变量调用任何函数，除非在调用之前做类型判断，不然会报错
- never：ts底层类型，和前两者完全相反，表示不包含任何值，可以用在一些没有返回值的函数等

### 关于type 和 interface区别

#### 相同

均可以用来定义 **接口** 结构，且都可以继承和互相继承，只是写法不太一样

#### 不同

- *type* 可以声明 **基本类型别名**，*interface* 不可以
- *type* 可以声明 **联合类型**， *interface* 不可以
- *type* 可以声明 **元祖类型**，*interface* 不可以
- *type* 可以通过 **typeof** 声明类型，*interface* 不可以
- *interface* 同名会自动合并，*type* 会报错


### 关于泛型

**泛型** 是一切工具类型的基础，所有的工具类型基本都是基于泛型来实现。什么是泛型呢，可以在定义类型声明的时候，动态的传递一个类型进去，然后类型内部会接收这个传入的类型来声明内部的一些类型。理解成使用函数的使用传入的参数一样。

```ts
// 声明泛型
export interface People<T = any> {
  name: string;
  age: number;
  sex: SEX;
  // 使用传入的类型
  customInfo?: T;
  bodyInfo: {
    height: number;
    weight: number;
  };
}

// 使用泛型
const demo: Partial<
  People2<{
    address?: string;
  }>
> = {};

```

### ts内置工具类型

ts，本身内置了一些基本的工具类型共日常使用，然后也可以结合这些自定义自己实际场景需要的一些工具类型。

#### Pick

选中指定类型中的一个或多个类型构建一个新类型

```ts
enum SEX {
  MAN = "MAN",
  WOMAN = "WOMAN",
}

export interface People {
  name: string;
  age: number;
  sex: SEX;
  bodyInfo: {
    height: number;
    weight: number;
  };
}

type BaseInfo = Pick<People, "name" | "age" | "sex">;
```

![](https://img.shuaxindiary.cn/1651544591294.png)

#### 获取指定类型的某个类型

类似js字面量对象一样的访问语法，和 **pick** 的区别是这样操作得到的是单个指定的类型，**pick** 会把拿到的类型放到一个 **interface** 中

```ts
enum SEX {
  MAN = "MAN",
  WOMAN = "WOMAN",
}
export interface People {
  name: string;
  age: number;
  sex: SEX;
  bodyInfo: {
    height: number;
    weight: number;
  };
}

type BodyInfo = People["bodyInfo"];
```
![](https://img.shuaxindiary.cn/1651544558466.png)

#### Omit

和pick用法一样，作用是忽略指定的单个或多个类型，然后把剩余的类型构建为一个新的类型

```ts
enum SEX {
  MAN = "MAN",
  WOMAN = "WOMAN",
}

export interface People {
  name: string;
  age: number;
  sex: SEX;
  bodyInfo: {
    height: number;
    weight: number;
  };
}

type Dog = Omit<People, "bodyInfo" | "sex">;

const dog: Dog = {
  name: "dog",
  age: 100,
};
```
![](https://img.shuaxindiary.cn/1651544992920.png)

#### Partial

把所有类型转为可选

```ts
enum SEX {
  MAN = "MAN",
  WOMAN = "WOMAN",
}

export interface People {
  name: string;
  age: number;
  sex: SEX;
  bodyInfo: {
    height: number;
    weight: number;
  };
}

export interface Man extends Partial<People> {
  sex: SEX.MAN;
}
```

![](https://img.shuaxindiary.cn/1651545569044.png)

#### Required

和Partial用法一致，作用相反，把所有类型转为必选

```ts
export interface People {
  name: string;
  age: number;
  sex: SEX;
  bodyInfo: {
    height: number;
    weight: number;
  };
}

type _People = Partial<People>;

type Req_People = Required<_People>;
```
![](https://img.shuaxindiary.cn/1651545880299.png)

#### Record

声明字面量对象类型

```ts

const obj: Record<string, any> = {};

```

#### Readonly

设置所有类型为只读

```ts
enum SEX {
  MAN = "MAN",
  WOMAN = "WOMAN",
}
export interface People {
  name: string;
  age: number;
  sex: SEX;
  bodyInfo: {
    height: number;
    weight: number;
  };
}

type Man = Readonly<People>;
```
![](https://img.shuaxindiary.cn/1651546170656.png)

#### Exclude

当第一个类型不是第二个类型的子类型时，直接返回第一个类型或者never

#### Extract

和exclude相反

### 开源ts工具类型合集

除过自己写，社区有一些已经类似lodash一样，封装了常用的一些工具类型：

- [type-fest](https://github.com/sindresorhus/type-fest)
- [utility-types](https://github.com/piotrwitek/utility-types)

