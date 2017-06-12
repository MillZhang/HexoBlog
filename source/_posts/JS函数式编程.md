---
title: JS函数式编程
tags: JavaScript
---

### 概念

> 以函数作为主要载体的编程方式,用函数去拆解,抽象一般的表达式;

### 特点

* 语义清晰
* 可复用性好
* 可维护性好
* 作用域局限, 副作用小

<!--more-->

### 基本的函数式编程
```js
// 数组中每个单词，首字母大写


// 一般写法
const arr = ['apple', 'pen', 'apple-pen'];
for(const i in arr){
  const c = arr[i][0];
  arr[i] = c.toUpperCase() + arr[i].slice(1);
}

console.log(arr);


// 函数式写法一
function upperFirst(word) {
  return word[0].toUpperCase() + word.slice(1);
}

function wordToUpperCase(arr) {
  return arr.map(upperFirst);
}

console.log(wordToUpperCase(['apple', 'pen', 'apple-pen']));


// 函数式写法二
console.log(arr.map(word=>{
	return word[0].toUpperCase()+word.slice(1)
}));

//另一种写法
console.log(Array.prototype.map.call(arr, word => word[0].toUpperCase() + word.slice(1)));
```
>  `map()` 方法返回一个由原数组中的每个元素调用一个指定方法后的返回值组成的新数组。

### 链式优化

> `lodash`:一个具有一致接口、模块化、高性能等特性的 JavaScript 工具库;主要是为了延迟计算.

```js
//一个简单的加减法链式计算

const utils = {
    chain(a){
        this._temp = a;
        return this;
    },
    sum(a){//加
        this._temp+=a;
        return this;
    },
    sub(a){//减
        this._temp-=a;
        return this;
    },
    value(){
        const _temp = this._temp;
        this._temp = undefined;//清空临时变量
        return _temp;
    }
}
console.log(utils.chain(1).sum(2).sum(3).sub(4).value());//2
```

### 链式对比回调和Promise模式
```js
// 回调函数
import $ from 'jquery';
$.post('a/url/to/target', (rs) => {
  if(rs){
    $.post('a/url/to/another/target', (rs2) => {
      if(rs2){
        $.post('a/url/to/third/target');
      }
    });
  }
});


// Promise
import request from 'catta';  
// catta 是一个轻量级请求工具，支持 fetch,jsonp,ajax，无依赖
request('a/url/to/target')
  .then(rs => rs ? $.post('a/url/to/another/target') : Promise.reject())
  .then(rs2 => rs2 ? $.post('a/url/to/third/target') : Promise.reject());

```
随着回调函数嵌套层级和单层复杂度增加，它将会变得臃肿且难以维护，而 Promise 的链式结构，在高复杂度时，仍能纵向扩展，而且层次隔离很清晰。

### 常见的函数式编程

#### 闭包(Closure)
> 可以保留局部变量不被释放的代码块,称为闭包

闭包创造的条件
* 存在内、外两层函数
* 内层函数对外层函数的局部变量进行了引用

用途:
```js
const cache = (function(){
		//局部变量
		const store = {};
		return {
			get(key){
				return store[key];
			},
			set(key,value){
				store[key] = value;
			}
			
		}
})();

cache.set('a','lalabu');
console.log(cache.get('a'));//"lalabu"
```

#### 高阶函数
概念:
> 接受或者返回一个函数的函数称为高阶函数

常见的高阶函数`Array.map , Array.reduce , Array.filter`

Map(映射):
> 映射是对集合而言的，即把集合的每一项都做相同的变换，产生一个新的集合

```js
// 数组中每一项加一，组成一个新数组

// 一般写法
const arr = [1,2,3];
const rs = [];
for(const n of arr){
  rs.push(++n);
}
console.log(rs)


// map改写
const arr = [1,2,3];
const rs = arr.map(n => ++n);

```
上面一般写法，利用 for...of 循环的方式遍历数组会产生额外的操作，而且有改变原数组的风险

而 map 函数封装了必要的操作，使我们仅需要关心映射逻辑的函数实现即可，减少了代码量，也降低了副作用产生的风险。

#### 柯里化（Currying）

概念:
> 给定一个函数的部分参数，生成一个接受其他参数的新函数;

```js
// 获取目标文件对基础路径的相对路径


// 一般写法
const BASE = '/path/to/base';
const relativePath = path.relative(BASE, '/some/path');


// _.parical 改写
const BASE = '/path/to/base';
const relativeFromBase = _.partial(path.relative, BASE);

const relativePath = relativeFromBase('/some/path');

```
通过 _.partial ，我们得到了新的函数 relativeFromBase ，这个函数在调用时就相当于调用 path.relative ，并默认将第一个参数传入 BASE ，后续传入的参数顺序后置。

本例中，我们真正想完成的操作是每次获得相对于 BASE 的路径，而非相对于任何路径。柯里化可以使我们只关心函数的部分参数，使函数的用途更加清晰，调用更加简单。

#### 组合(Composing)
概念:
> 将多个函数的能力合并，创造一个新的函数

同样你第一次见到他可能还是在 lodash 中，compose 方法（现在叫 flow）
```js
// 数组中每个单词大写，做 Base64


// 一般写法 (其中一种)
const arr = ['pen', 'apple', 'applypen'];
const rs = [];
for(const w of arr){
  rs.push(btoa(w.toUpperCase()));
}
console.log(rs);


// _.flow 改写
const arr = ['pen', 'apple', 'applypen'];
const upperAndBase64 = _.partialRight(_.map, _.flow(_.upperCase, btoa));
console.log(upperAndBase64(arr));
```
_.flow 将转大写和转 Base64 的函数的能力合并，生成一个新的函数。方便作为参数函数或后续复用。

