# ndk
ndk基础 c/c++


# c语言基础

## 创建C语言程序
xcode -> create new xcode project -> macos -> command line tools
在macos系统即可创建一个c语言程序

## 基本数据类型

int 整型，占4个字节
double 双精度小数 占8个字节
float 单精度小数 占4个字节
char 字符 占1个字节

```
int i = 2;
printf("i的值:%d,所占字节%d\n", i, sizeof(i));
double db = 1.34;
printf("db的值:%lf,所占字节%d\n", db, sizeof(db));
float fl = 1.12;
printf("fl的值:%f,所占字节%d\n", fl, sizeof(fl));
char ch = 'A';
printf("ch的值:%c,所占字节%d\n", ch, sizeof(ch));
   
```
打印结果：
i的值:2,所占字节4
db的值:1.340000,所占字节8
fl的值:1.120000,所占字节4
ch的值:A,所占字节1

## 内存地址

使用 & 变量 即可获取到变量的内存地址
```
int i = 2;
printf("i的值:%d,所占字节%d， 内存地址为%p\n", i, sizeof(i), &i);
```
i的值:2,所占字节4， 内存地址为0x7ffeefbff4c8

## 指针

&i 既是取出i的内存地址，也是i的指针。
或者换个说法：
```
int i = 10;
int * i_p = &i;
```
取出指针的值使用 *

* i_p 既可取出值 i = 10；

```
int i = 22;
int * i_p = &i;
printf("i 的值：%d, i_p指针指向的值: %d \n", i, *i_p);
printf("i的地址：%p, i_p的地址%p \n", i_p, &i_p);
```
打印：
    i 的值：22, i_p指针指向的值: 22 
    i的地址：0x7ffeefbff4c8, i_p的地址0x7ffeefbff4c0 
可以是 & 对指针再次取内存地址


## 数组

```
int a[5];
printf("a的值：%p， a的内存地址：%p, a[0]的地址%p\n", a, &a, &a[0]);
```
打印结果：a的值：0x7ffeefbff4b0， a的内存地址：0x7ffeefbff4b0, a[0]的地址0x7ffeefbff4b0

可以看到 打印 a、&a、&a[0]出现的结果都一样。

** 1、为何a与&a一样 **
因为a是数组，除基本数据类型外的其他变量，变量本身就是指向内存地址的。所以 a == &a
** 2、为何&a与&a[0]一样 **
因为a是数组，数组内存连续，所以数组下标为0的内存地址就是数组本身的内存地址。

```
int j = 0;
for (j = 0; j < 5; j ++) {
    printf("当前下标为%d,下标内存地址为%p\n", j, &a[j]);
}
```
打印结果如下：
    当前下标为0,下标内存地址为0x7ffeefbff4b0
    当前下标为1,下标内存地址为0x7ffeefbff4b4
    当前下标为2,下标内存地址为0x7ffeefbff4b8
    当前下标为3,下标内存地址为0x7ffeefbff4bc
    当前下标为4,下标内存地址为0x7ffeefbff4c0
可以看到内存地址以每次为4增加，因为这是int数组，int占4个字节，所以每次增加4

## 指针偏移

```
int * p = &a[j] + 1;
printf("p的值为:%p\n", p);
*(&a[j] + 0) = j;
```
&a[j] 是取出下标j处的地址。
&a[j] + 1 是指针在a[j]处偏移1位，既指向a[j + 1];
*(&a[j] + 0) = j; + 0 其实相当于未偏移，就是对a[j]赋值

## 函数指针
首先函数指针的申明格式：函数返回值类型 (* 指针变量名) (函数参数列表);
```
void add(int a, int b){
    printf("sum = %d\n", a+b);
}

void test(void (*method)(int, int), int a, int b) {
    method(a, b);
}

int main() {
    printf("Hello \n");
    test(add, 3, 4);
    return 0;
}
```
在test函数入参中，有一个函数指针，
在main函数中，调用test函数，并将add函数作为入参传递到test函数中。

## 多级指针

指向指针的指针就是多级指针

```
int main() {
    printf("HELLO WORLD!\n");
    
    int a = 0;
    int * b = &a;
    int **c = &b;
    int *** d = &c;
    printf("d的地址%p \n", d);
    
    return 0;
}
```

还可以用来表示多维数组


## 静态分配内存

在c语言中，内存区域分为栈区、堆区

