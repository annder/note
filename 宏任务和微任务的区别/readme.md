# 宏任务和微任务

要理解宏任务和微任务，首先要理解什么是进程，以及什么是线程。

## 进程（process）和线程（thread）的区别

### 官方定义

#### 进程

是系统进行资源分配和调度的一个**独立单位**。

#### 线程

线程是一个实体，是 CUP 调度和分派的基本单位，它比进程更小，且**可独立运行的基本单位**。

线程**无法获取系统资源**，**只能拥有运行中的资源**，它可以和**进程以及线程共享同一资源**。

简而言之，你可以把进程比作为一个工厂，而线程就是工厂上的流水线，负责维持工厂的生命力，以及产出产品。

### 优缺点

#### 进程

- 进程有独立的地址空间，在一个进程崩溃之后，有进程守护。

- 进程切换时，消耗的资源比线程大。

- 进程可以平台

#### 线程

- 可以并发

- 开销小

- 多个线程可以共享内存

- 无法独立运行

- 线程可锁

---

## 微任务和宏任务

`JavaScript`是单线程的脚本语言，也就是说，一个线程数崩溃掉了，下一步就不会执行了。

所以`JavaScript`有了异步的概念，不用等待其他的代码运行完毕之后，再运行下一步代码。

而是检查是否完毕，完毕以后即可使用数据。

但是`JavaScript`是无法知道，哪一些代码是同步代码，那些是异步代码。

于是有了宏任务和微任务的区别。

考虑下面的代码：

```js
setTimeout(() => console.log(4));

new Promise(resolve => {
  resolve();
  console.log(1);
}).then(() => {
  console.log(3);
});

console.log(2);
```

这段代码的执行顺序是：1， 2，3，4

由此可见，`setTimeout`是最后一个执行，而`Promise`内的函数是第一个执行。

其中`setTimeout`是宏任务，`Promise.then`是微任务。

显而易见的是，当所有的微任务执行完毕之后，再来执行宏任务。

---

宏任务常见的`API`：

- IO 操作集合
- setTimeout
- setInterval
- setImmediate （node 端）
- requestAnimationFrame

微任务常见的`API`：

- Promise.then，catch finally
- procress.nextTick （node 端）
- MutationObserver

既然有了微任务和宏任务的区别，我们来区别一下：

```js
// 宏任务
setTimeout(() => console.log(4));

new Promise(resolve => {
  // 正常运行
  resolve();
  console.log(1);
}).then(() => {
  // 微任务
  console.log(3);
});

console.log(2);
```

### 浏览器中的宏任务和微任务

考虑下面的代码：

```js
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>IOTask</title>
</head>

<body>
    <div class="outer">
        <div class="inner"></div>
    </div>
    <style>
        .outer {
            padding: 20px;
            background: #616161;
        }

        .inner {
            width: 100px;
            height: 100px;
            background: #757575;
        }
    </style>
</body>

</html>
```

```js
const $inner = document.querySelector('.inner');
const $outer = document.querySelector('.outer');

function handler() {
  console.log('click');

  Promise.resolve().then(() => console.log('promise')); // 微任务

  setTimeout(() => console.log('timeout')); // 宏任务

  requestAnimationFrame(() => console.log('animationFrame')); // 微任务

  $outer.setAttribute('data-random', Math.random()); // DOM属性修改，触发微任务
}

$inner.addEventListener('click', handler);
$outer.addEventListener('click', handler);
```

上面是一个关于微任务和宏任务的操作。



我们还经常遇到 setTimeout(fn,0)这样的代码，0 秒后执行又是什么意思呢？是不是可以立即执行呢？
答案是不会的，setTimeout(fn,0)的含义是，指定某个任务在主线程最早可得的空闲时间执行，意思就是不用再等多少秒了，只要主线程执行栈内的同步任务全部执行完成，栈为空就马上执行。

## Ref:

[腾讯面试题 04.进程和线程的区别？](https://blog.csdn.net/mxsgoden/article/details/8821936)

[微任务、宏任务与 Event-Loop](https://www.cnblogs.com/jiasm/p/9482443.html)
