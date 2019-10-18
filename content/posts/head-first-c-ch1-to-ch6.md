---
title: "Head First C Ch1 to Ch6"
subtitle: "一些值得注意的小細節"
date: 2019-10-06T17:25:46+08:00
draft: false
toc: true
image: /img/head-first-c.jpeg
tags: ["讀書筆記", "Head First C", "C"]
---

因為前面的內容比較基本，就只挑一些比較有趣（或比較沒看過）的功能來介紹～

## `A && B` 表示「假如A執行成功的話，就緊接著執行B」
---
例如編譯後馬上執行

```bash
$ gcc zork.c -o zork && ./zork
```

## `break` 不能用來中斷 `if` 陳述式
---
誤用 `break` 去中斷 `if` 的後果可以看[這裡](http://users.csc.calpoly.edu/~jdalbey/SWE/Papers/att_collapse.html)（The 1990 AT&T Long Distance Network Collapse）

## 串接指定
---
`x = 4` 運算式本身也具有回傳值 `4`<br>
所以說 `y = x = 4;` 就是把 `y` 設定成 `x = 4` 的回傳值 `4`<br>
i.e. 把 `x` 跟 `y` 都設成 `4`

## 陣列變數當作指標使用
---
`doses[3] == *(doses + 3) == *(3 + doses) == 3[doses]`

> 所以 `doses[3]` 跟 `3[doses]` 是一樣意思的喔～

## 字串實字（String literals）永遠不能被更新
---
指向字串實字的變數**不能**用來改變該字串的內容

```c
char *cards = "JQK";
cards[2] = cards[1]; // 這不合法！！
```

如果從字串實字重新建立一個陣列，就**能夠**修改該陣列

```c
char cards[] = "JQK";
cards[2] = cards[1]; // 合法！！
```

## 記憶體存儲器（位址由高至低）
---

名稱               	|用途
-------------------	|-----------------------
Stack（堆疊）       	| 區域變數（local variables）
Heap（堆積）        	| 動態記憶體（dynamic memory）
Globals（全域區段）  	| 全域變數（global variables）
Constants（常數區段）	| 唯讀
Code（程式碼區段）   	| 唯讀

## 指標構成的陣列
---

```c
char* name_for_dog[] = {"Bowser", "Bonza", "Snodgrass"};
```
每個**字串實字**都會有一個**指標**指向他

## `|` 符號（管線）是將一個行程之標準輸出連結到另一個行程之標準輸入的符號
---

```bash
$ (./bermuda | ./geo2json) < spooky.csv > output.json
```


## 命令列選項（command-line option）
---
內容待補（P.149）

## `struct`指標表示法（`t`是指向`struct`的指標）
---
`(*t).age`、`t->age`
這兩個表達式表達同一件事

## `strdup()` 函式能夠在 *heap* 某處重新產生該字串的完整副本
---

```c
char *copy = strdup(s);
```
`strdup()`在`string.h`中，函式計算該字串多長，然後呼叫`malloc()`函式，在 ***heap*** 上配置正確數量的字元
