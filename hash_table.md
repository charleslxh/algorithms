## 场景描述

实现 HashTable 数据结构。

## 实现原理

参考数据结构教科书。

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




```
