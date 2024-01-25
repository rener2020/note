---
title: c++八股 
---
2024-01


# malloc、free和new、delete 的区别

- malloc、free是c语言的库函数
- new、delete是c\+\+的操作符

用于分配和释放内存

## 使用区别
1. 使用
	- new 自动计算内存分配大小
	- malloc 手动计算内存分配大小
2. 返回值
	- new的返回值是改类型对象的指针
	- malloc返回的是一个 void * 已擦除类型，需要进行类型转换
		`Task *ptack = malloc(sizeof(*ptask))`
3. 失败
	- new 分配失败，抛出异常
	- malloc 分配失败，返回null




