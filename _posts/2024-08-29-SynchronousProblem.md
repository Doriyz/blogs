---
title: "同步问题"
date: 2024-08-29
---

多核架构下，系统中的协作进程（cooperating process）能够直接共享代码和数据，因此需要建立同步机制，来维护数据间的一致性。竞争条件指的是多进程并发访问和操作同一数据，执行结果与特定访问顺序有关的现象。为了防止竞争条件，需要确保每次只有一个进程可以操作变量。

临界区是修改公共变量的代码，临界区问题（critical-section problem）规定了协作进程访问临界区的方式，从而避免多个进程同时在临界区执行。

临界区问题应满足三个条件：

+ 互斥： 进程P1在临界区执行时，其他进程不允许进入临界区
+ 进步： 进程P1退出临界区后，如果有进程需要进入临界区，需要从等待进入的进程中挑选进程，执行临界区
+ 有限访问： 每个进程等待进入临界区的时间内，其他进程进入临界区的次数有限

## Peterson解决方案

基于软件的解决方案，针对两个进程交错执行的情况，不能确保在所有计算机上正确运行。

共享的数据项：

```java
int turn; // 规定哪个进程允许进入临界区
boolean flag[2]; // 规定每个进程是否需要进入临界区
```

进程Pi的结构：

```java
do{
  flag[i] = true;
  turn = j;
  while(turn == j && flag[j] == true){}  // Pj执行时, Pi自旋
  /* critical section of Pi */
  flag[i] = false;
}
```

turn相当于一个自旋锁，获得自旋锁（也就是turn值对应的进程）能够执行临界区。


## 互斥锁

定义变量：

```java
boolean availble; // 规定是否可以进入临界区
```

自旋锁的实现：

```java
void acquire(){
  while(avaible == false){
    /* busy wait */
  }
  availble = false;
}

void release(){
  avaible = true;
}
```

+ 缺点：忙等待浪费了CPU周期
+ 优点：没有上下文切换，使用于临界区短的场景

## 信号量

信号量(semophore)通常分为计数信号量和二进制信号量，其中二进制信号量相当于互斥锁。计数信号量可以控制具有多个实例的某种资源。

一个信号量只执行两种操作，wait（也叫操作P）和signal（也叫操作V）。

使用忙等待的信号量实现（每个信号量是一个整型变量）：

```java
void wait(S){
  while(S <= 0){
    /* busy wait */
  }
  S--;
}

void signal(S){
  S++;
}
```

使用挂起的信号量实现（每个信号量对应一个队列，用于记录等待信号量的进程）：

```java
typedef struct{
  int value;
  struct process *list;
}semophore;


void wait(S){
  if(S <= 0){

  }
}