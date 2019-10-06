---
title: "Head First C Ch7"
subtitle: "函式的進階用法"
date: 2019-10-06T17:41:14+08:00
draft: false
toc: true
image: /img/head-first-c.jpeg
tags: ["讀書筆記", "Head First C", "C"]
---

## 指向函式的指標
---
用法是 `回傳類型(*指標變數)(參數型別)`
像這樣 `char** (*names_fn)(char*, int)`

舉個小小的例子

```c
int (*wrap_fn)(int);
wrap_fn = go_to_wrap_speed; //儲存go_to_wrap_speed()函式的位址
wrap_fn(4); //這就跟呼叫go_to_wrap_speed(4)函式一樣
```

## 排序函式（sort function）與比較器函式（comparator function）
---
在`stdlib.h`裡面有一個`qsort()`函式，看起來長這樣

```c
qsort(void array, //指向陣列的指標
	  size_t length, //該陣列的長度
	  size_t item_size, //陣列裡每個元素的尺寸
	  int (*compar)(const void *, const void *)); //指向比較陣列裡頭兩個項目的函式
```
示範的程式如下
放在資料夾 [qsort\_with\_comparator]() 裡面

## 函式指標陣列
---
用法是`回傳類型(*指標變數[])(參數型別)`

```c
void (*replies[])(response) = {dump, second_chance, marriage}; //陣列裡面存放指向函式的指標
```
範例程式在 [mail_merge]() 裡面

## 可變參數函式（Variadic function）
---
直接來個範例：

```c
#include <stdarg.h> //處理可變參數函式的程式碼都在這

void print_ints(int args, ...) { //「...」被稱作「省略」（ellipsis），告訴你後面還有東西
	va_list ap; //va_list 用來儲存要傳進你的函式的額外引數
	va_start(ap, args); //va_start 指名可變動引數從哪裡開始
	for(int i = 0; i < args; i++)
		printf("argument: %d\n", va_arg(ap, int)); //va_arg 接受兩個值：va_list 和下一個引數的「型別」
	va_end(ap); //讀完之後記得要結束
}

//call the function
print_ints(3, 79, 101, 32);
print_ints(2, 10, 2);
```

> 小提示

>	1. `va_end`、`va_start` 等東西實際上是**巨集**，不是函式
>	2. 至少要有**一個**固定參數
>	3. 如果嘗試讀取比傳進來的還要多的引數，會發生隨機錯誤（random error）
