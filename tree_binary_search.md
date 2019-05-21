## 场景描述

我们学习过数据结构，我们就知道 `二分查找` 的效率有多高，本文就是二分查找的一个示例实现。

## 实现原理

将输入的一段无序的数值创建成一颗数

## 实现代码

1. PHP

```php
class Node
{
    public $left = null;
    public $right = null;
    public $val = null;

    public function __construct($val)
    {
        $this->val = $val;
    }
}

class Tree
{
    public $T = null;

    public function __construct(array $elements = [])
    {
        $T = new Node(array_shift($elements));

        while ($element = array_shift($elements)) {
           $T = self::insert($T, new Node($element));
        }

        $this->T = $T;
    }

    public static function insert(Node $T = null, Node $N)
    {
        if ($T === null) return $N;

        if ($T->val < $N->val) {
            $T->right = self::insert($T->right, $N);
        } elseif ($T->val > $N->val) {
            $T->left = self::insert($T->left, $N);
        }

        return $T;
    }

    public function search($element)
    {
        $T = $this->T;

        while($T) {
            if ($T->val < $element) {
                $T = $T->right;
            } elseif ($T->val > $element) {
                $T = $T->left;
            } else {
                break;
            }
        }

        return $T;
    }
}

$elementsTree = new Tree([3,5,4,2,23,123,345,123,567,234,8,234,35,45,623,4234,236,54,23456,9]);

var_dump($elementsTree->search(4)); // Node
var_dump($elementsTree->search(123)); // Node
var_dump($elementsTree->search(1000)); // null
var_dump($elementsTree->search(567)); // Node
var_dump($elementsTree->search(9)); // Node
```

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

  this.T = T;
}

Tree.prototype.search = function (element) {
  var T = this.T;

  while(T) {
    if (T.val < element) {
      T = T.right;
    } else if (T.val > element) {
      T = T.left;
    } else {
      break;
    }
  }

  return T;
}

var elementsTree = new Tree([3,5,4,2,23,123,345,123,567,234,8,234,35,45,623,4234,236,54,23456,9]);

console.log(elementsTree.search(4)); // Node
console.log(elementsTree.search(123)); // Node
console.log(elementsTree.search(1000)); // null
console.log(elementsTree.search(567)); // Node
console.log(elementsTree.search(9)); // Node
```
