---
layout: post
title:  "深入理解 Generator 函数"
date:   2019-08-30
categories: 前端
comments: true
---

```javascript
// 基本用法
function* f() {
    yield 'hello';
    return 'world';
}
let g = f();
console.log(g.next());
console.log(g.next());
console.log(g.next());

// yield 用在表达式中，需要用括号（）
function* f() {
    console.log('Hello' + yield 123);
}

// 对象的 Symbol.iterator 对应遍历器对象生成函数
function* f() {}
const g = f();
console.log(g[Symbol.iterator]() === g);

// next 参数改变上次 yield 表达式的返回值，第一次 next 传递的参数无效，因为没有上次 yield 表达式
function* f(x) {
    const y = 2 * (yield x + 1);
    const z = yield y / 3;
    return x + y + z;
}

const a = f(5);
console.log(a.next());
console.log(a.next());
console.log(a.next());

const b = f(5);
console.log(b.next(1));
console.log(b.next(12));
console.log(b.next(13));

// 遍历的范围，只包含 done: false 的结果
function* f() {
    yield 1;
    yield 2;
    return 3;
}

// yield* 表达式，等价于 for of 遍历有 Symbol.iterator 的对象，返回的值为，return 的结果
for (let v of f()) {
    console.log(v);
}

function* f() {
    yield 'd';
    return 'e';
}

function* gen() {
    yield* ['a', 'b', 'c'];
    const v = yield* f();
    console.log('v:', v);
    yield* 'ok';
}

for (let v of gen()) {
    console.log(v);
}

// 遍历器对象的 throw 方法，先由 generator 函数内部捕获，没有内部捕获，才会由外部捕获
const g = function*() {
    try {
        yield;
    } catch (e) {
        console.log('内部捕获', e);
    }
};

const i = g();
i.next();
try {
    i.throw('a');
    i.throw('b');
} catch (e) {
    console.log('外部捕获', e);
}

// 捕获 generator 内部异常后，还会执行下一 next 方法
const f = function* f() {
    try {
        yield console.log('a');
    } catch (e) {}
    yield console.log('b');
    yield console.log('c');
};

const g = f();
g.next();
g.throw();
g.next();

// return方法会推迟到 finally 代码块执行完再执行
function* f() {
    yield 1;
    try {
        yield 2;
        yield 3;
    } finally {
        yield 4;
        yield 5;
    }
    yield 6;
}

const g = f();
console.log(g.next());
console.log(g.next());
console.log(g.return(7));
console.log(g.next());
console.log(g.next());

// 利用 Generator 函数实现 lodash 中的 _.flattenDeep 方法

const tree = ['a', ['b', 'c'], ['d', 'e', ['f', 'g']]];

console.log(flattenDeep(tree));

function flattenDeep(tree) {
    return [...iterTree(tree)];
}

function* iterTree(tree) {
    if (Array.isArray(tree)) {
        for (let i = 0; i < tree.length; i++) {
            yield* iterTree(tree[i]);
        }
    } else {
        yield tree;
    }
}

// 利用 Generator 以及 对象的 Symbol.iterator，实现类似 Object.entries() 的方法，请完成 objectEntries 函数

let jane = { first: 'Jane', last: 'Doe' };

for (let [key, value] of objectEntries(jane)) {
    console.log(`${key}: ${value}`);
}

function* objectEntries(obj) {
    let keys = Object.keys(obj);
    for (let key of keys) {
        yield [key, obj[key]];
    }
}

// 利用 Generator 函数和for...of循环，实现斐波那契数列的例子斐波那契数列

function* fibonacci(num) {
    let [prev, curr] = [0, 1];
    for (let i; i < num; i++) {
        yield curr;
        [prev, curr] = [curr, prev + curr];
    }
}

for (let n of fibonacci(100)) {
    console.log(n);
}
```