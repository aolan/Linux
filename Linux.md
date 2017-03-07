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
* CLOCK_REALTIME: 系统实时时间,随系统实时时间改变而改变,即从UTC1970-1-1 0:0:0开始计时, 中间时刻如果系统时间被用户改成其他,则对应的时间相应改变.
* CLOCK_MONOTONIC: 从系统启动这一刻起开始计时,不受系统时间被用户改变的影响.
* CLOCK_PROCESS_CPUTIME_ID: 本进程到当前代码系统CPU花费的时间.
* CLOCK_THREAD_CPUTIME_ID: 本线程到当前代码系统CPU花费的时间.
*/
int clock_gettime(clockid_t __clock_id, struct timespec *__tp);

struct timespec{
   time_t tv_sec; // 秒
   long tv_nsec;  // 纳秒
};


```

1.CLOCK_REALTIME:

代表机器上可以理解为当前的我们所常看的时间，其当time-of-day 被修改的时候而改变，这包括NTP对它的修改（NTP:Network Time Protocol（NTP）是用来使计算机时间同步化的一种协议，它可以使计算机对其服务器或时钟源（如石英钟，GPS等等)做同步化，它可以提供高精准度的时间校正（LAN上与标准间差小于1毫秒，WAN上几十毫秒），且可介由加密确认的方式来防止恶毒的协议攻击。）

2.CLOCK_MONOTONIC:

代表从过去某个固定的时间点开始的绝对的逝去时间，它不受任何系统time-of-day时钟修改的影响，如果你想计算出在一台计算机上不受重启的影响，两个事件发生的间隔时间的话，那么它将是最好的选择。

eg:

```c

 代码
 #include <iostream>
 #include <time.h>
 using namespace std;
  
 timespec diff(timespec start, timespec end);
  
  int main()
 {
     timespec time1, time2;
     int temp;
     clock_gettime(CLOCK_PROCESS_CPUTIME_ID, &time1);
     for (int i = 0; i< 242000000; i++)
         temp+=temp;
     clock_gettime(CLOCK_PROCESS_CPUTIME_ID, &time2);
     cout<<diff(time1,time2).tv_sec<<":"<<diff(time1,time2).tv_nsec<<endl;
     return 0;
 }
  
 timespec diff(timespec start, timespec end)
 {
     timespec temp;
     if ((end.tv_nsec-start.tv_nsec)<0) {
         temp.tv_sec = end.tv_sec-start.tv_sec-1;
         temp.tv_nsec = 1000000000+end.tv_nsec-start.tv_nsec;
     } else {
         temp.tv_sec = end.tv_sec-start.tv_sec;
         temp.tv_nsec = end.tv_nsec-start.tv_nsec;
     }
     return temp;
 }

```
