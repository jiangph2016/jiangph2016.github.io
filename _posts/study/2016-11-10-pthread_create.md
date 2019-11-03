---
layout: post
title: pthread_create()函数传递整型参数问题
category: 学习
tags: Linux
keywords: Linux,pthread
---

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