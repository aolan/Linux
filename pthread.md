
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
```


# 线程终止

三种方法

1. 在线程函数中return

2. 被同一进程中的另外的线程Cancel掉

3. 线程调用pthread_exit函数
