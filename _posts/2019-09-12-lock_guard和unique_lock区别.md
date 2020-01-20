---
layout:     post
title:      lock_guard和unique_lock区别
subtitle:   锁机制区别
date:       2019-09-12
author:     VK
catalog: true
tags:
    - c++
---

## lock_guard和unique_lock区别

lock_guard和unique_lock都是RAII机制下的锁，即依靠对象的创建和销毁也就是其生命周期来自动实现一些逻辑，而这两个对象就是在创建时自动加锁，在销毁时自动解锁。所以如果仅仅是依靠对象生命周期实现加解锁的话，两者是相同的，都可以用，因跟生命周期有关，所以有时会用花括号指定其生命周期。但lock_guard的功能仅限于此。unique_lock是对lock_guard的扩展，允许在生命周期内再调用lock和unlock来加解锁以切换锁的状态。
根据linux下条件变量的机制，condition_variable在wait成员函数内部会先调用参数unique_lock的unlock临时解锁，让出锁的拥有权（以让其它线程获得该锁使用权加锁，改变条件，解锁），然后自己等待notify信号，等到之后，再调用参数unique_lock的lock加锁，处理相关逻辑，最后unique_lock对象销毁时自动解锁。
也即是说condition_variable的wait函数内伪代码如下：

```c++
condition_variable::wait(std::unique_lockstd::mutex& lk){
      lk.unlock();
      waiting_signal();
      lk.lock();
}
```