函数被调用时，就会进栈，函数执行完毕则会出栈，而在函数中默认方式申明生成的数据都是系统静态分配内存的，
当函数执行完毕，出栈后，在该函数中静态分配的内存会被回收，系统会自己执行，无需手动处理。
静态分配内存都是在栈区

栈区总容量较小，大约是2M，各个系统、设备可能不一致，超出会抛出异常

## 动态分配内存

动态分配内存需要手动调用API才可，动态分配的内存不会自动回收，动态分配的内存都是在堆区。
堆区总容量较大，至少超过40M，各个系统、设备可能不一致， 超出也会抛出异常。

调用动态分配内存：malloc(int size)函数
调用回收动态内存：free(void *);

需要额外引入依赖文件：#include <stdlib.h>

```
#include <stdio.h>
#include <stdlib.h>

int main(){
    int num;
    printf("请输入数字\n");
    scanf("%d", &num);
    printf("num的值为：%d\n", num);
    
    int * arr = (int *)malloc(sizeof(int) * num);
    int i = 0;
    for (i = 0; i < num; i ++) {
        arr[i] = i + 10000;
    }
    for (i = 0; i < num; ++i) {
        printf("%d对应值为%d\n", i, arr[i]);
    }
    free(arr);
    return 0;
}
```

### 重新分配

就是将之前动态分配的内存重新分配，使用：realloc(void *__ptr, size_t __size);
第一个参数：原来的内存指针
第二个参数：新的内存大小

```
int * new_arr = (int *)realloc(arr, sizeof(int) * (num + new_num));
if(new_arr) {
    int j = num;
    for (; j < (num + new_num); j ++) {
        new_arr[j] = j + 100000;
    }
    int i = 0;
    for (; i < (num + new_num); i ++) {
        printf("新数组第%d个值为%d\n", i, new_arr[i]);
    }
    
    free(new_arr);
    new_arr = NULL;
}

```


** 题目 **
截取字符串中指定字符
char c = "ABCDEFGHIJK";

截取第3位到第6位

```
#include <stdio.h>


void substring(char * result, char *str, int start, int end) {
    for (int i = start; i <= end; ++i) {
        printf("char:%c\n", *(str + i));
        *(result++) = *(str + i);
    }
}


int main(){
    char * str = "ABCDEFGHIJK";
    char * result;
    substring(result, str, 3, 6);
    printf("result:%c\n",result);
    return 0;
}
```


## 结构体

申明关键字 struct

```
struct Person {
    
    char * name;
    int age;
    
};
```

类似与java中的bean类，kotlin中的data class

```
struct Person {
    char * name;
    int age;
}
;

int main() {
    printf("HELLO WORLD!\n");
    struct Person * person;
    person->name = "justin"; // xcode可以这样写，其他工具可能需要使用 strcpy 函数来对字符串复制来进行赋值
    person->age = 29;
    printf("name:%s,age:%d\n", person->name, person->age);
    return 0;
}
```

还可以直接在结构体后实例化对象

```
struct Person {
    char * name;
    int age;
} ppp1  = {"9999", 21}
;
```


## 类型定义
在不同的ide工具中，c语言的语法可能略有不同，为了统一，推出一个类型定义，
如此保证在不同的ide工具，使用一样的代码运行

在源码中也是大量使用

关键字 typedef

```
struct Person {
    char * name;
    int age;
} ppp1  = {"9999", 21}
;

typedef struct Person Person; // 为结构体定义一个类型，后续申明结构体对象就不需要使用struct关键字
typedef Person * Person_; // 为结构体指针定一个类型


int main() {
    printf("HELLO WORLD!\n");
    Person_ person;
    person->name = "justin";
    person->age = 29;
    printf("name:%s,age:%d\n", person->name, person->age);
    return 0;
}
```


# c++语言基础，
c++中能运行c语言，但c语言不能运行c++

C++语言面向对象 + 标准特性
C语言面向过程，函数+结构体
C++里面可以运行C语言，可以调用C语言，反之 就不行C语言无法运行C++
以后我们85%以上 都是 用C++去写功能

在c语言中基本运行需要引入 
` #include <stdio.h> `
在c++中需要引入
` #include <iostream> `

## 打印语句

* cout *
```
cout << "HELLO WORLD!" << endl;
    
cout << "YYYYYY\n";

cout << "Line1\n"
        << "Line2\n"
        << "line3\n";
```

需要引入
` using namespace std; `
namespace 命名空间

<< 是一个操作重载符，cout 更正确的写法是： std::cout 
因为我们已经引入 命名空间，所以可以省略为： cout

