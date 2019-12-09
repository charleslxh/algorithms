
## 场景描述

有一个关键词字典，输入一句话，找出句子中的关键字。

通常用于敏感词过滤。

## 实现原理

使用 Trie 树实现。

## 实现代码

1. `Golang` 实现源码：

```go
package main

import (
	"strings"
  "testing"
)

type TrieNode struct {
	Value byte
	IsLeaf bool
	Level int
	IsWordEnd bool
	Children map[byte]*TrieNode
}

func (t *TrieNode) HasChild(w byte) bool {
	if _, ok := t.Children[w]; ok {
		return true
	}

	return false
}

func (t *TrieNode) GetChild(w byte) *TrieNode {
	if t.HasChild(w) {
		return t.Children[w]
	}

	return nil
}

func (t *TrieNode) AddChild(w byte) *TrieNode {
	if !t.HasChild(w) {
		if t.IsLeaf {
			t.Children = map[byte]*TrieNode{}
		}

		t.Children[w] = &TrieNode{
			Value: w,
			IsLeaf: true,
			Children: nil,
		}

		t.IsLeaf = false
	}

	return t.GetChild(w)
}

type Dict struct {
	count int
	root *TrieNode
}

func (d *Dict) HasWord(word string) bool {
	return d.hasWord(word)
}

func (d *Dict) hasWord(word string) bool {
	trieTreePtr := d.root

	for _, w := range []byte(word) {
		if !trieTreePtr.HasChild(w) {
			return false
		}

		trieTreePtr = trieTreePtr.GetChild(w)
	}

	return true
}

func (d *Dict) AddWorld(word string) {
	d.addWorld(word)
}

func (d *Dict) addWorld(word string) {
	word = strings.TrimSpace(word)

	if len(word) == 0 {
		return
	}

	bs := []byte(word)
	node := d.root

	for _, w := range bs {
		node = node.AddChild(w)
	}

	d.count++
	node.IsWordEnd = true
}

func (d *Dict) print() {
}

func (d *Dict) SearchWords(text string) []string {
	return d.searchWords(text)
}

func (d *Dict) searchWords(text string) []string {
	text = strings.TrimSpace(text)
	var words []string

	if len(text) == 0 {
		return words
	}

	bs := []byte(text)
	triePtr := d.root
	wordPtr1 := 0
	wordPtr2 := 0

	for wordPtr1 < len(bs) {
		if wordPtr2 < len(bs) && triePtr.HasChild(bs[wordPtr2]) {
			triePtr = triePtr.GetChild(bs[wordPtr2])
			wordPtr2++
		} else {
			if triePtr.IsWordEnd {
				if wordPtr2 > cap(bs) {
					wordPtr2 = cap(bs)
				}
				words = append(words, string(bs[wordPtr1:wordPtr2]))
				wordPtr1 = wordPtr2
			} else {
				wordPtr1++
				wordPtr2 = wordPtr1
			}
			triePtr = d.root
		}
	}

	return words
}

func NewDict(words ...string) *Dict {
	d := &Dict{
		count: 1,
		root:  &TrieNode{
			Value:    0,
			IsLeaf:   true,
			Level:    0,
			IsWordEnd: false,
			Children: nil,
		},
	}

	for _, w := range words {
		d.addWorld(w)
	}

	return d
}

func TestTire_HasWorld(t *testing.T) {
	dict := NewDict("hello", "world")

	assert.True(t, dict.HasWord("hello"))
	assert.True(t, dict.HasWord("world"))
	assert.False(t, dict.HasWord("hi"))
	dict.AddWorld("hi")
	assert.True(t, dict.HasWord("hi"))
}

func TestTire_AddWorld(t *testing.T) {
	dict := NewDict()

	assert.False(t, dict.HasWord("hello"))
	dict.AddWorld("hello")
	assert.True(t, dict.HasWord("hello"))

	assert.False(t, dict.HasWord("world"))
	dict.AddWorld("world")
	assert.True(t, dict.HasWord("world"))

	assert.False(t, dict.HasWord("hi"))
	dict.AddWorld("hi")
	assert.True(t, dict.HasWord("hi"))
}

func TestTire_SearchWords(t *testing.T) {
	dict := NewDict()
	dict.AddWorld("hello")
	dict.AddWorld("world")
	dict.AddWorld("he")
	dict.AddWorld("her")
	dict.AddWorld("his")
	dict.AddWorld("she")
	dict.AddWorld("hers")
	dict.AddWorld("中国")
	dict.AddWorld("美国")

	assert.Equal(t, []string{"hello"}, dict.searchWords("hello"))
	assert.Equal(t, []string{"hello"}, dict.searchWords("hello111helo"))
	assert.Equal(t, []string{"hello", "world"}, dict.searchWords("helloworld"))
	assert.Equal(t, []string{"hello"}, dict.searchWords("helloxxxwxorld"))
	assert.Equal(t, []string{"hello", "world"}, dict.searchWords("helloxxxxxxxworldxxxxxx"))
	assert.Equal(t, []string{"hello", "world"}, dict.searchWords("xxhelloxxxxxxxworldxxxxxx"))
	assert.Equal(t, []string{"hello", "hello", "world"}, dict.searchWords("hellohelloxxxxxxxworldxxxxxx"))
	assert.Equal(t, []string{"hello", "hello", "world"}, dict.searchWords("hellohelloxxx中x国xxxworldxxxxxx"))
	assert.Equal(t, []string{"hello", "hello", "中国", "world"}, dict.searchWords("hellohelloxxx中国xxxworldxxxxxx"))
	assert.Equal(t, []string{"hello", "hello", "美国", "world"}, dict.searchWords("hellohelloxxx中美国xxxworldxxxxxx"))
	assert.Equal(t, []string{"he", "her", "hers", "his", "hello", "world", "中国", "美国"}, dict.searchWords("heherhershishelloworld中国美国"))
}
```

