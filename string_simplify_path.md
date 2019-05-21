## 简化路径

该场景为 [Leetcode](https://leetcode-cn.com/explore/interview/card/bytedance/242/string/1013/) 中的题目。

以 Unix 风格给出一个文件的绝对路径，你需要简化它。或者换句话说，将其转换为规范路径。

在 Unix 风格的文件系统中，一个点（.）表示当前目录本身；此外，两个点 （..） 表示将目录切换到上一级（指向父目录）；两者都可以是复杂相对路径的组成部分。更多信息请参阅：Linux / Unix中的绝对路径 vs 相对路径

请注意，返回的规范路径必须始终以斜杠 / 开头，并且两个目录名之间必须只有一个斜杠 /。最后一个目录名（如果存在）不能以 / 结尾。此外，规范路径必须是表示绝对路径的最短字符串。

如：

**输入**：`/a//b////c/d//././/..`
**输出**：`/a/b/c`

## 实现原理

该模式可以使用 **队列** 和 **堆栈** 实现，实现方法如下：

1. 将源目录路径按层分开，将每层目录名放到队列中。
2. 从队列中一个一个读取目录，对目录做判断，如果目录名为 `..` 则表示需要回到上一层，则从堆栈中 `Pop` 出栈首元素；如果目录名为 `.` 或 空字符串，则不需要做任何操作；其他目录名直接压入堆栈。

## 实现代码

1. PHP

```php

class Solution 
{
    /**
     * @param String $path
     * @return String
     */
    function simplifyPath($path) 
    {
        $paths = preg_split('/\/+/', $path);
        
        $newPaths = [];
        
        foreach($paths as $path) {            
            if ($path === '..') {
                array_pop($newPaths);
            } elseif ($path === '.' || $path === '') {
                // do nothing
            } else {
                array_push($newPaths, $path);
            }
        }
        
        return '/'.implode('/', $newPaths);
    }
}

\Solution::simplifyPath('/a//b////c/d//././/..'); // /a/b/c
\Solution::simplifyPath('/a/../../b/../c//.//'); // /c
\Solution::simplifyPath('/a/./b/../../c/'); // /c
\Solution::simplifyPath('/a/./b/../../c/'); // /c
\Solution::simplifyPath('/../'); // /
```

2. JavaScript

```javascript
/**
 * @param {string} path
 * @return {string}
 */
var simplifyPath = function(path) {
    return '/' + path.split(/\/+/).reduce(function(paths, p) {
        if (p === '..') {
            paths.pop();
        } else if (p === '.' || p === '') {
            // do nothing
        } else {
            paths.push(p);
        }
        
        return paths;
    }, []).join('/');
};

simplifyPath('/a//b////c/d//././/..'); // /a/b/c
simplifyPath('/a/../../b/../c//.//'); // /c
simplifyPath('/a/./b/../../c/'); // /c
simplifyPath('/a/./b/../../c/'); // /c
simplifyPath('/../'); // /
```
