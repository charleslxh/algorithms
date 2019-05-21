## 场景描述

判断某个 IP 是否在指定的 Trusted IP 列表中。

比如，判断 IP `192.168.0.2` 是否在 Trusted IP 列表 [`192.168.0.0/16`，`172.168.10.1`] 中。

## 实现原理

**非网段 IP** 判断很简单，只需要判断目标 IP 与该值是否相等。

**网段 IP** 如何判断依据如下：

1. 根据网段 IP 求出该网段的 **子网掩码（mask）** 和 **子网（subnet）**。
2. 判断目标 IP 与掩码球异或，如果等于子网说明属于同一个子网。

**主要依据：同一个字网下的所有子网 IP 的网络号都是相同的，只是主机号不同，网络号相同，则表示在同一个子网内。**

## 实现代码

1. PHP

```php

Class Solution 
{
    public static isTrustedIp($targetIp, array $whitelist = [])
    {
        $isTrusted = false;
        $targetIp = ip2long($targetIp);
        
        foreach ($whitelist as $trustIp) {
            $isRangeExpression = strpos($trustIp, '/') > -1;
            
            if ($isRangeExpression) {
                $rets = explode('/', $trustIp);
                
                $mask = -1 << (32 - $rets[1]);
                $subnet = ip2long($rets[0]) & $mask;
                
                if (($targetIp & $mask) === $subnet) {
                    $isTrusted = true;
                    
                    break;
                }
            } else {
                if ($targetIp === ip2long($trustIp)) {
                    $isTrusted = true;
                    
                    break;
                }
            }
        }
        
        return $isTrusted;
    }
}
```

2. JavaScript

```javascript
var ip2long = function (ip) {
    let ips = ip.split('.');
    
    if (ips.length !== 4) {
        throw new Error('Invalid IP Expression.')
    }
    
    return (ips[0] << 24) | ips[1] << 16 | ips[2] << 8 | ips[3];
}

var isTrustedIp = function($targetIp, $whitelist) {
    let isTrusted = false;
    const targetIp = ip2long($targetIp);
    
    for ($trustIp of $whitelist) {
        $isRangeExpression = $trustIp.indexOf('/') > -1;
        
        if ($isRangeExpression) {
            $rets = explode('/', $trustIp);
                
            $mask = -1 << (32 - $rets[1]);
            $subnet = ip2long($rets[0]) & $mask;
                
            if (($targetIp & $mask) === $subnet) {
                $isTrusted = true;
                    
                break;
            }
        } else {
            if ($targetIp === ip2long($trustIp)) {
                $isTrusted = true;
                    
                break;
            }
        }
    }
}
```
