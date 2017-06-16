
# gcc 编译选项


1. gcc -g : 生成调试信息

2. gcc -static : 进行静态链接，使程序不再依赖动态库。加上 -O3 参数，进行最快速度优化。加上-DNDEBUG参数，定义NDEBUG宏，屏蔽断言

3. gcc -m32: 生成32位的代码

4. gcc -m64: 生成64位的代码，当没有-m32或-m64参数时，一般情况下会生成跟操作系统位数一致的代码，但某些编译器存在例外，MinGW，总是编译为32位代码。因为MinGW只支持32位代码

5. gcc -c: 生成.o的目标文件

6. gcc -S: 只激活预处理和编译，就是指把文件编译成为汇编代码

7. gcc -E: 只激活预处理,这个不生成文件,你需要把它重定向到一个输出文件里

8. gcc -o: 制定目标名称,缺省的时候,gcc编译出来的文件是a.out

9. gcc -pipe: 使用管道代替编译中临时文件,在使用非gnu汇编工具的时候,可能有些问题

10. gcc -share: 此选项将尽量使用动态库，所以生成文件比较小，但是需要系统由动态库

11. gcc -O0: 编译器的优化选项的4个级别，-O0表示没有优化,-O1为缺省值，-O3优化级别最，还有-O2

12. gcc -fPIC: 作用于编译阶段，告诉编译器产生与位置无关代码(Position-Independent Code)

13. gcc -w: 关闭编译时的警告，也就是编译后不显示任何warning，因为有时在编译之后编译器会显示一些例如数据转换之类的警告，这些警告是我们平时可以忽略的

14. gcc -Wall: 编译后显示所有警告。

15. gcc -W: 类似-Wall，会显示警告，但是只显示编译器认为会出现错误的警告

16. gcc -std: 若没有使用-std参数来指定GCC的C标准, 则使用默认的GNUC90标准(GCC对C99和C11支持得不完整), GNUC90标准在C90标准的基础上进行了扩展

17. gcc -D_GNU_SOURCE: 编译器将会按照GNU标准进行编译

18. gcc -D: makefile中增加宏定义，如果的代码中有判断某个宏是否定义了#ifdef，那么用这条编译选项就可以添加进去

19. gcc -I: -I /home/hello/include表示将该目录作为第一个寻找头文件的目录，寻找顺序是/home/hello/include  -> /usr/include -> /usr/local/include

20. gcc -L: -L /home/hello/lib表示将该目录作为第一个寻找库文件的目录，寻找顺序是/home/hello/lib  -> /usr/lib -> /usr/local/lib

21. gcc -l: -l hello表示在上面的lib目录寻找libHello.so动态库文件，如果gcc编译选项中增加了-static，表示寻找libHello.a静态库文件
