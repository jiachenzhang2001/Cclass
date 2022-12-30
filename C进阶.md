# 一、指针

指针是什么？首先，它是一个值，这个值代表一个内存地址，因此指针相当于指向某个内存地址的路标。

字符`*`表示指针，通常跟在类型关键字的后面，表示指针指向的是什么类型的值。比如，`char*`表示一个指向字符的指针，`float*`表示一个指向 `float`类型的值的指针。

```plain
int* intPtr;
```
### 1、指针变量

对指针变量的声明与对普通变量的声明基本一样，唯一的不同就是必须在指针变量名字前放置星号

```c++
int *p;
```
int 是指针指向的变量类型，上述声明说明 p 是指向 int **类型对象**的指针变量。用术语**对象**来代替变量 ，是因为 p 可以指向**不属于变量的内存区域。**
**C 语言要求每个指针变量只能指向一种特定类型（引用类型 ）的对象：**

指针变量也可以指向另一个指针，即指向指针的指针（指向指针类型的指针）

### 2、*间接寻址运算符

`*`这个符号除了表示指针以外，还可以作为运算符，用来取出指针变量所指向的内存地址里面的值。 

为了获得对指针所指向对象的访问，可以使用* （间接寻址 ）运算符。如果 p 是指针，那么*p 表示 p 当前指向的对象。

### 3、&取地址运算符

`&`运算符用来取出一个变量所在的内存地址。 

为了找到变量的地址，可以使用& （取地址 ）运算符。如果 x 是变量，那么&x 就是 x 在内存中的地址。

`&`运算符与`*`运算符互为逆运算，下面的表达式总是成立。

```plain
int i = 5;

if (i == *(&i)) // 正确
```
### 4、指针变量的初始化

声明指针变量之后，编译器会为指针变量本身分配一个内存空间，但是这个内存空间里面的值是随机的，也就是说，指针变量指向的值是随机的。这时一定不能去读写指针变量指向的地址，因为那个地址是随机地址，很可能会导致严重后果。

```plain
int* p;
*p = 1; // 错误
```
上面的代码是错的，因为 `p`指向的那个地址是随机的，向这个随机地址里面写入 `1`，会导致意想不到的结果。
正确做法是指针变量声明后，必须先让它指向一个分配好的地址，然后再进行读写，这叫做指针变量的初始化。

```plain
int* p;
int i;

p = &i;
*p = 13;
```
上面示例中，`p`是指针变量，声明这个变量后，`p`会指向一个随机的内存地址。这时要将它指向一个已经分配好的内存地址，上例就是再声明一个整数变量 `i`，编译器会为 `i`分配内存地址，然后让 `p`指向 `i`的内存地址（`p = &i;`）。完成初始化之后，就可以对 `p`指向的内存地址进行赋值了（`*p = 13;`）。
为了防止读写未初始化的指针变量，可以养成习惯，将未初始化的指针变量设为 `NULL`。

```plain
int* p = NULL;
```
`NULL`在 C 语言中是一个常量，表示地址为 `0`的内存空间，这个地址是无法使用的，读写该地址会报错。 
### 5、指针的运算

指针本质上就是一个无符号整数，代表了内存地址。它可以进行运算，但是规则并不是整数运算的规则。

C 语言支持 3 种指针运算

* 指针加法：指针加上整数
* 指针减法：指针减去整数
* 指针相减：两个指针相减
**（1）指针与整数值的加减运算**

指针与整数值的运算，表示指针的移动。

```plain
short* j;
j = (short*)0x1234;
j = j + 1; // 0x1236
```
上面示例中，`j`是一个指针，指向内存地址 `0x1234`。你可能以为 `j + 1`等于 `0x1235`，但正确答案是 `0x1236`。原因是 `j + 1`表示指针向内存地址的高位移动一个单位，而一个单位的 `short`类型占据两个字节的宽度，所以相当于向高位移动两个字节。同样的，`j - 1`得到的结果是 `0x1232`。
*指针移动的单位，与指针指向的数据类型有关。数据类型占据多少个字节，每单位就移动多少个字节。*

**（2）指针与指针的减法**

相同类型的指针允许进行减法运算，返回它们之间的距离，即相隔多少个数据单位。

高位地址减去低位地址，返回的是正值；低位地址减去高位地址，返回的是负值。

这时，减法返回的值属于 `ptrdiff_t`类型，这是一个带符号的整数类型别名，具体类型根据系统不同而不同。这个类型的原型定义在头文件 `stddef.h`里面。

```plain
short* j1;
short* j2;

j1 = (short*)0x1234;
j2 = (short*)0x1236;

ptrdiff_t dist = j2 - j1;
printf("%td\n", dist); // 1
```
上面示例中，`j1`和 `j2`是两个指向 short 类型的指针，变量 `dist`是它们之间的距离，类型为 `ptrdiff_t`，值为 `1`，因为相差 2 个字节正好存放一个 short 类型的值。
**（3）指针与指针的比较运算**

指针之间的比较运算，比较的是各自的内存地址哪一个更大，返回值是整数 `1`（true）或 `0`（false）。 


---
# 二、函数

函数是一段可以重复执行的代码。它可以接受不同的参数，完成对应的操作。下面的例子就是一个函数。

```plain
int plus_one(int n) {
  return n + 1;
}
```
上面的代码声明了一个函数 `plus_one()`。
函数声明的语法有以下几点，需要注意。

（1）返回值类型。函数声明时，首先需要给出返回值的类型，上例是 `int`，表示函数 `plus_one()`返回一个整数。

（2）参数。函数名后面的圆括号里面，需要声明参数的类型和参数名，`plus_one(int n)`表示这个函数有一个整数参数 `n`。

（3）函数体。函数体要写在大括号里面，后面（即大括号外面）不需要加分号。大括号的起始位置，可以跟函数名在同一行，也可以另起一行，本书采用同一行的写法。

（4）`return`语句。`return`语句给出函数的返回值，程序运行到这一行，就会跳出函数体，结束函数的调用。如果函数没有返回值，可以省略 `return`语句，或者写成 `return;`。非 void 类型必须 return 返回值。

调用函数时，只要在函数名后面加上圆括号就可以了，实际的参数放在圆括号里面。 

### 一、函数定义和声明

函数定义格式：

```c++
返回类型 函数名（形式参数）{
  复合语句;
}
```
函数声明：
通常情况下函数的定义总是在调用点之上，但是这样会给程序的结构带来问题，如果函数的定义在调用点之后，那么主函数无法调用函数就会出错。为了避免调用问题，可以在调用之前就为函数声明，使编译器率先知道函数定义的存在。

```c++
返回类型 函数名(形式参数);
```
### 二、参数的传值引用

如果函数的参数是一个变量，那么调用时，传入的是这个变量的值的拷贝，而不是变量本身。因为传入函数的是变量的拷贝，而不是变量本身，拷贝的变化，影响不到原始变量。这就叫做“传值引用”。

所以，如果参数变量发生变化，最好把它作为返回值传出来。 

### 三、指针与函数

函数本身就是一段内存里面的代码，**C 语言允许通过指针获取函数**。

```plain
void print(int a) {
  printf("%d\n", a);
}
void (*print_ptr)(int) = &print;
```
上面示例中，变量 `print_ptr`是一个函数指针，它指向函数 `print()`的地址。函数 `print()`的地址可以用`&print`获得。注意，`(*print_ptr)`一定要写在圆括号里面，否则函数参数`(int)`的优先级高于`*`，整个式子就会变成 `void* print_ptr(int)`。
有了函数指针，通过它也可以调用函数。

