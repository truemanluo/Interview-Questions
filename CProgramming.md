### memcpy实现

- 函数原型

    > ```void * memcpy ( void * destination, const void * source, size_t num );``` 
    >
    > Copies the values of num bytes from the location 
    > pointed to by source directly to the memory block 
    > pointed to by destination.

- Example

```
/* memcpy example */
#include <stdio.h>
#include <string.h>

struct {
  char name[40];
  int age;
} person, person_copy;

int main ()
{
    char myname[] = "Pierre de Fermat";

    /* using memcpy to copy string: */
    memcpy(person.name, myname, strlen(myname)+1);
    person.age = 46;

    /* using memcpy to copy structure: */
    memcpy ( &person_copy, &person, sizeof(person) );

    printf ("person_copy: %s, %d \n", person_copy.name, person_copy.age );

    return 0;
}
```

- 实现

    - `memcpy`只能精确地拷贝`n`个字节，因此要拷贝的字节数由用户传入
    - 传入的是`void`指针，编译器不知道指向对象的确切大小，因此只能**逐字节拷贝**，将其转化为`char*`
    - 【gcc4.9中未考虑】考虑`src`和`dst`为空指针的情况
    - 【gcc4.9中未考虑】内存重叠的情况（目的地址与源地址有重叠）
    - 考虑以字为单位进行拷贝

    :star:我在实现中的错误：
    ```
    #include <iostream>
    using namespace std;

    void *memcpy(void *dst, const void *src, unsigned int n)
    {
        if (src == nullptr) {
            return dst; // 可以直接返回nullptr，表示拷贝失败
        }
        if (dst == nullptr) return nullptr; // 之前没有考虑边界条件
            
            
        for (int i = 0; i < n; ++i) { // i最好用unsigned表示，因为n是unsigned
            *((char*)dst+i) = *((char*)src+i); // 可以提前将```dst```转换成```char*```，```src```转换成```const char*```
        }
        return dst;
    }

    int main() {
        const int src[3] = {0, 1, 2}; // 这个地方不用const，传入时会进行转换
        int dst[3] = {0}; // 用new int[n]申请一个n大小的数组
        memcpy(dst, src, sizeof(dst)); // 第三个参数应该传入实际的字节数，而不是元素个数
        for (int i = 0; i < 3; ++i) {
            cout << *(dst+i) << endl;
        }
        return 0;
    }

    ```
    :star:gcc4.9标准实现：
    ```
    void *memcpy (void *dest, const void *src, size_t len)
    {
        char *d = dest;
        const char *s = src;
        while (len--)
            *d++ = *s++;
        return dest;
    }
    ```

