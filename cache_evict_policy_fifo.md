## 场景描述

缓存策略 LRU 的实现。

FIFO（First In First Out，先进先出）算法根据数据的存储的先后顺序来进行淘汰数据，其核心思想是 **如果一个数据最先进入缓存中，则应该最早淘汰掉**。

## 实现原理

方案一：使用 `HashTable` 和 `双向链表` 来存储，已达到最有效的时间复杂度，插入 O(1)，查找 O(1)，删除 O(1)，更新 O(1)。

整体的设计思路是，可以使用 HashMap 存储 key，这样可以做到 save 和 get key的时间都是 O(1)，而 HashMap 的 Value 指向双向链表实现的 LRU 的 Node 节点，如图所示。

## 实现代码

1. PHP

待实现...

2. JavaScript

```javascript
const assert = require('assert');

const DEFAULT_HASH_TABLE_SIZE = 10;
const DEFAULT_QUEUE_SIZE = 10;

function ListNode(type, key, value) {
  this.type = type;
  this.key = key;
  this.value = value;
  this.next = null;
  this.prev = null;
}

ListNode.prototype.isData = function() {
  return this.type === 'data';
}

ListNode.prototype.isHead = function() {
  return this.type === 'head';
}

ListNode.prototype.isTail = function() {
  return this.type === 'tail';
}

function HashTableNode(key, value) {
  this.key = key;
  this.value = value;
  this.next = null;
}

function LRU(size) {
  this.size = size || DEFAULT_QUEUE_SIZE;
  this.table = new HashTable(this.size);
  this.length = 0;

  this.head = new ListNode('head');
  this.tail = new ListNode('tail');
  this.head.next = this.tail;
  this.tail.prev = this.head;
}

function HashTable(size) {
  this.size = size || DEFAULT_HASH_TABLE_SIZE;
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

  var newNode = new HashTableNode(key, value);
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

LRU.prototype.get = function(key) {
  if (key === null || key === undefined) {
    return null;
  }

  var node = this.table.get(key);

  return node ? node.value : null;
};

LRU.prototype.del = function(key) {
  if (key === null || key === undefined) {
    return;
  }

  var node = this.table.get(key);

  if (node) {
    var prev = node.prev;
    var next = node.next;

    if (prev) prev.next = next;
    if (next) next.prev = prev;

    this.length--;
    delete node;
    this.table.del(key);
  }
};

LRU.prototype.isFull = function() {
  return this.length >= this.size;
}

LRU.prototype.add = function(key, value) {
  if (this.isFull()) {
    var lastNode = this.tail.prev;
    if (lastNode !== null && lastNode !== undefined) {
      this.del(lastNode.key);
    }
  }

  var newNode = new ListNode('data', key, value);
  this.table.set(key, newNode);

  var firstNode = this.head.next;

  newNode.next = firstNode;
  newNode.prev = this.head;
  this.head.next = newNode;
  firstNode.prev = newNode;

  this.length++;
}

LRU.prototype.list = function() {
  var nodes = [];
  var node = this.head.next;

  while(node && node.isData()) {
    nodes.push(node);
    node = node.next;
  }

  return nodes;
}

LRU.prototype.keys = function() {
  var keys = [];
  var node = this.head.next;

  while(node && node.isData()) {
    keys.push(node.key);
    node = node.next;
  }

  return keys;
}

LRU.prototype.values = function() {
  var values = [];
  var node = this.head.next;

  while(node && node.isData()) {
    values.push(node.value);
    node = node.next;
  }

  return values;
}

var h = new LRU(52);
var keys = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';

for (var i = 0; i < keys.length; i++) {
  h.add(keys[i], keys.charCodeAt(i))
}

// 测试 Case
assert.strictEqual(h.isFull(), true);

assert.strictEqual(h.keys().toString(), 'Z,Y,X,W,V,U,T,S,R,Q,P,O,N,M,L,K,J,I,H,G,F,E,D,C,B,A,z,y,x,w,v,u,t,s,r,q,p,o,n,m,l,k,j,i,h,g,f,e,d,c,b,a');

assert.strictEqual(h.get('a'), 97);

assert.strictEqual(h.keys().toString(), 'Z,Y,X,W,V,U,T,S,R,Q,P,O,N,M,L,K,J,I,H,G,F,E,D,C,B,A,z,y,x,w,v,u,t,s,r,q,p,o,n,m,l,k,j,i,h,g,f,e,d,c,b,a');

assert.strictEqual(h.get('b'), 98);

assert.strictEqual(h.keys().toString(), 'Z,Y,X,W,V,U,T,S,R,Q,P,O,N,M,L,K,J,I,H,G,F,E,D,C,B,A,z,y,x,w,v,u,t,s,r,q,p,o,n,m,l,k,j,i,h,g,f,e,d,c,b,a');

assert.strictEqual(h.get('Z'), 90);

assert.strictEqual(h.keys().toString(), 'Z,Y,X,W,V,U,T,S,R,Q,P,O,N,M,L,K,J,I,H,G,F,E,D,C,B,A,z,y,x,w,v,u,t,s,r,q,p,o,n,m,l,k,j,i,h,g,f,e,d,c,b,a');

h.add('test', 'just test');

assert.strictEqual(h.length, 52);
assert.strictEqual(h.keys().toString(), 'test,Z,Y,X,W,V,U,T,S,R,Q,P,O,N,M,L,K,J,I,H,G,F,E,D,C,B,A,z,y,x,w,v,u,t,s,r,q,p,o,n,m,l,k,j,i,h,g,f,e,d,c,b');

h.add('key', 'just key');

assert.strictEqual(h.length, 52);
assert.strictEqual(h.keys().toString(), 'key,test,Z,Y,X,W,V,U,T,S,R,Q,P,O,N,M,L,K,J,I,H,G,F,E,D,C,B,A,z,y,x,w,v,u,t,s,r,q,p,o,n,m,l,k,j,i,h,g,f,e,d,c');

h.del('e');

assert.strictEqual(h.keys().toString(), 'key,test,Z,Y,X,W,V,U,T,S,R,Q,P,O,N,M,L,K,J,I,H,G,F,E,D,C,B,A,z,y,x,w,v,u,t,s,r,q,p,o,n,m,l,k,j,i,h,g,f,d,c');

assert.strictEqual(h.isFull(), false);

h.del('test');

assert.strictEqual(h.keys().toString(), 'key,Z,Y,X,W,V,U,T,S,R,Q,P,O,N,M,L,K,J,I,H,G,F,E,D,C,B,A,z,y,x,w,v,u,t,s,r,q,p,o,n,m,l,k,j,i,h,g,f,d,c');

h.add('test', 'just test');

assert.strictEqual(h.keys().toString(), 'test,key,Z,Y,X,W,V,U,T,S,R,Q,P,O,N,M,L,K,J,I,H,G,F,E,D,C,B,A,z,y,x,w,v,u,t,s,r,q,p,o,n,m,l,k,j,i,h,g,f,d,c');


```