```plain
(*print_ptr)(10);
// 等同于
print(10);
```
比较特殊的是，C 语言还规定，**函数名本身就是指向函数代码的指针，通过函数名就能获取函数地址。也就是说，**`print`**和**`&print`**是一回事。**
```plain
if (print == &print) // true
```
因此，上面代码的 `print_ptr`等同于 `print`。
```plain
void (*print_ptr)(int) = &print;
// 或
void (*print_ptr)(int) = print;
if (print_ptr == print) // true
```
所以，对于任意函数，都有五种调用函数的写法。
```plain
// 写法一
print(10)
// 写法二
(*print)(10)
// 写法三
(&print)(10)
// 写法四
(*print_ptr)(10)
// 写法五
print_ptr(10)
```
为了简洁易读，一般情况下，函数名前面都不加`*`和`&`。
这种特性的一个应用是**，如果一个函数的参数或返回值，也是一个函数，那么函数原型可以写成下面这样。**

```plain
int compute(int (*myfunc)(int), int, int);
```
上面示例可以清晰地表明，函数 `compute()`的第一个参数也是一个函数。 
不仅可以通过指针来调用函数，指针真正对于函数有重要作用的是**指针作为函数的参数。指针作为参数的两个作用**

* **指针作为参数传值会节省大量的内存空间**
* **指针作为参数可以改变指向变量的值（实际参数的值）**
指针作为函数的参数在 C 语言中非常常见//**scanf("%d",&i);** scanf()函数就接收一个指针作为参数。

指针作为参数更重要的不是运用指针变量存储的内存地址，而主要是通过指针变量的指向对象关系，来获取指向对象的值，因为实际函数的调用中，内存地址参与运算没有任何意义。

*指针高效的原因是在实际参数较大时，调用函数会需要大量的内存空间，变量的传递会浪费大量的时间和空间，而指针则会节省参数传递的时间和需要占用的内存空间。*

**指针作为函数参数的主要作用是改变实际参数的值**

**指针参数：max(array)&&min(array)**

```c++
#include<stdio.h>
#define N 10
void max_min(int a[],int n,int *max,int *min);
int main(){
	int arr[N],i,big,small;
	printf("请输入%d 个数字：",N);
	for(i=0;i<N;i++){
		scanf("%d",&arr[i]);
	}
	max_min(arr,N,&big,&small);
	printf("最大的数：%d\n",big);
	printf("最小的数：%d\n",small);
	return 0;
}
void max_min(int a[],int n,int *max,int *min){
	int i;
	*max=*min=a[0];
	//用数组的第一个元素初始化两个变量，以确定比较参照 
	for(i=0;i<n;i++){
		if(a[i]>*max){
			*max=a[i];
		}else if(a[i]<*min){
			*min=a[i];
		}
	}
}
```
### 四、exit()函数

`exit()`函数用来终止整个程序的运行。一旦执行到该函数，程序就会立即结束。该函数的原型定义在头文件 `stdlib.h`里面。

`exit()`可以向程序外部返回一个值，它的参数就是程序的返回值。一般来说，使用两个常量作为它的参数：`EXIT_SUCCESS`（相当于 0）表示程序运行成功，`EXIT_FAILURE`（相当于 1）表示程序异常中止。这两个常数也是定义在 `stdlib.h`里面。

```plain
// 程序运行成功
// 等同于 exit(0);
exit(EXIT_SUCCESS);

// 程序异常中止
// 等同于 exit(1);
exit(EXIT_FAILURE);
```
在 `main()`函数里面，`exit()`等价于使用 `return`语句。其他函数使用 `exit()`，就是终止整个程序的运行，没有其他作用。
C 语言还提供了一个 `atexit()`函数，用来登记 `exit()`执行时额外执行的函数，用来做一些退出程序时的收尾工作。该函数的原型也是定义在头文件 `stdlib.h`。

```plain
int atexit(void (*func)(void));
```
`atexit()`的参数是一个函数指针。注意，它的参数函数（下例的 `print`）不能接受参数，也不能有返回值。
```plain
void print(void) {
  printf("something wrong!\n");
}
atexit(print);
exit(EXIT_FAILURE);
```
上面示例中，`exit()`执行时会先自动调用 `atexit()`注册的 `print()`函数，然后再终止程序。 
### 五、函数说明符

C 语言提供了一些函数说明符，让函数用法更加明确。

#### 1、extern 说明符

对于多文件的项目，源码文件会用到其他文件声明的函数。这时，当前文件里面，需要给出外部函数的原型，并用 `extern`说明该函数的定义来自其他文件。

```plain
extern int foo(int arg1, char arg2);

int main(void) {
  int a = foo(2, 3);
  // ...
  return 0;
}
```
上面示例中，函数 `foo()`定义在其他文件，`extern`告诉编译器当前文件不包含该函数的定义。
由于函数原型默认就是 `extern`，这里不加 `extern`，效果是一样的。 

#### 2、static 说明符

默认情况下，每次调用函数时，函数的内部变量都会重新初始化，不会保留上一次运行的值。`static`说明符可以改变这种行为。

`static`用于函数内部声明变量时，表示该变量只需要初始化一次，不需要在每次调用时都进行初始化。也就是说，它的值在两次调用之间保持不变。 

```plain
#include <stdio.h>

void counter(void) {
  static int count = 1;  // 只初始化一次
  printf("%d\n", count);
  count++;
}
int main(void) {
  counter();  // 1
  counter();  // 2
  counter();  // 3
  counter();  // 4
}
```
上面示例中，函数 `counter()`的内部变量 `count`，使用 `static`说明符修饰，表明这个变量只初始化一次，以后每次调用时都会使用上一次的值，造成递增的效果。
**注意，**`static`**修饰的变量初始化时，只能赋值为常量，不能赋值为变量。**

```plain
int i = 3;
static int j = i; // 错误
```
上面示例中，`j`属于静态变量，初始化时不能赋值为另一个变量 `i`。
**另外，在块作用域中，**`static`**声明的变量有默认值**`0`**。**

```plain
static int foo;
// 等同于
static int foo = 0;
```
`static`**可以用来修饰函数本身。**
```plain
static int Twice(int num) {
  int result = num * 2;
  return(result);
}
```
上面示例中，`static`关键字表示该函数只能在当前文件里使用，如果没有这个关键字，其他文件也可以使用这个函数（通过声明函数原型）。
`static`**也可以用在参数里面，修饰参数数组。**

```plain
int sum_array(int a[static 3], int n) {
  // ...
}
```
上面示例中，`static`对程序行为不会有任何影响，只是用来告诉编译器，该数组长度至少为 3，某些情况下可以加快程序运行速度。另外，需要注意的是，对于多维数组的参数，`static`仅可用于第一维的说明。 
#### 3、const 说明符

**函数参数里面的**`const`**说明符，表示函数内部不得修改该参数变量。**

```plain
void f(int* p) {
  // ...
}
```
上面示例中，函数 `f()`的参数是一个指针 `p`，函数内部可能会改掉它所指向的值`*p`，从而影响到函数外部。
为了避免这种情况，可以在声明函数时，在指针参数前面加上 `const`说明符，告诉编译器，函数内部不能修改该参数所指向的值。

```plain
void f(const int* p) {
  *p = 0; // 该行报错
}
```
上面示例中，声明函数时，`const`指定不能修改指针 `p`指向的值，所以`*p = 0`就会报错。
但是上面这种写法，只限制修改 `p`所指向的值，而 `p`本身的地址是可以修改的。

