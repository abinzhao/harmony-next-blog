> 随着鸿蒙操作系统的发展，ArkTS 作为其官方推荐的编程语言，逐渐受到大家的关注。，ArkTS 这个魔改的 TS 语言，对于前端开发者来说无疑是福音，因为这意味着只需要极小的成本或者完全没有学习成本，可以直接无缝开发鸿蒙 Next 应用。
>
> 本文将详细介绍如何将 TypeScript 代码适配到 ArkTS，并提供详细的适配规则，帮助大家更加顺利完成从前端到鸿蒙的过渡迁移。

## 一、ArkTS 简介

ArkTS 是鸿蒙操作系统推出的一种静态类型编程语言，基于 TypeScript 并进行了诸多优化和改进。ArkTS 不仅保留了 TypeScript 的大部分语法特性，还引入了一系列新的特性和约束，以提升性能和开发效率。

## 二、适配规则

### 2.1 强制使用静态类型

ArkTS 强制要求所有类型在编译时已知，禁止使用 any 和 unknown 类型。这有助于提升代码的可读性和可维护性，同时减少运行时错误。

```ts
// TypeScript
let res: any = some_api_function('hello', 'world');

// ArkTS
class CallResult {
  public succeeded(): boolean { ... }
  public errorMessage(): string { ... }
}

let res: CallResult = some_api_function('hello', 'world');
```

### 2.2 禁止在运行时变更对象布局

ArkTS 要求对象的布局在编译时确定，并在运行时不可更改。这包括禁止添加、删除属性和方法，以及禁止将任意类型的值赋值给对象属性。

在 ArkTS 中，严格类型检查不是可配置项。ArkTS 强制进行部分严格类型检查，并通过规范禁止使用 any 类型，禁止在代码中使用@ts-ignore。

```ts
// TypeScript
class Point {
  x: number = 0;
  y: number = 0;
}

let p1 = new Point();
delete p1.x; // 编译时错误

// ArkTS
class Point {
  x: number = 0;
  y: number = 0;
}

let p1 = new Point();
// delete p1.x; // 编译时错误
```

### 2.3 限制运算符的语义

ArkTS 限制了一元运算符+、-和~仅适用于数值类型，禁止隐式将字符串转换为数值。

```ts
// TypeScript
let a = +5; // 合法
let b = +"5"; // 编译时错误

// ArkTS
let a = +5; // 合法
// let b = +'5';    // 编译时错误
```

### 2.4 不支持 structural typing

ArkTS 不支持 structural typing，要求类型必须显式声明，禁止通过接口实现 structural typing。

```ts
// TypeScript
interface I1 {
  f(): string;
}
interface I2 {
  f(): string;
}

class X implements I1, I2 {
  f() {
    return "foo";
  }
}

// ArkTS
interface I1 {
  f(): string;
}
interface I2 {
  f(): string;
}

class X implements I1, I2 {
  f() {
    return "foo";
  }
}
```

structural typing 是否有助于生成清晰、易理解的代码，关于这一点并没有定论。那为什么 ArkTS 不支持 structural typing 呢？

因为对 structural typing 的支持是一个重大的特性，需要在语言规范、编译器和运行时进行大量的考虑和仔细的实现。另外，由于 ArkTS 使用静态类型，运行时为了支持这个特性需要额外的性能开销。

### 2.5 其他重要规则

- 对象的属性名必须是合法的标识符：ArkTS 不允许对象属性名为数字或字符串。
- 不支持 Symbol() API：ArkTS 不支持动态生成唯一属性名称。
- 不支持以#开头的私有字段：改用 private 关键字。
- 类型、命名空间的命名必须唯一：避免名称冲突。
- 使用 let 而非 var：提升代码的可读性和安全性。
- 不支持 any 和 unknown 类型：显式标注具体类型。
- 不支持类表达式：必须显式声明一个类。
- 类不允许 implements：只有接口可以被 implements。
- 不支持修改对象的方法：禁止动态修改对象方法。
- 类型转换仅支持 as T 语法：禁止使用<type>语法。
- 不支持 JSX 表达式：ArkTS 不支持 JSX。
- 不支持 new.target：ArkTS 没有原型的概念。
- 不支持确定赋值断言：改为在声明变量的同时为变量赋值。
- 不支持在原型上赋值：ArkTS 没有原型的概念。
- 不支持 globalThis：ArkTS 不支持全局作用域。
- 不支持一些 utility 类型：仅支持 Partial、Required、Readonly 和 Record。
- 不支持对函数声明属性：禁止动态改变函数对象布局。
- 不支持 Function.apply 和 Function.call：禁止使用标准库函数。
- 不支持 Function.bind：禁止使用标准库函数。
- 不支持 as const 断言：ArkTS 不支持字面量类型。
- 不支持导入断言：ArkTS 不支持导入断言。
- 限制使用标准库：禁止使用 TypeScript 或 JavaScript 标准库中的某些接口。
- 强制进行严格类型检查：包括 noImplicitReturns、strictFunctionTypes 等。
- 不允许通过注释关闭类型检查：类型检查是强制性的。
- 允许.ets 文件 import.ets/.ts/.js 文件源码，不允许.ts/.js 文件 import.ets 文件源码：确保类型安全。
- 类不能被用作对象：类声明的是一个新的类型，不是一个值。
- 不支持在 import 语句前使用其他语句：所有 import 语句需要放在所有其他语句之前。
- 限制使用 ESObject 类型：防止动态对象在静态代码中的滥用。

## 三、总结

ArkTS 作为鸿蒙 NEXT 的重要组成部分，通过一系列严格的适配规则，确保了代码的性能和安全性。尽管这些规则可能会带来一定的迁移和学习成本，但长远来看，它们将有助于提升开发效率和代码质量。

希望本文能为大家提供有价值的参考，助力大家顺利从前端过渡到 ArkTS 的开发环境。

通过遵循上述规则和建议，开发者可以有效地将 TypeScript 代码转换为 ArkTS，并充分利用 ArkTS 的优势，开发出更高效、更安全的鸿蒙 next 应用。
