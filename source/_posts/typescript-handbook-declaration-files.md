---
title: typescript 声明文件
top: false
cover: false
toc: true
mathjax: true
date: 2020-04-12 14:55:26
password:
summary:
tags: [ts, handbook]
categories:
---
## 介绍
## 库结构
## 案例
## 该干什么和不该干什么
## Deep Dive
## 模板

### global-modifying-module.d.ts

### global-plugin.d.ts

### global.d.ts

```js
// Type definitions for [~THE LIBRARY NAME~] [~OPTIONAL VERSION NUMBER~]
// Project: [~THE PROJECT NAME~]
// Definitions by: [~YOUR NAME~] <[~A URL FOR YOU~]>

/*~ 如果这个库是可以被调用的 (e.g. 可以作为myLib(3)调用),
 *~ 在这里声明它们的调用签名
 *~ 否则，删除它们
 */
declare function myLib(a: string): string;
declare function myLib(a: number): number;

/*~ 如果你想要库的名字是一个有效的类型名,
 *~ 你可以在这里声明.
 *~
 *~ 例如，它允许我们写 'var x: myLib';
 *~ 确保它是有意义的! 如果没有删掉这个声明，并添加这些类型到命名空间中
 */
interface myLib {
  name: string;
  length: number;
  extras?: string[];
}

/*~ 如果你的库有暴露到全局变量的属性,在这里放
 *~ 你还要改在这里放类型（接口和类型别名）
 */
declare namespace myLib {
  //~ 我看可以这么使用 'myLib.timeout = 50;'
  let timeout: number;

  //~ 我们可以访问 'myLib.version', 但无法改变它
  const version: string;
  //~ 我们可以创建类，通过 'let c = new myLib.Cat(42)'
  //~ 或者引用它 e.g. 'function f(c: myLib.Cat) { ... }
  class Cat {
    constructor(n: number);

    //~ 我们可以从Cat实例中读 'c.age'
    readonly age: number;

    //~ 我们可以从Cat实例中访问 'c.purr()'
    purr(): void;
  }

  //~ 我们可以声明一个变量
  //~   'var s: myLib.CatSettings = { weight: 5, name: "Maru" };'
  interface CatSettings {
    weight: number;
    name: string;
    tailLength?: number;
  }

  //~ 我们可以这么写 'const v: myLib.VetID = 42;'
  //~  或者 'const v: myLib.VetID = "bob";'
  type VetID = string | number;

  //~ 我们可以调用 'myLib.checkCat(c)' 或者 'myLib.checkCat(c, v);'
  function checkCat(c: Cat, s?: VetID);
}
```

### module-class.d.ts

### module-function.d.ts

### module-plugin.d.ts

### module.d.ts

```js
// Type definitions for [~THE LIBRARY NAME~] [~OPTIONAL VERSION NUMBER~]
// Project: [~THE PROJECT NAME~]
// Definitions by: [~YOUR NAME~] <[~A URL FOR YOU~]>

/*~ 这是模块模板文件. 你应该重命名它为 index.d.ts
 *~ 并把它放到模块同名的文件夹下.
 *~ 针对这个案例, 如果你写了一个"super-greeter"的文件, this
 *~ 这个文件应该是 'super-greeter/index.d.ts'
 */

/*~ 如果模块是UMD模块， 当模块加载器环境之外加载时，它导出一个全局的环境变量 'myLib'，在这里声明它为全局。
 *~ 否则，删除这个声明。
 */
export as namespace myLib;

/*~ 如果这个模块有函数，则声明它们为函数
 */
export function myMethod(a: string): string;
export function myOtherMethod(a: number): number;

/*~ 你可以通过导入模块声明可用类型 */
export interface someType {
    name: string;
    length: number;
    extras?: string[];
}

/*~ 在模块中你可以使用const，let,或者var来声明模块的属性 */
export const myField: number;

/*~ 如果模块的.名称中包括类型，属性，或者方法。声明它们到命名空间中
 */
export namespace subProp {
    /*~ 列如，给这个定义，可以这么使用:
     *~   import { subProp } from 'yourModule';
     *~   subProp.foo();
     *~ 或者
     *~   import * as yourMod from 'yourModule';
     *~   yourMod.subProp.foo();
     */
    export function foo(): void;
}
```
## 发布
## Consumption