
## 场景描述

**二叉搜索树** 的实现。

## 实现原理

任何节点的左儿子一定比根节点小，右儿子一定比根节点大。

## 实现代码

### 大顶堆实现

1. PHP

  待实现

2. JavaScript

```javascript
function Node (key, value) {
  this.left = null;
  this.right = null;
  this.key = key;
  this.value = value;
  this.length = 1; // length 表示该节点的子孙节点总数（包含自己）
  this.height = 1; // 当前节点的高度（树的高度）
};

Node.prototype.resize = function () {
  this.length = (this.left ? this.left.length : 0) +
              (this.right ? this.right.length : 0) + 1;
}

Node.prototype.getMin = function () {
  if (!this.left) return this;

  return this.left.getMin();
}

Node.prototype.getMax = function () {
  if (!this.right) return this;

  return this.right.getMax();
}

Node.prototype.get = function (key) {
  var target = null;

  if (this.key === key) {
    target = this;
  } else if (this.key < key) {
    if(!this.right) return null;
    target = this.right.get(key);
  } else {
    if(!this.left) return null;
    target = this.left.get(key);
  }

  return target;
}

Node.prototype.insert = function (key, value) {
  if (this.key === key) {
    this.value === value
  } else {
    if (this.key > key) {
      if (!this.left) {
        this.left = new Node(key, value);
      } else {
        this.left = this.left.insert(key, value);
      }
    } else {
      if (!this.right) {
        this.right = new Node(key, value);
      } else {
        this.right = this.right.insert(key, value);
      }
    }

    this.resize();
  }

  return this;
}

Node.prototype.delMin = function () {
  if (!this.left) return this.right;

  this.left = this.left.delMin();
  this.resize();

  return this;
}

Node.prototype.delMax = function () {
  if (!this.right) return this.left;

  this.right = this.right.delMax();
  this.resize();

  return this;
}

Node.prototype.del = function (key) {
  if (this.key > key) {
    if (!this.left) return null;
    this.left = this.left.del(key);
  } else if (this.key < key) {
    if (!this.right) return null;
    this.right = this.right.del(key);
  } else {
    if (this.left && this.right) {
      var min = this.right.getMin();

      this.key = min.key;
      this.value = min.value;
      this.right = this.right.del(key);

      return this;
    } else {
      if (!this.left) {
        return this.right;
      } else if (!this.right) {
        return this.left;
      }
    }
  }

  this.resize();

  return this;
}

Node.prototype.print = function(key) {
  var str = '';
  if (this.left) str += this.left.print();
  str += this.key;
  if (this.right) str += this.right.print();

  return str;
}

// ===== Tree =====

function Tree (key, value) {
  this.root = new Node(key, value);
};

Tree.prototype.get = function (key) {
  return this.root.get(key);
}

Tree.prototype.set = function(key, value) {
  return this.root.insert(key, value);
};

Tree.prototype.size = function () {
  return this.root.length;
}

Tree.prototype.getMin = function() {
  return this.root.getMin();
}

Tree.prototype.getMax = function() {
  return this.root.getMax();
}

Tree.prototype.delMin = function() {
  return this.root.delMin();
}

Tree.prototype.delMax = function() {
  return this.root.delMax();
}

Tree.prototype.del = function(key) {
  return this.root.del(key);
}

Tree.prototype.print = function(key) {
  return this.root.print();
}

function getValueOfKey(char) {
  return char.charCodeAt(0).toString(16)
}

var tree = new Tree('a', getValueOfKey('a'));

var keys = 'bcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ';
for (var i = 0; i < keys.length; i++) {
  tree.set(keys[i], getValueOfKey(keys[i]));
}

console.log(tree.print()); // ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
console.log(tree.getMax()); // Node {left: null, right: null, key: "z", value: "7a", length: 1, …}
console.log(tree.getMin()); // Node {left: null, right: Node, key: "A", value: "41", length: 25, …}
console.log(tree.get('H')); // Node {left: null, right: Node, key: "H", value: "48", length: 18, …}
console.log(tree.get('j')); // Node {left: null, right: Node, key: "j", value: "6a", length: 17, …}
console.log(tree.delMin()); 
console.log(tree.print()); // BCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz
console.log(tree.delMax()); 
console.log(tree.print()); // BCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxy
console.log(tree.del('b'));
console.log(tree.print()); // BCDEFGHIJKLMNOPQRSTUVWXYZacdefghijklmnopqrstuvwxy
console.log(tree.set('0', 12));
console.log(tree.print()); // 0BCDEFGHIJKLMNOPQRSTUVWXYZacdefghijklmnopqrstuvwxy
console.log(tree.set('廖', 123));
console.log(tree.print()); // 0BCDEFGHIJKLMNOPQRSTUVWXYZacdefghijklmnopqrstuvwxy廖
```
