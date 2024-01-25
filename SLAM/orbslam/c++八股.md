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
	- malloc 分配失败，返回 NULL
4. 分配空间
	- new 通常在free store 自由存储区上分配空间
			1. operator new 可重写
			2. 申请足够空间
			3. 调用构造函数，初始化成员变量，已经拥有物理内存
	- malloc 大小小于128k，通过brk进行系统调用进行在堆空间进行内存分配，大于128k通过mmap系统调用在文件映射区进行分配
		1. 无初始化，最开始只有虚拟内存，最后通过缺页中断进行映射到真实物理内存
		2. 在头部多分配sizeof(size_t) 的空间
5. 释放
	- delete 需要对象类型的指针
		1. 调用析构函数
		2. operator delete 可重写
		3. 释放空间
	- free是 void * 类型的指针，不需要具体类型
		- 释放多大空间根据头部存储信息来判定





