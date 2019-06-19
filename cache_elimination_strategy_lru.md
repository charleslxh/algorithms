## 场景描述

缓存策略 LRU 的实现。

LRU（Least recently used，最近最少使用）算法根据数据的历史访问记录来进行淘汰数据，其核心思想是“如果数据最近被访问过，那么将来被访问的几率也更高”。

## 实现原理

方案一：使用 `HashTable` 和 `双向链表` 来存储，已达到最有效的时间复杂度，插入 o(1)，查找 o(1)，删除 o(1)，更新 o(1)。

整体的设计思路是，可以使用 HashMap 存储 key，这样可以做到 save 和 get key的时间都是 O(1)，而 HashMap 的 Value 指向双向链表实现的 LRU 的 Node 节点，如图所示。

![LRU](https://pic4.zhimg.com/80/v2-09f037608b1b2de70b52d1312ef3b307_hd.jpg)

LRU 存储是基于双向链表实现的，下面的图演示了它的原理。其中 head 代表双向链表的表头，tail 代表尾部。首先预先设置 LRU 的容量，如果存储满了，可以通过 O(1) 的时间淘汰掉双向链表的尾部，每次新增和访问数据，都可以通过 O(1)的效率把新的节点增加到对头，或者把已经存在的节点移动到队头。

## 实现代码

1. PHP

待实现...

2. JavaScript

```javascript
const assert = require('assert');

const HASH_TABLE_SIZE_DEFAULT = 10;

function Node(key, value) {
  this.key = key;
  this.value = value;
  this.next = null;
}

function HashTable(size) {
  this.size = size || HASH_TABLE_SIZE_DEFAULT;
  this.table = new Array(this.size);
}

HashTable.prototype.getHashKey = function (key) {
  var value = 0;
  var index = 0;

  while (index < key.length) {
    value += key.charCodeAt(index++);
  }

  return value % this.size
}

HashTable.prototype.get = function(key) {
  var pos = this.getHashKey(key);

  var node = this.table[pos];

  while (node) {
    if (node.key === key) {
      return node.value;
    }

    node = node.next;
  }

  return null;
};

HashTable.prototype.set = function(key, value) {
  var pos = this.getHashKey(key);
  var node = this.table[pos];

  while (node) {
    if (node.key === key) {
      node.value = value;
      return;
    }

    node = node.next;
  }

  var newNode = new Node(key, value);
  newNode.next = this.table[pos];
  this.table[pos] = newNode;
}

HashTable.prototype.del = function(key, value) {
  var pos = this.getHashKey(key);
  var node = this.table[pos];

  if (node.key === key) {
      this.table[pos] = node.next;
      return node;
    }

  while (node.next) {
    if (node.next.key === key) {
      node.next = node.next.next;
      return node.next;
    }

    node = node.next;
  }

  return null;
}

HashTable.prototype.toString = function(key, value) {
  var str = '';

  for (var i = 0; i < this.table.length; i++) {
    var node = this.table[i];

    while(node) {
      str += node.key;
      node = node.next;
    }
  }

  return str;
}

var h = new HashTable(26);
var keys = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';

for (var i = 0; i < keys.length; i++) {
  h.set(keys[i], keys.charCodeAt(i))
}

// 测试 Case

assert.strictEqual(h.toString(), 'NhOiPjQkRlSmTnUoVpWqXrYsZtAuBvCwDxEyFzGaHbIcJdKeLfMg');
for (var i = 0; i < keys.length; i++) {
  assert.strictEqual(h.get(keys[i]), keys[i].charCodeAt());
}

h.del('h');
assert.strictEqual(h.toString(), 'NOiPjQkRlSmTnUoVpWqXrYsZtAuBvCwDxEyFzGaHbIcJdKeLfMg');

h.del('O');
assert.strictEqual(h.toString(), 'NiPjQkRlSmTnUoVpWqXrYsZtAuBvCwDxEyFzGaHbIcJdKeLfMg');

h.del('g');
assert.strictEqual(h.toString(), 'NiPjQkRlSmTnUoVpWqXrYsZtAuBvCwDxEyFzGaHbIcJdKeLfM');
```
