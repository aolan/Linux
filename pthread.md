
# 常见方法

pthread_t用于表示Thread ID，具体内容根据实现的不同而不同，有可能是一个Structure，因此不能将其看作为整数

```c

// 用于比较两个pthread_t是否相等
int pthread_equal(pthread_t tid1, pthread_t tid2); 

// 用于获得本线程的thread id
pthread _t pthread_self(void);

/*
* 创建线程
* pthread_t *restrict tidp：返回最后创建出来的Thread的Thread ID
* const pthread_attr_t *restrict attr：指定线程的Attributes
* void *(*start_rtn)(void *)：指定线程函数指针，该函数返回一个void *，参数也为void*
* void *restrict arg：传入给线程函数的参数
* return 返回错误值，pthread函数在出错的时候不会设置errno，而是直接返回错误值
*/
int pthread_create(pthread_t *restrict tidp,
                    const pthread_attr_t *restrict attr, 
                    void *(*start_rtn)(void *), 
                    void *restrict arg);
                    
/*
* 线程通过调用pthread_exit函数终止执行，就如同进程在结束时调用exit函数一样
* 这个函数的作用是，终止调用它的线程并返回一个指向某个对象的指针
* 这个对象不能是在当前线程中的stack中创建的
*/               
void pthread_exit(void *rval_ptr);

/*
* pthread_join()函数，以阻塞的方式等待thread指定的线程结束
* 当函数返回时，被等待线程的资源被收回
* 如果线程已经结束，那么该函数会立即返回。并且thread指定的线程必须是joinable的
**/
int pthread_join(pthread_t thread, void **rval_ptr);

/*
* 该函数会使指定线程如同调用了pthread_exit(PTHREAD_CANCELLED)
* 不过，指定线程可以选择忽略或者进行自己的处理
* 该函数不会导致Block，只是发送Cancel这个请求
*/
void pthread_cancel(pthread_t tid);

/*
* 线程可以安排在它退出的时候，某些函数自动被调用
* 这两个函数维护一个函数指针的Stack，可以把函数指针和函数参数值push/pop。执行的顺序则是从栈顶到栈底，也就是和push的顺序相反
*/
void pthread_cleanup_push(void (*rtn)(void *), void *arg);
void pthread_cleanup_pop(int execute);



```


# 线程终止

三种方法

1. 在线程函数中return

2. 被同一进程中的另外的线程Cancel掉

3. 线程调用pthread_exit函数


# pthread_exit和pthread_join组合使用

1. 线程A调用pthread_join(B, &rval_ptr)，被阻塞，进入Detached状态（如果已经进入Detached状态，则pthread_join函数返回EINVAL）。如果对B的结束代码不感兴趣，rval_ptr可以传NULL。

2. 线程B调用pthread_exit(rval_ptr)，退出线程B，结束代码为rval_ptr。注意rval_ptr指向的内存的生命周期，不应该指向B的Stack中的数据。

3. 线程A恢复运行，pthread_join函数调用结束，线程B的结束代码被保存到rval_ptr参数中去。如果线程B被Cancel，那么rval_ptr的值就是PTHREAD_CANCELLED。


# pthread_cleanup_push和pthread_cleanup_pop

这两个函数维护一个函数指针的Stack，可以把函数指针和函数参数值push/pop。执行的顺序则是从栈顶到栈底，也就是和push的顺序相反。
在下面情况下pthread_cleanup_push所指定的thread cleanup handlers会被调用：

1. 调用pthread_exit

2. 相应cancel请求

3. 以非0参数调用pthread_cleanup_pop()。（如果以0调用pthread_cleanup_pop()，那么handler不会被调用

有一个比较怪异的要求是，由于这两个函数可能由宏的方式来实现，因此这两个函数的调用必须得是在同一个代码段之中，并且配对，因为在pthread_cleanup_push的实现中可能有一个{，而 pthread_cleanup_pop可能有一个}。因此，一般情况下，这两个函数是用于处理意外情况用的，举例如下：

```c
void *thread_func(void *arg)
{
    pthread_cleanup_push(cleanup, “handler”)
 
    // do something
 
    Pthread_cleanup_pop(0);
    return((void *)0)；
}
```

必须要注意的是，如果线程处于PTHREAD_CANCEL_ASYNCHRONOUS状态，上述代码段就有可能出错，因为CANCEL事件有可能在pthread_cleanup_push()和pthread_mutex_lock()之间发生，或者在pthread_mutex_unlock()和pthread_cleanup_pop()之间发生，从而导致清理函数unlock一个并没有加锁的mutex变量，造成错误。因此，在使用清理函数的时候，都应该暂时设置成PTHREAD_CANCEL_DEFERRED模式。为此，POSIX的Linux实现中还提供了一对不保证可移植的pthread_cleanup_push_defer_np()/pthread_cleanup_pop_defer_np()扩展函数，功能与以下

代码段相当：

```c
void *thread_func(void *arg)
{ 
    int oldtype;
    pthread_setcanceltype(PTHREAD_CANCEL_DEFERRED, &oldtype);
    pthread_cleanup_push(routine, arg);
 
    pthread_mutex_lock(&mut);
    // do something
    pthread_mutex_unlock(&mut);

    pthread_cleanup_pop(execute);
    pthread_setcanceltype(oldtype, NULL);
}
```

本来do something之后是有pthread_mutex_unlock(&mut)这句，也就是有解锁操作，但是在do something时会出现非正常终止，那样的话，系统会根据pthread_cleanup_push中提供的函数和参数进行解锁操作或者其他操作，以免造成死锁。

