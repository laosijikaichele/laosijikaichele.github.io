---
layout: post
title: ConcurrentHashMap-tableSizeFor方法的改进
category: 原创
tags: ConcurrentHashMap,Java
description: ConcurrentHashMap-tableSizeFor方法的改进
---

ConcurrentHashMap.tableSizeFor(int c)方法：
```
private static final int tableSizeFor(int c) {
        int n = c - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return (n < 0) ? 1 : (n >= MAXIMUM_CAPACITY) ? MAXIMUM_CAPACITY : n + 1;
    }
```

改进版：

```
private static final int tableSizeFor(int c) {
        if(c <= 1) {return 1;}
        if(c > (MAXIMUM_CAPACITY >>> 1)) {return MAXIMUM_CAPACITY;}
        int n = c - 1;
        n |= n >>> 1;
        n |= n >>> 2;
        n |= n >>> 4;
        n |= n >>> 8;
        n |= n >>> 16;
        return n + 1;
    }
```

