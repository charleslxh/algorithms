## 场景描述

如何判断给定的两个序列是否是同一棵树，比如：`[3,1,4,2]` 与 `[3,4,1,2]` 属于同一棵树，但是 `[3,2,1,4]` 不是同一棵树。

## 实现原理

目前列出了以下几种方案：

1. 对每个序列建一个树，分别对比两颗树的所有节点是否一致，只要有一个节点不一致，则表示不是同一颗树。
2. 对标准序列建一棵基本树 BST，遍历第二个序列的每一个元素，如果在查找该元素的过程中，碰到 BST 中某个节点没有被遍历过，则表示不是一棵树。

## 实现代码

### 方案一

1. PHP

待实现...

2. JavaScript

```javascript
function Node(v) {
    this.left = this.right = null;
    this.val = v;
}

function insert(T, N) {
  if (T === null) return N;

  if (T.val < N.val) {
    T.right = insert(T.right, N);
  } else if (T.val > N.val) {
    T.left = insert(T.left, N)
  }

  return T;
}

function Tree(elements) {
  if (elements.length <= 0) return null;

  var T = new Node(elements[0])

  var i = 1;
  while(i < elements.length) {
    T = insert(T, new Node(elements[i]));
    i++;
  }

  return T;
}

var equals = function (t1, t2) {
  if (t1 === null && t2 === null) return true;
  if ((t1 === null && t2 !== null) || (t1 !== null && t2 === null)) return false;
  if (t1.val !== t2.val) return false;

  if (equals(t1.left, t2.left) && equals(t1.right, t2.right)) {
    return true;
  } else {
    return false;
  }
}

var BST = new Tree([3,1,2,4]);

var T1 = new Tree([3,1,4,2]);
var T2 = new Tree([3,2,1,4]);
var T3 = new Tree([3,2,4,1]);
var T4 = new Tree([3,4,1,2]);
var T5 = new Tree([3,4,2,1]);

console.log(equals(BST, T1)); // true
console.log(equals(BST, T2)); // false
console.log(equals(BST, T3)); // false
console.log(equals(BST, T4)); // true
console.log(equals(BST, T5)); // false
```

### 方案二

```js
function Node(v) {
    this.left = this.right = null;
    this.val = v;
    this.isVisit = false;
}

function insert(T, N) {
  if (T === null) return N;

  if (T.val < N.val) {
    T.right = insert(T.right, N);
  } else if (T.val > N.val) {
    T.left = insert(T.left, N)
  }

  return T;
}

function Tree(elements) {
  if (elements.length <= 0) return null;

  var T = new Node(elements[0])

  var i = 1;
  while(i < elements.length) {
    T = insert(T, new Node(elements[i]));
    i++;
  }

  this.T = T;
}

Tree.prototype.checkElement = function (T, element) {
  if (T.isVisit) {
    if (T.val < element) {
      return this.checkElement(T.right, element);
    } else if (T.val > element) {
      return this.checkElement(T.left, element);
    }
  } else {
    if (T.val === element) {
      T.isVisit = true;

      return true;
    } else {
      return false;
    }
  }
}

Tree.prototype.reset = function (T) {
  T.isVisit = false;

  if (T.left) this.reset(T.left);
  if (T.right) this.reset(T.right);

  return T;
}

Tree.prototype.equals = function (elements) {
  T = this.reset(this.T);

  for (var i = 0; i < elements.length; i++) {
    var element = elements[i];

    if (!this.checkElement(T, element)) {
      return false;
    }
  }

  return true;
}

var BST = new Tree([3,1,2,4]);

console.log(BST.equals([3,1,4,2])); // true
console.log(BST.equals([3,2,1,4])); // false
console.log(BST.equals([3,2,4,1])); // false
console.log(BST.equals([3,4,1,2])); // true
console.log(BST.equals([3,4,2,1])); // false
```
