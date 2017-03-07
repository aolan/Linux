# gettimeofday 获取当前时间

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
}

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
