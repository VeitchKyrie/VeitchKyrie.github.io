---
layout:     post
title:      关于std::this_thread::yield()的理解和sleep_for区别
subtitle:   问题分析及解决
date:       2019-08-28
author:     VK
catalog: true
tags:
    - c++
---

# std::this_thread::yield()和sleep_for() 区别

**引自stack overflow：**

> std::this_thread::yield tells the implementation to reschedule the execution of threads, that should be used in a case where you are in a busy waiting state, like in a thread pool:

```c++
while(true) {
  if(pool.try_get_work()) {
    // do work
  }
  else {
    std::this_thread::yield(); // other threads can push work to the queue now
  }
}

```

> std::this_thread::sleep_for can be used if you really want to wait for a specific amount of time.
>
> This can be used for task, where timing really matters, e.g.: if you really only want to wait for 2 seconds. (Note that the implementation might wait longer than the given time duration)

**个人理解:** 

(1) std::this_thread::yield(); 是将当前线程所抢到的CPU”时间片A”让渡给其他线程(其他线程会争抢”时间片A”, 

注意: 此时”当前线程”不参与争抢). 
等到其他线程使用完”时间片A”后, 再由操作系统调度, 当前线程再和其他线程一起开始抢CPU时间片.

(2) 如果将 std::this_thread::yield();上述语句修改为: return; ,则将未使用完的CPU”时间片A”还给操作系统, 再由操作系统调度, 当前线程和其他线程一起开始抢CPU时间片.

std::this_thread::yield(): 适合应用在 — “that should be used in a case where you are in a busy waiting state”, 如果存在此情况, 线程实际的执行代码类似如下:

```c
void thread_func()
{
  while (1)
  {
      if (!HasTask())
      {
          return;
      }
     // else
     // {
     //     // do work
     // }
  }
}
```

因为条件不满足, 所以此线程很快就返回(然后立即与其他线程竞争CPU时间片), 结果就是此线程频繁的与其他线程争抢CPU时间片, 从而影响程序性能,使用 std::this_thread::yield() 后, 就相当于”当前线程”检查条件不成功后, 将其未使用完的”CPU时间片”分享给其他线程使用, 等到其他线程用完后, 再和其他线程一起竞争.

**结论:** 
std::this_thread::yield() 的目的是避免一个线程(that should be used in a case where you are in a busy waiting state)频繁与其他线程争抢CPU时间片, 从而导致多线程处理性能下降.

std::this_thread::yield() 是让当前线程让渡出自己的CPU时间片(给其他线程使用) 
std::this_thread::sleep_for() 是让当前休眠”指定的一段”时间.

sleep_for()也可以起到 std::this_thread::yield()相似的作用, (即:当前线程在休眠期间, 自然不会与其他线程争抢CPU时间片)但两者的使用目的是大不相同的: 
std::this_thread::yield() 是让线程让渡出自己的CPU时间片(给其他线程使用) 
sleep_for() 是线程根据某种需要, 需要等待若干时间.
