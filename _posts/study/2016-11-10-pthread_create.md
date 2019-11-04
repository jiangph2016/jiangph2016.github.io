---
layout: post
title: pthread_create()函数传递整型参数问题
category: 学习
tags: Linux
keywords: Linux,pthread
---

__头文件__: pthread.h

__原型__ :
```
int pthread_create(pthread_t * thread, const pthread_arrt_t* attr,void*(*start_routine)(void *), void* arg);
```
## 用法

1. __thread__: 新线程的标识符,为一个整型。

2. __attr参数__: 用于设置新线程的属性。给传递NULL表示设置为默认线程属性。

3. __start_routine和arg参数__: 分别指定新线程将运行的函数和参数。start_routine返回时,这个线程就退出了

4. __返回值__: 成功返回0,失败返回错误号。

5. __其他__:
线程id的类型是thread_t,它只在当前进程中保证是唯一的,在不同的系统中thread_t这个类型有不同的实现，调用pthread_self()可以获得当前线程的id.  
进程id的类型时pid_t,每个进程的id在整个系统中是唯一的,调用getpid()可以获得当前进程的id,是一个正整数值。


## 使用
根据我的尝试，线程的函数在定义的时候，必须传入参数，使用的时候第四个参数可以填入NULL
```
void *func(void *t)
{
	printf("hello world!\n");
	//参数*t不使用
}
int main()
{
	pthread_t t;
	pthread_create(&t,NULL,func,NULL);
	//第四个参数不填
	pthread_join(t,NULL);
	return 0;
}
```
传入具体的整型值的时候，有两种方法。

```
void *func(void *t)
{
	printf("This value is %d\n",*(int *)t);
}
int main()
{
	int value=5;
	pthread_t t;
	pthread_create(&t,NULL,func,&value);
	//将value的地址传过去.
	pthread_join(t,NULL);
	return 0;
}
```
这种方法有一个弊端，因为是传入的地址，所以线程函数中的t值会随着value的值一起修改。
第二种方法：
第二种方法琢磨了好久，因为编译的时候一直报错，后来搞明白是64位机器的原因。
```
#define TYPE32 int
#define TYPE64 long
void *func(void *t)
{
	printf("This value is %d\n",(TYPE64)(t));
	//需要根据机器的位数选择int还是long
	//不然编译的时候就会出现精度丢失的错误
}
int main()
{
	int value = 5;
	pthread_t t;
	pthread_create(&t,NULL,func,(void *)value);
	pthread_join(t,NULL);
	return 0;
}

```