---
layout: post
title: equals与hashCode方法
category: 转载
tags: Java,equals,hashCode
description: equals与hashCode方法
---

equals 与 hashCode:
前提： 谈到hashCode就不得不说equals方法，二者均是Object类里的方法。由于Object类是所有类的基类，所以一切类里都可以重写这两个方法。

原则 1 ： 如果 x.equals(y) 返回 “true”，那么 x 和 y 的 hashCode() 必须相等 ；
原则 2 ： 如果 x.equals(y) 返回 “false”，那么 x 和 y 的 hashCode() 有可能相等，也有可能不等 ；（注：但实际编程中，提倡hashCode也不相等，可以提高hashMap场景下的性能）
原则 3 ： 如果 x 和 y 的 hashCode() 不相等，那么 x.equals(y) 一定返回 “false” ；
原则 4 ： 一般来讲，equals 这个方法是给用户调用的，而 hashcode 方法一般用户不会去调用 ；
原则 5 ： 当一个对象类型作为集合对象的元素时，那么这个对象应该拥有自己的equals()和hashCode()设计，而且要遵守前面所说的几个原则

hashCode 的常规协定是：

在 Java 应用程序执行期间，在对同一对象多次调用 hashCode 方法时，必须一致地返回相同的整数，前提是将对象进行 equals 比较时所用的信息没有被修改。从某一应用程序的一次执行到同一应用程序的另一次执行，该整数无需保持一致。

如果根据 equals(Object) 方法，两个对象是相等的，那么对这两个对象中的每个对象调用 hashCode 方法都必须生成相同的整数结果。

如果根据 equals(java.lang.Object) 方法，两个对象不相等，那么对这两个对象中的任一对象上调用 hashCode 方法 不要求 一定生成不同的整数结果。但是，程序员应该意识到，为不相等的对象生成不同整数结果可以提高哈希表的性能。

在 Java 规范中，它对 equals() 方法的使用必须要遵循如下几个规则：

equals 方法在非空对象引用上实现相等关系：

1、自反性：对于任何非空引用值 x，x.equals(x) 都应返回 true。

2、对称性：对于任何非空引用值 x 和 y，当且仅当 y.equals(x) 返回 true 时，x.equals(y) 才应返回 true。

3、传递性：对于任何非空引用值 x、y 和 z，如果 x.equals(y) 返回 true，并且 y.equals(z) 返回 true，那么 x.equals(z) 应返回 true。

4、一致性：对于任何非空引用值 x 和 y，多次调用 x.equals(y) 始终返回 true 或始终返回 false，前提是对象上 equals 比较中所用的信息没有被修改。

5、对于任何非空引用值 x，x.equals(null) 都应返回 false。

参考：https://blog.csdn.net/justloveyou_/article/details/52464440
           http://wiki.jikexueyuan.com/project/java-enhancement/java-thirteen.html
