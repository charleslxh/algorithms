## 场景描述

实现 [斐波那契数列](https://baike.baidu.com/item/%E6%96%90%E6%B3%A2%E9%82%A3%E5%A5%91%E6%95%B0%E5%88%97/99145?fr=aladdin)。

## 实现原理

![](https://gss0.baidu.com/9fo3dSag_xI4khGko9WTAnF6hhy/zhidao/wh%3D600%2C800/sign=b574c0fcb199a9013b6053302da52643/80cb39dbb6fd52667a5220fca218972bd50736d8.jpg
)

## 实现代码

1. PHP

待实现...

2. JavaScript

```javascript
// 递归
function fabonacci1(n) {
  if (n <= 2) return 1;
  return fabonaci1(n - 1) + fabonaci1(n - 2);
}

// 递归，避免重复计算
var mem = {};
function fabonacci2(n) {
  if (n <= 2) return 1;
  if (mem[n]) return mem[n];
  mem[n] = fabonaci2(n - 1) + fabonaci2(n - 2);
  return mem[n];
}

// 迭代
function fabonaci3(n) {
  if (n <= 1) return n;
  
  var now = 1, pre1 = 1, pre2 = 0, start = 2;
  
  while(start++ <= n) {
    now = pre1 + pre2;
    pre2 = pre1;
    pre1 = now;
  }
  
  return now;
}
```
