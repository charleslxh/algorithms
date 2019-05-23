
## 场景描述

堆（HEAP）是一种常见的 **完全二叉树** 结构，氛围大顶堆、小顶堆。

## 实现原理

堆的规则：

大顶堆：任何节点的值都比子节点的值大。
小顶堆：任何节点的值都比子节点的值小。

**完全二叉树** 完全可以使用数组来表示，通过数组下标我们可以知道其父节点、左儿子、右儿子的位置。

如何将一个值插入在堆中？

我们首先将将要插入的值，插入到树最后一个节点，然后与其父节点的值作比较，如果该堆是最大堆，根据上面提到的大顶堆规则，判断父节点的值是否大于当前节点的值，如果大于，则将本节点的值与父节点的值交换位置，以此类推，直到树的根节点位置，小顶堆也是同样的逻辑。

## 实现代码

### 大顶堆实现

1. PHP

  待实现

2. JavaScript

```javascript
const MAX_SIZE = 1000;
const MAX_VALUE = Infinity;
const MIN_VALUE = -Infinity;

function Heap(values) {
  this.heap = new Array(MAX_SIZE);
  this.heap[0] = MAX_VALUE;
  this.size = 0;

  for (var i = 0; i < values.length; i++) {
    this.insert(values[i]);
  }
}

Heap.prototype.insert = function (value) {
  let i = ++this.size;

  while (this.heap[parseInt(i/2)] < value) {
    this.heap[i] = this.heap[parseInt(i/2)];
    i = parseInt(i/2);
  }

  this.heap[i] = value;
}

new Heap([980, 128, 4, 198, 456, 23, 56]) // [-Infinity, 4, 198, 23, 980, 456, 128, 56, empty × 992]
```

### 小顶堆实现

1. PHP

  待实现

2. JavaScript

```javascript
const MAX_SIZE = 1000;
const MAX_VALUE = Infinity;
const MIN_VALUE = -Infinity;

function Heap(values) {
  this.heap = new Array(MAX_SIZE);
  this.heap[0] = MIN_VALUE;
  this.size = 0;

  for (var i = 0; i < values.length; i++) {
    this.insert(values[i]);
  }
}

Heap.prototype.insert = function (value) {
  let i = ++this.size;

  while (this.heap[parseInt(i/2)] > value) {
    this.heap[i] = this.heap[parseInt(i/2)];
    i = parseInt(i/2);
  }

  this.heap[i] = value;
}

new Heap([980, 128, 4, 198, 456, 23, 56]) // [-Infinity, 4, 198, 23, 980, 456, 128, 56, empty × 992]
```