## 常量

在c或者c++中，常量使用 const 声明，但是在c语言中，常量是个伪命题，因为c中可以直接修改指针

```
const int i = 90;
int * p = &i; // c++ Cannot initialize a variable of type 'int *' with an rvalue of type 'const int *'
*p = 10;
```
i 已经被定义成常量
上面这段代码在c语言中可以运行，并能修改i地址处的值为10，达到了修改常量值的目的。
但是在c++中会报错，ide工具就会直接报错：Cannot initialize a variable of type 'int *' with an rvalue of type 'const int *'


## 引用

* & *

c++中 & 标示引用类型。

```
int i = 0;
int & a = i;
cout << "a地址:" << &a << "\ni地址:" << &i << endl;
int c = 9;
int d = 9;
cout << "c地址:" << &c << "\nd地址:" << &d << endl;
```
打印结果：
a地址:0x7ffeefbff4c8
i地址:0x7ffeefbff4c8
c地址:0x7ffeefbff4bc
d地址:0x7ffeefbff4b8

因为 a 是 i 的引用。 a 指向 i， 所以 a 与 i 的地址一致。
而c 与 d 不同，c、d都是一个新的对于9的引用。

## 类
c++ 申明类使用关键字 class
```
class Person {
private:
    int age;
    char * name;

public:
    void setAge(int age) {
        this->age = age;
    }
    void setName(char * name) {
        this->name = name;
    }
    
    int getAge(){
        return this->age;
    }
    char * getName(){
        return this->name;
    }
};
```
如此一个基本类就定义完成，成员变量使用private修饰，在外部不可访问，提供public修饰的set、get函数来操作成员变量。

### 构造函数

```
public:
    Person(){
        cout << "空构造函数" << endl;
    }
    Person(int age): age(age) { // 2
        cout << "一个参数的构造函数： age" << endl;
    }
    Person(char * name) { // 3
        this->name = name;
        cout << "一个参数的构造函数： name" << endl;
    }
    Person(int age, char * name){
        this->name = name;
        this->age = age;
        cout << "两个参数的构造函数： name" << endl;
    }
```
如上所示，第2个与第三个其实效果是一样的，都是对成员变量直接赋值。
还有构造函数的相互调用问题:
```
Person(int age, char * name): Person(age){
        this->name = name;
        cout << "两个参数的构造函数： name" << endl;
    }
```
调用两个参数的构造函数时，会调用到一个参数的构造函数，
* 而且打印会先打印一个参数的构造函数的日志，然后再打印两个参数构造函数的日志  *

### 类的引用
我们在上面申明了一个类，然后我们可以根据类申明引用

```
cout << "HELLO TESTCLASS" << endl;
Person person;
person.setAge(29);
person.setName("justin");
cout << "age:" << person.getAge() << ",  name:" << person.getName() << endl;

Person person1(24, "novia");
cout << "age:" << person1.getAge() << ",  name:" << person1.getName() << endl;
```
打印结果如下：
`
HELLO TESTCLASS
空构造函数
age:29,  name:justin
一个参数的构造函数： age
两个参数的构造函数： name
age:24,  name:novia
`

在上述代码中，生成的person都是普通的引用，数据内存都是在栈区。
接下来看一下在C++中如何使用堆区内存

### 堆内存
与c语言不同，c++中使用 new 生成的对象都存放在堆内存中，而存放在堆内存中的数据，需要自己手动
c++使用 delete 释放内存。

```
Person * person = new Person(29, "justin");
cout << "age:" << person->getAge() << ",  name:" << person->getName() << endl;

delete person;
person = NULL;
```

* QA: 为何栈区内存不需要释放内存
我们都知道函数在出栈时，会释放栈区内存，那为何不需要使用delete释放。
其实函数并不是没有调用delete来释放，在函数出栈时，有静默的调用了delete，来释放栈区的内存

* QA：为何调用了delete后，还能调用指针并获取到值
因为delete的原理并不是直接清空数据，而是标记该块内存地址为可用，当应用有向堆内存重新申请内存时，就可能申请到这块内存，此时数据才会被修改，
如果此块内存一直没有被申请到时，调用该指针后，还是能访问到之前的数据的。
当调用 delete 时，该指针即成为悬空指针，继续调用就有可能出现异常，规范用法时指向NULL；