```plain
void f(const int* p) {
  int x = 13;
  p = &x; // 允许修改
}
```
上面示例中，`p`本身是可以修改，`const`只限定`*p`不能修改。
如果想限制修改 `p`，可以把 `const`放在 `p`前面。

```plain
void f(int* const p) {
  int x = 13;
  p = &x; // 该行报错
}
```
如果想同时限制修改 `p`和`*p`，需要使用两个 `const`。
```plain
void f(const int* const p) {
  // ...
}
```
### 六、可变参数

有些函数的参数数量是不确定的，声明函数的时候，可以使用省略号`...`表示可变数量的参数。

```plain
int printf(const char* format, ...);
```
上面示例是 `printf()`函数的原型，除了第一个参数，其他参数的数量是可变的，与格式字符串里面的占位符数量有关。这时，就可以用`...`表示可变数量的参数。
注意，`...`符号必须放在参数序列的结尾，否则会报错。

头文件 `stdarg.h`定义了一些宏，可以操作可变参数。

（1）`va_list`：一个数据类型，用来定义一个可变参数对象。它必须在操作可变参数时，首先使用。

（2）`va_start`：一个函数，用来初始化可变参数对象。它接受两个参数，第一个参数是可变参数对象，第二个参数是原始函数里面，可变参数之前的那个参数，用来为可变参数定位。

（3）`va_arg`：一个函数，用来取出当前那个可变参数，每次调用后，内部指针就会指向下一个可变参数。它接受两个参数，第一个是可变参数对象，第二个是当前可变参数的类型。

（4）`va_end`：一个函数，用来清理可变参数对象。

下面是一个例子。

```plain
double average(int i, ...) {
  double total = 0;
  va_list ap;
  va_start(ap, i);
  for (int j = 1; j <= i; ++j) {
    total += va_arg(ap, double);
  }
  va_end(ap);
  return total / i;
}
```
上面示例中，`va_list ap`定义 `ap`为可变参数对象，`va_start(ap, i)`将参数 `i`后面的参数统一放入 `ap`，`va_arg(ap, double)`用来从 `ap`依次取出一个参数，并且指定该参数为 double 类型，`va_end(ap)`用来清理可变参数对象。 
### 七、作用域

任何一种编程中，作用域是程序中定义的变量所存在的区域，超过该区域变量就不能被访问。C 语言中有三个地方可以声明变量：

1. 在函数或块内部的**局部**变量 
2. 在所有函数外部的**全局**变量 
3. 在**形式**参数的函数参数定义中 
**全局变量与局部变量在内存中的区别**：

* 全局变量保存在内存的全局存储区中，占用静态的存储单元； 
* 局部变量保存在栈中，只有在所在函数被调用时才动态地为变量分配存储单元。 
## 初始化局部变量和全局变量

当局部变量被定义时，系统不会对其初始化，您必须自行对其初始化。定义全局变量时，系统会自动对其初始化，如下所示：

|数据类型|初始化默认值|
|:----|:----|
|int|0|
|char|'\0'|
|float|0|
|double|0|
|pointer|NULL|

正确地初始化变量是一个良好的编程习惯，否则有时候程序可能会产生意想不到的结果，因为未初始化的变量会导致一些在内存位置中已经可用的垃圾值。

**局部变量：**

在某个函数或块的内部声明的变量称为局部变量。它们只能被该函数或该代码块内部的语句使用。局部变量在函数外部是不可知的。

**全局变量：**

全局变量是定义在函数外部，通常是在程序的顶部。全局变量在整个程序生命周期内都是有效的，在任意的函数内部能访问全局变量。

全局变量可以被任何函数访问。也就是说，全局变量在声明后整个程序中都是可用的。

局部变量和全局变量的名称可以相同，但是在函数内，如果两个名字相同，会使用局部变量值，全局变量不会被使用。

**形式参数：**

函数的参数，形式参数，被当作该函数内的局部变量，如果与全局变量同名它们会优先使用。


---
# 三、数组

### 一、一维数组

数组是含有多个数据值的数据结构，并且每个数据值具有相同的数据类型。这些数据值称为元素，可以根据元素在数组中的位置把它们选出来。

```c++
int arr[10];//声明一个长度为 10 元素类型为 int 的数组
```
**1、数组的下标**
为了存取特定的数组元素，可以通过数组的下标获取数组元素，也可以在循环中对数组遍历

```c++
int arr[10];
arr[0]//数组的第一个元素
arr[9]//数组的最后一个元素
```
**2、数组的初始化**
与标准变量一样数组也可以进行初始化的操作，

* 数组初始化式：是用一个大括号括起来的常量数值列表来进行初始化
```c++
int arr[10]={1,2,3,4,5,6,7,8,9,10};
```
* 初始化列表比数组长度短是允许的，剩余的元素会赋值为 0，但初始化列表比数组长是错误的,初始化列表完全为空也是错误的，可以用 0 替代。
```c++
int arr[10]={1,2,3,4,5};
//arr=={1,2,3,4,5,0,0,0,0,0}
int arr[10]={1,2,3,4,5,6,7,8,9,10,11};//err
int arr[10]={}//err
int arr[10]={0}
```
如果数组元素中有一部分未确定值，可以指定初始化,未指定的元素将默认为 0
```c++
int arr[10]={[0]=1,[5]=6,[9]=10}
```
**3、数组的长度（sizeof）**
`sizeof`运算符会返回整个数组的字节长度。

```plain
int a[] = {22, 37, 3490};
int arrLen = sizeof(a); // 12
```
上面示例中，`sizeof`返回数组 `a`的字节长度是 `12`。
由于数组成员都是同一个类型，每个成员的字节长度都是一样的，所以数组整体的字节长度除以某个数组成员的字节长度，就可以得到数组的成员数量。

```plain
sizeof(a) / sizeof(a[0])
```
上面示例中，`sizeof(a)`是整个数组的字节长度，`sizeof(a[0])`是数组成员的字节长度，相除就是数组的成员数量。
注意，`sizeof`返回值的数据类型是 `size_t`，所以 `sizeof(a) / sizeof(a[0])`的数据类型也是 `size_t`。在 `printf()`里面的占位符，要用`%zd`或`%zu`。

```plain
int x[12];

printf("%zu\n", sizeof(x));     // 48
printf("%zu\n", sizeof(int));  // 4
printf("%zu\n", sizeof(x) / sizeof(int)); // 12
```
上面示例中，`sizeof(x) / sizeof(int)`就可以得到数组成员数量 `12`。 
### 二、多维数组

C 语言允许声明多个维度的数组，有多少个维度，就用多少个方括号，比如二维数组就使用两个方括号。

```plain
int board[10][10];
```
上面示例声明了一个二维数组，第一个维度有 10 个成员，第二个维度也有 10 个成员。
**多维数组可以理解成，上层维度的每个成员本身就是一个数组。比如上例中，第一个维度的每个成员本身就是一个有 10 个成员的数组，因此整个二维数组共有 100 个成员（10 x 10 = 100）。**

三维数组就使用三个方括号声明，以此类推。

```plain
int c[4][5][6];
```
引用二维数组的每个成员时，需要使用两个方括号，同时指定两个维度。
```plain
board[0][0] = 13;
board[9][9] = 13;
```
注意，`board[0][0]`不能写成 `board[0, 0]`，因为 `0, 0`是一个逗号表达式，返回第二个值，所以 `board[0, 0]`等同于 `board[0]`。
跟一维数组一样，多维数组每个维度的第一个成员也是从 `0`开始编号。

多维数组也可以使用大括号，一次性对所有成员赋值。

