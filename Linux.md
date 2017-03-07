# gettimeofday 

获取当前时间，可以精确到微秒，但是可能会出现时间跳变

```c
#include <sys/time.h>

/*
* gettimeofday函数获取当前时间存于tv结构体中，相应的时区信息则存于tz结构体中
* 需要注意的是tz是依赖于系统，不同的系统可能存在获取不到的可能，因此通常设置为NULL 
*/
int gettimeofday(struct timeval *tv, struct timezone *tz);

struct timeval{
  time_t tv_sec;
  suseconds tv_usec;
};
  
struct timezone{
  int tz_minuteswest; 
  int tz_dsttime;
};

```

eg:

```c
#include <stdio.h>
#include <string.h>
#include <sys/time.h>

int main()
{
    struct timeval tv; 
    
    gettimeofday(&tv, NULL);

    printf("tv_sec: %d\n", tv.tv_sec);
    printf("tv_usec: %d\n", tv.tv_usec);
    
    return 0;
}

```

# time 

获取当前时间，可以精确到微秒，但是可能会出现时间跳变

# clock_gettime

获取当前时间，精确到纳秒

```c

/*
* clockid_t时钟类型:
* CLOCK_REALTIME:标准POSIX实时时钟
* CLOCK_MONOTONIC: POSIX时钟,以恒定速率运行;不会复位和调整,它的取值和CLOCK_REALTIME是一样的.
* CLOCK_PROCESS_CPUTIME_ID和CLOCK_THREAD_CPUTIME_ID是CPU中的硬件计时器中实现的.
*/
int clock_gettime(clockid_t __clock_id, struct timespec *__tp);

struct timespec{
   time_t tv_sec; // 秒
   long tv_nsec;  // 纳秒
};



```