### 析构函数
当对象内存被回收时，会执行对象的析构函数，类似与java对象的finalize函数
但与finalize函数不同的是，java对象可以在finalize中实现自我拯救，
c++析构函数中，不能实现自我拯救，主要是用于一些数据清除、销毁工作，如解绑、释放堆内存空间，是的，对象中如果也有开辟堆内存时，就需要在此处释放，不然该内存会被一直占用。
析构函数的格式：

```
~Person(){
    cout << "析构函数" << endl;
}
```

### 拷贝构造函数
```
// 拷贝构造函数
Person(const Person & person){
    this->name = person.name;
    this->age = person.age;
    cout << "拷贝构造函数" << endl;
}
```
当执行复制拷贝时，就会执行复制拷贝函数，系统默认的复制拷贝函数会自动完成复制值，
手动重写复制拷贝函数时，我们也需要手动赋值

此时传入的person就是旧对象，this即是拷贝后生成的新对象

#### 拷贝的场景

* person1 = person2

```
Person person1(24, "novia");
Person person2 = person1;
```
当执行 Person person2 = person1; 即会执行 拷贝构造函数 来对person2赋值，

* 参数传递

```
void test(Person person) {
    cout << "test函数中,参数person的地址: " << &person << endl;
}

int main() {  
    Person person1(24, "novia");
    Person person2 = person1;
    cout << "Person1.age:" << person1.getAge() << ",  Person1.name:" << person1.getName() << endl;
    cout << "Person2.age:" << person2.getAge() << ",  Person2.name:" << person2.getName() << endl;
    cout << "person1地址---" << &person1 << "  ,person2地址---" << &person2 << endl;
    test(person2);
    return 0;
}
```
打印结果：

一个参数的构造函数： age
两个参数的构造函数： name
拷贝构造函数
Person1.age:24,  Person1.name:novia
Person2.age:24,  Person2.name:novia
person1地址---0x7ffeefbff4b8  ,person2地址---0x7ffeefbff4a8
拷贝构造函数
test函数中,参数person的地址: 0x7ffeefbff488
析构函数
析构函数
析构函数

可以看到test函数中打印的person地址 与我们调用 test 函数传递的 person2 地址并不相同。这被叫做 * 行参 *

像避免上面的操作，避免产生大量的副本对象，占用内存，有几个办法，

* 1、使用引用
在上面就讲过引用，引用也是直接执行地址的，所以当我们使用引用时，就不会重新拷贝赋值了。
修改一下test函数的入参
```
void test(Person & person) {
    cout << "test函数中,参数person的地址: " << &person << endl;
}
```

* 2、运算符重载
重写 = 运算符，使之返回之前的地址。


* 注意事项 *
```
Person person2 = person1; // 1
Person person2;
person2 = person1; // 2
```
方式2是不会调用 拷贝构造函数， 方式1 才会。
为什么？

因为person2其实已经调用了无参构造函数，
Person person2; 这一句已经调用了无参构造函数，
person2 = person1; 只是重新赋值而已。


### 常量指针 常量引用 指针常量 常量指针常量

之前讲过常量，代表不可更改值

常量下还有 常量指针 常量引用 指针常量 常量指针常量

注意常量修饰的位置 
#### 常量指针

```
 int number = 10;
int number2 = 100;
const int * a_p  = &number;
*a_p = 100; // 修改值 会报错，
a_p = &number2; // 修改地址 不会报错
```
const 用来修饰类型，这个指针即为常量指针
声明了常量指针 a_p，
*a_p = 100; 会提示错误：Read-only variable is not assignable
常量代表只读,  常量指针 常量是修饰值的，不可以直接修改指针指向的数据，只能通过修改指针指向的地址来修改值

#### 常量引用
```
const int & a = 18;
```
const 用来修饰引用，这个引用即为 常量引用
常量引用指向的数据也是不能直接修改的。
拷贝构造函数 重的参数就是一个常量引用。

#### 指针常量
```
int * const b_p = &number;
//    b_p = &number2; // 会报错 Cannot assign to variable 'b_p' with const-qualified type 'int *const'
*b_p = 190; // 不报错
cout << "*bp指向的值为" << *b_p << endl;
```
const 用来修饰指针，这个指针即是 指针常量
上面的输出结果：
*bp指向的值为190

指针常量，不可修改指向的地址，但可以修改指针指向的值

#### 常量指针常量
常量指针常量 其实相当于 常量指针 与 指针常量 两个的合并。
```
const int * const c_p = &number;
c_p = &number2; // 报错
*c_p = 99; // 报错
```
常量指针常量 既不能修改指向的地址，也不能修改指针指向的数据，即完全只读