```plain
int a[2][5] = {
  {0, 1, 2, 3, 4},
  {5, 6, 7, 8, 9}
};
```
上面示例中，`a`是一个二维数组，这种赋值写法相当于将第一维的每个成员写成一个数组。这种写法不用为每个成员都赋值，缺少的成员会自动设置为 `0`。
多维数组也可以指定位置，进行初始化赋值。

```plain
int a[2][2] = {[0][0] = 1, [1][1] = 2};
```
上面示例中，指定了`[0][0]`和`[1][1]`位置的值，其他位置就自动设为 `0`。
不管数组有多少维度，在内存里面都是线性存储，`a[0][0]`的后面是 `a[0][1]`，`a[0][1]`的后面是 `a[1][0]`，以此类推。因此，多维数组也可以使用单层大括号赋值，下面的语句与上面的赋值语句是完全等同的。

```plain
int a[2][2] = {1, 0, 0, 2};
```
### 三、变长数组

数组声明的时候，数组长度除了使用常量，也可以使用变量。这叫做变长数组（variable-length array，简称 VLA）。

```plain
int n = x + y;
int arr[n];
```
上面示例中，数组 `arr`就是变长数组，因为它的长度取决于变量 `n`的值，编译器没法事先确定，只有运行时才能知道 `n`是多少。
变长数组的根本特征，就是数组长度只有运行时才能确定。它的好处是程序员不必在开发时，随意为数组指定一个估计的长度，程序可以在运行时为数组分配精确的长度。 

变长数组也可以用于多维数组。

```plain
int m = 4;
int n = 5;
int c[m][n];
```
### 四、常量数组

在数组前面声明一个 const 类型限定，就可以将数组变为常量数组，意味着程序不应该对数组进行修改操作。

### 五、指针和数组

C 语言的数组和指针的关系十分紧密，当指针指向数组元素时可以对指针进行算数运算（加减运算）通过这种运算我们可以用指针替代数组下标对数组进行处理

#### 数组指针的运算：

* 指针加法：指针加上整数
* 指针减法：指针减去整数
* 指针相减：两个指针相减
**指针加法：**

指针加上整数产生指向特定数组元素的指针，指针 p 加上整数 j 就是原先指向元素后的第 j 个元素。指针 p 指向数组元素 a[i]，那么 p+j 就指向 a[i+j]（前提是 i+j 不能超过数组的长度）。

```c++
int a[10],*p,*q,j=3;
p=&a[2];
q=p+j;
//*q==a[5]
```
**指针减法：**
指针的减法和指针的加法原理相同，不同的是加法是指针指向向下标大的一方向移动，减法就是指针指向下下标小的一方向移动

```c++
int a[10],*p,*q,j=3;
p=&a[8];
q=p-j;
//*q==a[5]
```
**指针相减：**
两个指针相减时，结果为指针之间的距离（用数组元素的个数来度量），p 指向 a[i]，q 指向 a[j]，那么 p-q=i-j。

```c++
p = &a[5]; 
q = &a[1];
i = p - q; /* i is 4 */ 
i = q - p; /* i is -4 */
```
**指针比较：**
可以用关系运算符（< 、<= 、> 和>= ）和判等运算符（== 和!= ）进行指针比较。只有在两个指针指向同一数组时，用关系运算符进 行的指针比较才有意义。比较的结果依赖于数组中两个元素的相对位 置。

```c++
p = &a[5];
q = &a[1];
//赋值后 p <= q 的值是 0，而 p >= q 的值是 1。
```
**指向复合常量的指针：**
指针指向由复合字面量创建的数组中的某个元素是合法 的。

```c++
int *p3=(int[]){1,2,3,4,5};
//*p==a[0] 指向数组的第一个元素
```
#### **指针处理数组：**

指针的算术运算允许通过对指针变量进行重复自增来访问数组的元素。

```c++
#define N 10 ...
int a[N], sum, *p; ...
sum = 0;
for (p = &a[0]; p < &a[N]; p++) sum += *p;
```
>for 语句中的条件 p < &a[N] 值得特别说明一下。**尽管元素 a[N] 不存在（数组 a 的下标从 0 到 N-1），但是对它使用取地址运算符是 合法的。**因为循环不会尝试检查 a[N] 的值，所以在上述方式下使用 a[N] 是非常安全的。执行循环体时 p 依次等于&a[0],&a[1], …,&a[N-1] ，但是当p 等于&a[N] 时，循环终止。
C 程序经常在处理数组元素的语句中组合* （间接寻址）运算符和 ++ 运算符。++自加运算符有前缀和后缀之分，不同位置与指针的结合有不同的操作

一个问题是：把值存入一个数组元素中，然后前进到下一个数组

数组的下标写法是：