2. `Javascript` 实现源码：

```js
function TrieNode(value) {
  this.value = value;
  this.isWordEnd = false;
  this.children = {};
}

TrieNode.prototype.hasChild = function(w) {
  return this.children[w] !== undefined && this.children[w] !== null;
}

TrieNode.prototype.getChild = function(w) {
  if (this.hasChild(w)) {
    return this.children[w]
  }

  return null
}

TrieNode.prototype.addChild = function(w) {
  if (!this.hasChild(w)) {
    trieNode = new TrieNode(w);
    trieNode.isWordEnd = false;
    this.children[w] = trieNode;
  }

  return this.getChild(w)
}

function Dict() {
  this.count = 0;
  this.root = new TrieNode(0);
}

Dict.prototype.addWord = function (word) {
  if (word.length === 0) {
    return
  }

  var node = this.root;

  for (var i = 0; i < word.length; i++) {
    node = node.addChild(word[i]);
  }

  node.isWordEnd = true;
  this.count++
}

Dict.prototype.searchWords = function(text) {
  var words = [];

  if (text.length === 0) {
    return words;
  }

  var triePtr = this.root;
  var textPtr1 = 0;
  var textPtr2 = 0;

  while (textPtr1 < text.length) {
    if (triePtr.hasChild(text[textPtr2])) {
      triePtr = triePtr.getChild(text[textPtr2]);
      textPtr2++
    } else {
      if (triePtr.isWordEnd) {
        words.push(text.substring(textPtr1, textPtr2));
        textPtr1 = textPtr2;
      } else {
        textPtr1++
        textPtr2 = textPtr1;
      }
      triePtr = this.root;
    }
  }

  return words
}

var dict = new Dict()

dict.addWord("hello")
dict.addWord("world")
dict.addWord("he")
dict.addWord("she")
dict.addWord("her")
dict.addWord("his")
dict.addWord("hers")
dict.addWord("中国")
dict.addWord("美国")

console.log(dict.searchWords("hello"))
console.log(dict.searchWords("hello111helo"))
console.log(dict.searchWords("helloworld"))
console.log(dict.searchWords("helloxxxwxorld"))
console.log(dict.searchWords("helloxxxxxxxworldxxxxxx"))
console.log(dict.searchWords("xxhelloxxxxxxxworldxxxxxx"))
console.log(dict.searchWords("hellohelloxxxxxxxworldxxxxxx"))
console.log(dict.searchWords("hellohelloxxx中x国xxxworldxxxxxx"))
console.log(dict.searchWords("hellohelloxxx中国xxxworldxxxxxx"))
console.log(dict.searchWords("hellohelloxxx中美国xxxworldxxxxxx"))
console.log(dict.searchWords("heherhershishelloworld中国美国"))
```
