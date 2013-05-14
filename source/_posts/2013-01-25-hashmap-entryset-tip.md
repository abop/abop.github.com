---
layout: post
title: "A trap when iterate on HashMap.entrySet()"
description: ""
category: Dev
tags: [java, HashMap, entrySet]
---

 >If the map is modified while an iteration over the set is in progress (except through the iterator's own remove operation, or through the setValue operation on a map entry returned by the iterator) the results of the iteration are undefined. 

## Wrong code：

```java
for (Entry<String, Long> entry : sizeMap.entrySet()) {
	if (xxx=xxx) {
		sizeMap.remove(entry.getKey());//这里删除会造成下载迭代时抛出java.util.ConcurrentModificationException异常
	} 
}
```

## Right code：
```java
for (Iterator<Entry<String, Long>> itr = sizeMap.entrySet().iterator();itr.hasNext();) {
	Entry<String, Long> entry = itr.next();
	if(xxx=xxx) {
		itr.remove();
	}
}
```