```c++
a[i++]=j;
```
p 指针指向的写法是：
```c++
*p++=j;
```
![图片](data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAAtgAAAGJCAYAAACw6UTKAAAABGdBTUEAALGPC/xhBQAAACBjSFJNAAB6JgAAgIQAAPoAAACA6AAAdTAAAOpgAAA6mAAAF3CculE8AAAABmJLR0QA/wD/AP+gvaeTAAAACXBIWXMAAA7DAAAOwwHHb6hkAAAAB3RJTUUH5gweBgw1h3RoDwAAAAFvck5UAc+id5oAAIAASURBVHja7N1lVBvbAgXgHRJcCwUKdXd3d3d3d3dvb93dqVB3d3d3aKkghRb34pIQeT8ouYQEa1O7b39rda0yPpPMZM+Zc84IImNiFSAiIiIiIq3Q+d0bQERERET0X8KATURERESkRQzYRERERERaxIBNRERERKRFDNhERERERFrEgE1EREREpEUM2EREREREWsSATURERESkRQzYRERERERaxIBNRERERKRFDNhERERERFrEgE1EREREpEUM2EREREREWsSATURERESkRQzYRERERERaJPrRBViYmvzufSAiIiIi0prImNgfmp8l2EREREREWvTDJdgpfjTpE/0XpDzR4flARET099FWzQyWYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYP9iMpkMgQEB3z1vRqKioqBQKLS2rQH+/r/02BAR/Q7e3l8glUq1sqwvX7787t0hoj8AA/YvJhAIUL5sGYwZNSrTwJzWsaNHMGhAfwQHBWkcv9txF5o0bIhHDx9qZVt79+qJju3b4cP799jt6IioqKjfffiIiLRKLBaje9eu6Nq5k1aucbNnzsTUyZMgFot/964R0W/EgP2L6egkH3JTM1MIhUKVcW/fvMHxY8fSnbdHz14ICwtD/Xp1NZaS6Ip08erVSwgEAq1sq76+Pj57fUbRYsXg8vYtShUvhu3btv3uQ0hEpBUKhQJTJk2C68ePCAwIgEgk+uFlrlq9GocPHUKX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ERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRaJtLUgC1OT370vRH8Mng9ERET/v7QWsANDQn73vhD9EexsbHg+EBER/aXsbGx+eBmsIkJEREREpEUM2EREREREWsSATURERESkRQzYRERERERaxIBNRERERKRFDNhERERERFrEgE1EREREpEUM2EREREREWsSATURERESkRQzYRERERERaxIBNRERERKRFDNhERERERFrEgE1EREREpEUM2EREREREWsSATURERESkRQzYRERERERaxIBNRERERKRFDNhERERERFrEgE1EREREpEUM2EREREREWsSATURERESkRQzYRERERERaxIBNRERERKRFDNhERERERFrEgE1EREREpEUM2ER/CIdt27B+7TpER8dka77PXp9x//59KBSqw48eOQIfH1+VYQcPHMS9u3czXaabqxsunL+gcdzWLVtw4/qN79pHFxcXzJg2HQEBgVmeR6EAjhw+gsREcbrTzJ45C588Pn3XNqW2a8dObHdwQFxcPGJj47K8fSOHD4eHu4faOA93D6xbszbDbU8tNjYO/8yZo/YduH3rFlavXJXt78atm7cQFxevcVxCQiK2bd2KpCTpDx+3/wJfXz84OzlrbXnx8Qk4cfx4lj/7zFy+dDlbn/+G9euxedOmdD//9Li7uePxo8dq15ODBw6qnbeOO3fh8aPHWjtmRP8lot+9AUSU7Pq1ayhSpCiMjIyyNd/Xr18xbvRo3LxzBzlz5lQOr9+gIdq1aQ3H3XtQrnw5JCVJ4bhzBwYPHZbpMosVL44Vy5fj6ZMnWLx0KQSC5OFxcfHYvHEj+vUfgMZNmkBHR5CtbRUIBLh86SLmL1yoNs7X1w958uRRruvfeQBra2u0a9MaR44eg1VOK7V5T508gZKlSqFI0SI/9BmEhYXh9etXGDJ0GHY77sKL588xe85c5C+QP915QkJCcPHCBQwdNlxt3JMnT7Bxw3rUb9AAlSpXynT9Dx88wOlTp9C1e3eULVtWOTwhIRFXr17B+IkTs7U/hw4exJHDh1GlahWN+7rDwQGVK1dBterVfui4/cm2bd2KvHnz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WOjvrVMpk8nx4vlzFC1aVPmG2ZSX4WRVZi/C+fDhAxQKRYZVcDKT2YtNtC0sLAxTJ0/BilUroa+nr/EtvX8CH28f5M6T57sLXj55fFJ5y6W2vHF+g3Lly2f4PXr54iXKlC2bYamuQgHcu3sHtevUzfQ68eTxE1SrXj1bx8LHxxc2NjYatyGj60lGwsPCMWXyZKxYtRJ6unoZnhtEv5qdjQ0iY36sSh0DNpGWZTVgE/3NxGIJli1ZrKzmYGNrq9LrClF6pFIZlixaqCz5trS0UnkJFNHvxoBN9AdiwCYiIvp7aSNgs6IyEREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWCSJjYhU/uhALU5PfvR9ERERERFoRGRP7Q/OL/pQNIfqvsDA14flARET0l9JGwTGriBARERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDNhERERGRFjFgExERERFpEQM2EREREZEWMWATEREREWkRAzYRERERkRYxYBMRERERaREDthbcuX0bTq9fAwAOHzoIT0/P371J9JeaN3cu3r9798PLOXTwAI4dOYKkpKRszefn6wsPDw+14WfPnEFkZKTasHcuLr/1eGmLXC7HlEkT4frxY7bnffrkCRITElSGhYaG4tLFCyrDFAoFVq5YDolEopVtDg8Px9s3b7I1z/Zt23Dzxg0oFIosz/P+3Tt4e39Jd/yjhw9x7drVbC1TE4VCgRnTpsLLywtyuTzL812/fg1nz5zRuP5jR47Ay8sry8u6euUynJ2c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FrADQ0J+974Q/RHsbGx4PhAREf2l7GxsfngZrCJCRERERKRFDNhERERERFrEgE1EREREpEUM2EREREREWsSATURERESkRQzYRERERERaxIBNRERERKRFDNhERERERFrEgE1EREREpEUM2EREREREWsSATURERESkRQzYRERERERaxIBNRERERKRFDNhERERERFrEgE1EREREpEUM2EREREREWsSATURERESkRQzYRERERERaxIBNRERERKRFDNhERERERFrEgE1EREREpEUM2EREREREWsSATURERESkRQzYRERERERaxIBNRERERKRFDNha4PTaCQEBgQCAq1euIioy6ndvEv2ldu7YgZCQkB9ezpXLV/D40WMoFNmbLywsDBFfI9SG3793DxJJksqwBw8ewM/P/7cer1/tzu3buHvnTraPa2REJEJDQ9WGP3zwEAkJiSrDnj19Bi9PL61t82evz5DLs77B/v4BOHL4CBITxdlaz+tXrzMcf+b0abV9/R53bt/G0ydPs/0Z7N29B3Fx8WrDoyKjcPPGzSwvR6EAzp05q7b+Dx8+4Mb1G9neLh9vnwznefH8xQ8fMyL69RiwteD+/Xto3aI5voZ/hZ2dHerUqolzZ87+7s2iP5xCAbWAumvHTly9fOWHl33wwH6sWrEc0VHZu9n75PEJ9evWQVhYmMpwIyNjdGzfThn+FQpgxdJl2LPb8TcfxV/rxPHjWLZ0KcLCQrM1X2BQIOrUrKn2eVvltEKHdm3h6+unHLZ+3Vps27o120EtRWREJO7fv49VK1aiU4cOqFWjOrZu2Zzl+ZMkEsyaMR0B/uo3T1KpLN353r17h9EjR6YbzBcvWoT169Z+306lWc/kiRMQGREBx527sHnTJo3BWWWfkqSYPWsmzp45ozbu+fPn6Nu7F25cv5HF9btgzOhRuHP7lsrwsNBQTJk0EaGh2btBXr1qJcaMGontDg5q/1atWIl2bVrj+rXrP3zciOjXEv3uDfgv0NPTQ6XKVWBpZQlLK0tMnT4D165dRcPGjWFmZvq7N4/+UH5+fqhbqyb27NuHho0aAQBEIiEqVKz4Q8uNjo5BUFAQzpw9BzNzM5VxCgUgEKQ/r76+PgoVKoycOXOqTF+lahVY57TG+bPnMGTYUFy5fBmNGjfGpCmTM13mf0Viohiurq44evw4rK2tVcZldgz0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)


#### 数组名作为指针：

指针的算数运算是指针和数组之间一种相互关联的方法，但并不是唯一的关系

另一种指针和数组的关系是：

**可以用数组的名字作为指向数组第一个元素的指针**

这种关系能够简化指针的算术运算，也能够使指针和数组更加通用

```c++
int a[10];
*a=0;
//*a==&a[0]
```
用 a 作为指向数组第一个元素的指针，可以修改 a[0] ：
通常情况下，a + i 等同于&a[i] （两者都表示指向数组 a 中元素 i 的指针），并且*(a+i) 等价于 a[i] （两者都表示元素 i 本 身）。换句话说，可以把数组的取下标操作看成是指针算术运算的一 种形式。

虽然可以把数组名用作指针，但是不能给数组名赋新的 值。试图使数组名指向

**数组型的实际参数：**

数组名作为参数在传递给函数时，总是被视为指针

```c++
int largest(int arr[],int n){
	int i,max;
	max=arr[0];
	for(i=0;i<n;i++){
		if(max<arr[i]){
			max=arr[i];
		}
	}
	return max;
} 
```
在这个函数的调用中，数组作为参数并没有全部复制，而数组名作为指针指向参数数组的第一个元素，这
数组型形式参数看作为指针会产生重要的影响

在给函数传递普通变量时，变量的值会被复制，任何对相应的形 式参数的改变都不会影响到变量。反之，因为没有对数组本身进 行复制，所以作为实际参数的数组是可能被改变的。

```c++
void arr_one(int a[],int n){
    int i;
    for(i=0;i<n;i++){
      a[i]=1;
    }
}
//这里作为实际参数的数组被改变
```
### 六、数组的复制

由于数组名是指针，所以复制数组不能简单地复制数组名。

```plain
int* a;
int b[3] = {1, 2, 3};

a = b;
```
上面的写法，结果不是将数组 `b`复制给数组 `a`，而是让 `a`和 `b`指向同一个数组。
**复制数组最简单的方法，还是使用循环，将数组元素逐个进行复制。**

```plain
for (i = 0; i < N; i++)
  a[i] = b[i];
```
上面示例中，通过将数组 `b`的成员逐个复制给数组 `a`，从而实现数组的赋值。
**另一种方法是使用**`memcpy()`**函数（定义在头文件**`string.h`**），直接把数组所在的那一段内存，再复制一份。**

```plain
memcpy(a, b, sizeof(b));
```
上面示例中，将数组 `b`所在的那段内存，复制给数组 `a`。这种方法要比循环复制数组成员要快。 
### 七、数组作为参数

#### 声明参数数组   

数组作为函数的参数，一般会同时传入数组名和数组长度。

```plain
int sum_array(int a[], int n) {
  // ...
}

int a[] = {3, 5, 7, 3};
int sum = sum_array(a, 4);
```
上面示例中，函数 `sum_array()`的第一个参数是数组本身，也就是数组名，第二个参数是数组长度。
**由于数组名就是一个指针，如果只传数组名，那么函数只知道数组开始的地址，不知道结束的地址，所以才需要把数组长度也一起传入。**

如果函数的参数是多维数组，那么除了第一维的长度可以当作参数传入函数，其他维的长度需要写入函数的定义。

```plain
int sum_array(int a[][4], int n) {
  // ...
}

int a[2][4] = {
  {1, 2, 3, 4},
  {8, 9, 10, 11}
};
int sum = sum_array(a, 2);
```
上面示例中，函数 `sum_array()`的参数是一个二维数组。第一个参数是数组本身（`a[][4]`），这时可以不写第一维的长度，因为它作为第二个参数，会传入函数，但是一定要写第二维的长度 `4`。
这是因为函数内部拿到的，只是数组的起始地址 `a`，以及第一维的成员数量 `2`。如果要正确计算数组的结束地址，还必须知道第一维每个成员的字节长度。写成 `int a[][4]`，编译器就知道了，第一维每个成员本身也是一个数组，里面包含了 4 个整数，所以每个成员的字节长度就是 `4 * sizeof(int)`。

#### 变长数组作为参数   

变长数组作为函数参数时，写法略有不同。

```plain
int sum_array(int n, int a[n]) {
  // ...
}

int a[] = {3, 5, 7, 3};
int sum = sum_array(4, a);
```
***上面示例中，数组***`a[n]`***是一个变长数组，它的长度取决于变量***`n`***的值，只有运行时才能知道。所以，变量***`n`***作为参数时，顺序一定要在变长数组前面，这样运行时才能确定数组***`a[n]`***的长度，否则就会报错。***
因为函数原型可以省略参数名，所以变长数组的原型中，可以使用`*`代替变量名，也可以省略变量名。

```plain
int sum_array(int, int [*]);
int sum_array(int, int []);
```
上面两种变长函数的原型写法，都是合法的。
变长数组作为函数参数有一个好处，就是多维数组的参数声明，可以把后面的维度省掉了。

```plain
// 原来的写法
int sum_array(int a[][4], int n);

// 变长数组的写法
int sum_array(int n, int m, int a[n][m]);
```
上面示例中，函数 `sum_array()`的参数是一个多维数组，按照原来的写法，一定要声明第二维的长度。但是使用变长数组的写法，就不用声明第二维长度了，因为它可以作为参数传入函数。
#### 数组字面量作为参数   

C 语言允许将数组字面量作为参数，传入函数。

```plain
// 数组变量作为参数
int a[] = {2, 3, 4, 5};
int sum = sum_array(a, 4);

// 数组字面量作为参数
int sum = sum_array((int []){2, 3, 4, 5}, 4);
```
上面示例中，两种写法是等价的。第二种写法省掉了数组变量的声明，直接将数组字面量传入函数。`{2, 3, 4, 5}`是数组值的字面量，`(int [])`类似于强制的类型转换，告诉编译器怎么理解这组值。 

---
# 四、字符串

### 1、字符串

C 语言没有单独的字符串类型，字符串被当作字符数组，即 `char`类型的数组。比如，字符串“Hello”是当作数组`{'H', 'e', 'l', 'l', 'o'}`处理的。 

编译器会给数组分配一段连续内存，所有字符储存在相邻的内存单元之中。在字符串结尾，C 语言会自动添加一个全是二进制 `0`的字节，写作`\0`字符，表示字符串结束。字符`\0`不同于字符 `0`，前者的 ASCII 码是 0（二进制形式 `00000000`），后者的 ASCII 码是 48（二进制形式 `00110000`）。所以，字符串“Hello”实际储存的数组是`{'H', 'e', 'l', 'l', 'o', '\0'}`。

所有字符串的最后一个字符，都是`\0`。这样做的好处是，C 语言不需要知道字符串的长度，就可以读取内存里面的字符串，只要发现有一个字符是`\0`，那么就知道字符串结束了。 

字符串写成数组的形式，是非常麻烦的。C 语言提供了一种简写法，双引号之中的字符，会被自动视为字符数组。

```plain
{'H', 'e', 'l', 'l', 'o', '\0'}

// 等价于
"Hello"
```
上面两种字符串的写法是等价的，内部存储方式都是一样的。双引号里面的字符串，不用自己添加结尾字符`\0`，C 语言会自动添加。
注意，双引号里面是字符串，单引号里面是字符，两者不能互换。如果把 `Hello`放在单引号里面，编译器会报错。

```plain
// 报错
'Hello'
```
*另一方面，即使双引号里面只有一个字符（比如*`"a"`*），也依然被处理成字符串（存储为 2 个字节），而不是字符*`'a'`*（存储为 1 个字节）。*
如果字符串内部包含双引号，则该双引号需要使用反斜杠转义。

```plain
"She replied, \"It does.\""
```
反斜杠还可以表示其他特殊字符，比如换行符（`\n`）、制表符（`\t`）等。
```plain
"Hello, world!\n"
```
如果字符串过长，可以在需要折行的地方，使用反斜杠（`\`）结尾，将一行拆成多行。
```plain
"hello \
world"
```
上面这种写法有一个缺点，就是第二行必须顶格书写，如果想包含缩进，那么缩进也会被计入字符串。为了解决这个问题，**C 语言允许合并多个字符串字面量，只要这些字符串之间没有间隔，或者只有空格，C 语言会将它们自动合并。**
```plain
char greeting[50] = "Hello, ""how are you ""today!";
// 等同于
char greeting[50] = "Hello, how are you today!";
```
这种新写法支持多行字符串的合并。
```plain
char greeting[50] = "Hello, "
  "how are you "
  "today!";
```
`printf()`**使用占位符**`%s`**输出字符串。**
```plain
printf("%s\n", "hello world")
```
### 2、字符串变量

**字符串变量可以声明成一个字符数组，也可以声明成一个指针，指向字符数组。**

```plain
// 写法一
char s[14] = "Hello, world!";

// 写法二
char* s = "Hello, world!";
```
上面两种写法都声明了一个字符串变量 `s`。如果采用第一种写法，由于字符数组的长度可以让编译器自动计算，所以声明时可以省略字符数组的长度。
```plain
char s[] = "Hello, world!";
```
上面示例中，编译器会将数组 `s`的长度指定为 14，正好容纳后面的字符串。
**字符数组的长度，可以大于字符串的实际长度。**

```plain
char s[50] = "hello";
```
上面示例中，字符数组 `s`的长度是 `50`，但是字符串“hello”的实际长度只有 6（包含结尾符号`\0`），所以后面空出来的 44 个位置，都会被初始化为`\0`。
**字符数组的长度，不能小于字符串的实际长度。**

```plain
char s[5] = "hello";//err
```
上面示例中，字符串数组 `s`的长度是 `5`，小于字符串“hello”的实际长度 6，这时编译器会报错。因为如果只将前 5 个字符写入，而省略最后的结尾符号`\0`，这很可能导致后面的字符串相关代码出错。
字符指针和字符数组，这两种声明字符串变量的写法基本是等价的，但是有两个差异。

**第一个差异是，指针指向的字符串，在 C 语言内部被当作常量，不能修改字符串本身。**

```plain
char* s = "Hello, world!";
s[0] = 'z'; // 错误
```
如果使用数组声明字符串变量，就没有这个问题，可以修改数组的任意成员。
```plain
char s[] = "Hello, world!";
s[0] = 'z';
```
为什么字符串声明为指针时不能修改，声明为数组时就可以修改？原因是系统会将字符串的字面量保存在内存的常量区，这个区是不允许用户修改的。声明为指针时，指针变量存储的值是一个指向常量区的内存地址，因此用户不能通过这个地址去修改常量区。*但是，声明为数组时，编译器会给数组单独分配一段内存，字符串字面量会被编译器解释成字符数组，逐个字符写入这段新分配的内存之中，而这段新内存是允许修改的。*
**为了提醒用户，字符串声明为指针后不得修改，可以在声明时使用**`const`**说明符，保证该字符串是只读的。**

```plain
const char* s = "Hello, world!";
```
上面字符串声明为指针时，使用了 `const`说明符，就保证了该字符串无法修改。一旦修改，编译器肯定会报错。
**第二个差异是，指针变量可以指向其它字符串。**

```plain
char* s = "hello";
s = "world";
```
字符指针可以指向另一个字符串。
但是，字符数组变量不能指向另一个字符串。

```plain
char s[] = "hello";
s = "world"; // 报错
```
上面示例中，字符数组的数组名，总是指向初始化时的字符串地址，不能修改。
同样的原因，声明字符数组后，不能直接用字符串赋值。

```plain
char s[10];
s = "abc"; // 错误
```
上面示例中，不能直接把字符串赋值给字符数组变量，会报错。原因是字符数组的变量名，跟所指向的数组是绑定的，不能指向另一个地址。
为什么数组变量不能赋值为另一个数组？原因是数组变量所在的地址无法改变，或者说，编译器一旦为数组变量分配地址后，这个地址就绑定这个数组变量了，这种绑定关系是不变的。C 语言也因此规定，数组变量是一个不可修改的左值，即不能用赋值运算符为它重新赋值。

**想要重新赋值，必须使用 C 语言原生提供的**`strcpy()`**函数，通过字符串拷贝完成赋值。这样做以后，数组变量的地址还是不变的，即**`strcpy()`**只是在原地址写入新的字符串，而不是让数组变量指向新的地址。**

```plain
char s[10];
strcpy(s, "abc");
```
### 3、字符库函数

#### 1）strlen()函数

`strlen()`函数返回字符串的字节长度，不包括末尾的空字符`\0`。 

它的参数是字符串变量，返回的是 `size_t`类型的无符号整数，除非是极长的字符串，一般情况下当作 `int`类型处理即可。 

`strlen()`的原型在标准库的 `string.h`文件中定义，使用时需要加载头文件 `string.h`。 

注意，字符串长度（`strlen()`）与字符串变量长度（`sizeof()`），是两个不同的概念。 

```plain
char s[50] = "hello";
printf("%d\n", strlen(s));  // 5
printf("%d\n", sizeof(s));  // 50
```
#### 2）strcpy()函数

字符串的复制，不能使用赋值运算符，直接将一个字符串赋值给字符数组变量。

```plain
char str1[10];
char str2[10];
str1 = "abc"; // 报错
str2 = str1;  // 报错
```
上面两种字符串的复制写法，都是错的。因为数组的变量名是一个固定的地址，不能修改，使其指向另一个地址。
如果是字符指针，赋值运算符（`=`）只是将一个指针的地址复制给另一个指针，而不是复制字符串。

```plain
char* s1;
char* s2;
s1 = "abc";
s2 = s1;
```
 **C 语言提供了**`strcpy()`**函数，用于将一个字符串的内容复制到另一个字符串，相当于字符串赋值。该函数的原型定义在**`string.h`**头文件里面。**
```plain
strcpy(char dest[], const char source[])
```
 `strcpy()`接受两个参数，第一个参数是目的字符串数组，第二个参数是源字符串数组。复制字符串之前，必须要保证第一个参数的长度不小于第二个参数，否则虽然不会报错，但会溢出第一个字符串变量的边界，发生难以预料的结果。第二个参数的 `const`说明符，表示这个函数不会修改第二个字符串。 
`strcpy()`也可以用于字符数组的赋值。

```plain
char str[10];
strcpy(str, "abcd");
```
 `strcpy()`的返回值是一个字符串指针（即 `char*`），指向第一个参数。 
`strcpy()`返回值的另一个用途，是连续为多个字符数组赋值。

```plain
strcpy(str1, strcpy(str2, "abcd"));
```
上面示例调用两次 `strcpy()`，完成两个字符串变量的赋值。
`strcpy()`的第一个参数最好是一个已经声明的数组，而不是声明后没有进行初始化的字符指针。

```plain
char* str;
strcpy(str, "hello world"); // 错误
```
上面的代码是有问题的。`strcpy()`将字符串分配给指针变量 `str`，但是 `str`并没有进行初始化，指向的是一个随机的位置，因此字符串可能被复制到任意地方。
如果不用 `strcpy()`，自己实现字符串的拷贝，可以用下面的代码。

```plain
char* strcpy(char* dest, const char* source) {
  char* ptr = dest;
  while (*dest++ = *source++);
  return ptr;
}
int main(void) {
  char str[25];
  strcpy(str, "hello world");
  printf("%s\n", str);
  return 0;
}
```
*上面代码中，关键的一行是*`while (*dest++ = *source++)`*，这是一个循环，依次将*`source`*的每个字符赋值给*`dest`*，然后移向下一个位置，直到遇到*`\0`*，循环判断条件不再为真，从而跳出循环。其中，*`*dest++`*这个表达式等同于*`*(dest++)`*，即先返回*`dest`*这个地址，再进行自增运算移向下一个位置，而*`*dest`*可以对当前位置赋值。*
`strcpy()`*函数有安全风险，因为它并不检查目标字符串的长度，是否足够容纳源字符串的副本，可能导致写入溢出。***如果不能保证不会发生溢出，建议使用**`strncpy()`**函数代替。**

#### 3）strncpy()函数

`strncpy()`跟 `strcpy()`的用法完全一样，只是多了第 3 个参数，用来指定复制的最大字符数，防止溢出目标字符串变量的边界。

```plain
char* strncpy(
  char* dest, 
  char* src, 
  size_t n
);
```
上面原型中，第三个参数 `n`定义了复制的最大字符数。如果达到最大字符数以后，源字符串仍然没有复制完，就会停止复制，这时目的字符串结尾将没有终止符`\0`，这一点务必注意。如果源字符串的字符数小于 `n`，则 `strncpy()`的行为与 `strcpy()`完全一致。 
`strncpy()`也可以用来拷贝部分字符串。

```plain
char s1[40];
char s2[12] = "hello world";

strncpy(s1, s2, 5);
s1[5] = '\0';

printf("%s\n", s1); // hello
```
上示例中，指定只拷贝前 5 个字符。 
#### 4）strcat()函数

`strcat()`函数用于连接字符串。它接受两个字符串作为参数，把第二个字符串的副本添加到第一个字符串的末尾。这个函数会改变第一个字符串，但是第二个字符串不变。

该函数的原型定义在 `string.h`头文件里面。

```plain
char* strcat(char* s1, const char* s2);
```
`strcat()`的返回值是一个字符串指针，指向第一个参数。
```plain
char s1[12] = "hello";
char s2[6] = "world";

strcat(s1, s2);
puts(s1); // "helloworld"
```
上面示例中，调用 `strcat()`以后，可以看到字符串 `s1`的值变了。
注意，`strcat()`的第一个参数的长度，必须足以容纳添加第二个参数字符串。否则，拼接后的字符串会溢出第一个字符串的边界，写入相邻的内存单元，这是很危险的，建议使用下面的 `strncat()`代替。 

#### 5）strncat()函数

`strncat()`用于连接两个字符串，用法与 `strcat()`完全一致，只是增加了第三个参数，指定最大添加的字符数。在添加过程中，一旦达到指定的字符数，或者在源字符串中遇到空字符`\0`，就不再添加了。它的原型定义在 `string.h`头文件里面。

```plain
char* strncat(
  const char* dest,
  const char* src,
  size_t n
);
```
`strncat()`返回第一个参数，即目标字符串指针。
为了保证连接后的字符串，不超过目标字符串的长度，`strncat()`通常会写成下面这样。

```plain
strncat(
  str1, 
  str2, 
  sizeof(str1) - strlen(str1) - 1
);
```
`strncat()`总是会在拼接结果的结尾，自动添加空字符`\0`，所以第三个参数的最大值，应该是 `str1`的变量长度减去 `str1`的字符串长度，再减去 `1`。下面是一个用法实例。
```plain
char s1[10] = "Monday";
char s2[8] = "Tuesday";

strncat(s1, s2, 3);
puts(s1); // "MondayTue"
```
上面示例中，`s1`的变量长度是 10，字符长度是 6，两者相减后再减去 1，得到 `3`，表明 `s1`最多可以再添加三个字符，所以得到的结果是 `MondayTue`。 
#### 6）strcmp()函数

如果要比较两个字符串，无法直接比较，只能一个个字符进行比较，C 语言提供了 `strcmp()`函数。

`strcmp()`函数用于比较两个字符串的内容。该函数的原型如下，定义在 `string.h`头文件里面。

```plain
int strcmp(const char* s1, const char* s2);
```
按照字典顺序，如果两个字符串相同，返回值为 `0`；如果 `s1`小于 `s2`，`strcmp()`返回值小于 0；如果 `s1`大于 `s2`，返回值大于 0。
下面是一个用法示例。

```plain
// s1 = Happy New Year
// s2 = Happy New Year
// s3 = Happy Holidays

strcmp(s1, s2) // 0
strcmp(s1, s3) // 大于 0
strcmp(s3, s1) // 小于 0
```
注意，`strcmp()`只用来比较字符串，不用来比较字符。因为字符就是小整数，直接用相等运算符（`==`）就能比较。所以，不要把字符类型（`char`）的值，放入 `strcmp()`当作参数。 
#### 7）strncmp()函数

由于 `strcmp()`比较的是整个字符串，C 语言又提供了 `strncmp()`函数，只比较到指定的位置。

该函数增加了第三个参数，指定了比较的字符数。它的原型定义在 `string.h`头文件里面。

```plain
int strncmp(
  const char* s1,
  const char* s2, 
  size_t n
);
```
它的返回值与 `strcmp()`一样。如果两个字符串相同，返回值为 `0`；如果 `s1`小于 `s2`，`strcmp()`返回值小于 0；如果 `s1`大于 `s2`，返回值大于 0。
下面是一个例子。

```plain
char s1[12] = "hello world";
char s2[12] = "hello C";

if (strncmp(s1, s2, 5) == 0) {
  printf("They all have hello.\n");
}
```
上面示例只比较两个字符串的前 5 个字符。 
#### 8）sprintf()、snprintf()函数

`sprintf()`函数跟 `printf()`类似，但是用于将数据写入字符串，而不是输出到显示器。该函数的原型定义在 `stdio.h`头文件里面。

```plain
int sprintf(char* s, const char* format, ...);
```
`sprintf()`的第一个参数是字符串指针变量，其余参数和 `printf()`相同，即第二个参数是格式字符串，后面的参数是待写入的变量列表。
```plain
char first[6] = "hello";
char last[6] = "world";
char s[40];

sprintf(s, "%s %s", first, last);

printf("%s\n", s); // hello world
```
上面示例中，`sprintf()`将输出内容组合成“hello world”，然后放入了变量 `s`。
`sprintf()`的返回值是写入变量的字符数量（不计入尾部的空字符`\0`）。如果遇到错误，返回负值。

`sprintf()`有严重的安全风险，如果写入的字符串过长，超过了目标字符串的长度，`sprintf()`依然会将其写入，导致发生溢出。为了控制写入的字符串的长度，C 语言又提供了另一个函数 `snprintf()`。

`snprintf()`只比 `sprintf()`多了一个参数 `n`，用来控制写入变量的字符串不超过 `n - 1`个字符，剩下一个位置写入空字符`\0`。下面是它的原型。

```plain
int snprintf(char*s, size_t n, const char* format, ...);
```
`snprintf()`总是会自动写入字符串结尾的空字符。如果你尝试写入的字符数超过指定的最大字符数，`snprintf()`会写入 n - 1 个字符，留出最后一个位置写入空字符。
下面是一个例子。

```plain
snprintf(s, 12, "%s %s", "hello", "world");
```
上面的例子中，`snprintf()`的第二个参数是 12，表示写入字符串的最大长度不超过 12（包括尾部的空字符）。
`snprintf()`的返回值是写入格式字符串的字符数量（不计入尾部的空字符`\0`）。如果 `n`足够大，返回值应该小于 `n`，但是有时候格式字符串的长度可能大于 `n`，那么这时返回值会大于 `n`，但实际上真正写入变量的还是 `n-1`个字符。如果遇到错误，返回一个负值。因此，返回值只有在非负并且小于 `n`时，才能确认完整的格式字符串写入了变量。 

### 4、字符串数组

如果一个数组的每个成员都是一个字符串，需要通过二维的字符数组实现。每个字符串本身是一个字符数组，多个字符串再组成一个数组。

```plain
char weekdays[7][10] = {
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday",
  "Sunday"
};
```
上面示例就是一个字符串数组，一共包含 7 个字符串，所以第一维的长度是 7。其中，最长的字符串的长度是 10（含结尾的终止符`\0`），所以第二维的长度统一设为 10。
因为第一维的长度，编译器可以自动计算，所以可以省略。

```plain
char weekdays[][10] = {
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday",
  "Sunday"
};
```
上面示例中，二维数组第一维的长度，可以由编译器根据后面的赋值，自动计算，所以可以不写。
数组的第二维，长度统一定为 10，有点浪费空间，因为大多数成员的长度都小于 10。解决方法就是把数组的第二维，从字符数组改成字符指针。

```plain
char* weekdays[] = {
  "Monday",
  "Tuesday",
  "Wednesday",
  "Thursday",
  "Friday",
  "Saturday",
  "Sunday"
};
```
上面的字符串数组，其实是一个一维数组，成员就是 7 个字符指针，每个指针指向一个字符串（字符数组）。
遍历字符串数组的写法如下。

```plain
for (int i = 0; i < 7; i++) {
  printf("%s\n", weekdays[i]);
}
```

