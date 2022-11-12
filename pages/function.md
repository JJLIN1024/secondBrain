- 不懂：p335, p336 結尾，關於 two-level indirection, pointer-to-pointer const non-const
- 不懂：p335, p336 結尾，關於 two-level indirection, pointer-to-pointer const non-const
- The return value of a function cannot be an array. Everything else is possible.
- Interestingly, even though a C++ function can’t return an array directly, it can return an array that’s part of a structure or object.
- C++ 的原則是 pass-by-value（記憶體的位置也是一個 value）
- 在 C++ 中規定 program 若要使用 function，必須提供 function prototype，而 C 不強制。
## function prototype
- 為何要有 function prototype？
	- 提升 compile 效率
	- function prototype 即所謂的 static type checking，可以檢查使用者在 call function 時是否傳入了具有正確型別、數量的 argument，以及在合理的範圍內對 argument 做 type casting（例如：將傳入的 int 轉成 function prototype 定義的 double）
- 在 function 內的 variable(包括 parameters)，對於 function 來說是 private 的，其佔據的記憶體空間會跟著 function 一起被建立以及消滅。這些 variable 也稱為 automatic variable，因為使用者不需要負責其 allocation & deallocation。
## function with 1D array
- `int arr[]` & `int *arr` 只有在 function header 中才是代表相同的意思，即 `arr` 的第一個 element 的 address 的 pointer(a pointer-to-int)。
- 只傳遞 array 的第一個 element 的 address 的好處在於節省 program 使用的記憶體空間。
	- 這也是為何要將 array 的 size 用第二個 parameter 傳遞，因為 `sizeof arr` 並不是整個 array 的 size，而是第一個 element 的 size，以 pointer-to-int 為例，就是 4-byte（假設在此 machine 上 a interger pointer 佔用 4 byte）
- Pointer 相關
	- Declare 一個 const data 是在告訴 compiler：請保證 data 不會被改變
	- Declare 一個 pointer to const  是在告訴 compiler：請保證 pointer 指向的 data 不會被改變，但這並不保證 pointer 本身為 const。
	- [pointer-to-const can point to non-const object - language design or technical reason?](https://stackoverflow.com/questions/13061450/pointer-to-const-can-point-to-non-const-object-language-design-or-technical-re)
	  
	      ```cpp=
	      int a = 3;
	      const int *a_ptr_1 = &a;
	      int * const a_ptr_2 = &a;
	      ```
	      > const: a_ptr_2, *a_ptr_1 
	      > non-const: *a_ptr_2, a_ptr_1
	  
	  ![](https://i.imgur.com/L4csi6Z.png)
- C++ 禁止將一個 const data 的 address assign 給一個非 const 的 pointer，由上可知，是因為 compiler 無法保證非 const pointer 不會被改變（進而改變指向的 const data）
- 可在 argument 之前加上 keyword `const`，例如 `printArray(const int arr[], int size)`，如此一來，`arr` 這個 array 在 `printArray` 這個 function 中就會被視為 read-only。
	- pointer-to-const，用來保護傳入的 data
	-
## Functions and 2D array

```cpp=
int data[3][4] = {{1,2,3,4}, {9,8,7,6}, {2,4,6,8}};
int total = sum(data, 3);

//function prototype
int sum(int (*ar2)[4], int size);
int sum(int ar2[][4], int size);
// same meaning
// Key: *ar2 or ar2[] 都表示指向 data 的第一個 element 的 pointer

ar2[r][c] == *(*(ar2 + r) + c) // same thing
  
```
## Functions with strings

其實都是傳 string 的第一個字的 address，另外，string 和 array of char 的差別在於 string 有內建 terminating character(`'\0'`)。
```c=
char ghost[15] = "galloping";
char * str = "galumphing";
int n1 = strlen(ghost); // ghost is &ghost[0]
int n2 = strlen(str); // pointer to char
int n3 = strlen("gamboling"); // address of string
```
## Functions with structure
可以直接傳 structure 的name，不像 array會是 address pointer。

```c=
#include <iostream>
using namespace std;

struct cube {
int x;
int y;
};

cube addCube(cube, cube);
int main() {
cube one = {
  .x = 2, .y = 3
};
cube two = {
  .x = 6, .y = 8
};
cube combine = addCube(one, two);
cout << combine.x << " " << combine.y << endl;
// 8 11
return 0;
}


cube addCube(cube one, cube two) {
cube temp; 
temp.x = one.x + two.x;
temp.y = one.y + two.y;
return temp;
}
```
若要省時省空間，傳 address 的話：

Because the formal parameter is a pointer instead of a structure, use the indirect membership operator (->) rather than the membership operator (dot).

```c=
#include <iostream>
using namespace std;

struct cube {
int x;
int y;
};

cube addCube(const cube *, const cube *);
int main() {
cube one = {
  .x = 2, .y = 3
};
cube two = {
  .x = 6, .y = 8
};
cube combine = addCube(&one, &two);
cout << combine.x << " " << combine.y << endl;
return 0;
}


cube addCube(const cube *one, const cube *two) {
cube temp; 
temp.x = one->x + two->x;
temp.y = one->y + two->y;
return temp;
}

```
## Function Pointer

```c=
double (*pf)(int); // pf points to a function that returns double
double *pf(int); // pf() a function that returns a pointer-to-double

double pam(int);
double (*pf)(int);
pf = pam; // pf now points to the pam() function

double x = pam(4); // call pam() using the function name
double y = (*pf)(5); // call pam() using the pointer pf
double y = pf(5); // also call pam() using the pointer pf
```

```c=
const double * f1(const double ar[], int n);
const double * f2(const double [], int);
const double * f3(const double *, int);
// f1 f2 f3 are the same
// TIP: when defining function pointer, simply replace the function name(f1) with (*pt) will do.
const double * (*p1)(const double *, int) = f1;

// an array of function pointers
const double * (*pa[3])(const double *, int) = {f1,f2,f3};

auto pb = pa;
const double * px = pa[0](av,3);
const double * py = (*pb[1])(av,3);
double x = *pa[0](av,3);
double y = *(*pb[1])(av,3);

*pd[3] // an array of 3 pointers
(*pd)[3] // a pointer to an array of 3 elements
const double *(*(*pd)[3])(const double *, int) = &pa;


**&pa == *pa == pa[0]

```

The name of a C++ function acts as the address of the function. By using a function argument that is a pointer to a function, you can pass to a function the name of a second function that you want the first function to evoke.
## C++ specific functions

ex: inline functions, pass by reference
## Inline functions

In an inline function, the compiled code is “in line” with the other code in the program.That is, the compiler replaces the function call with the corresponding function code.With inline code, the program **doesn’t have to jump to another location to execute the code and then jump back**. Inline functions thus run a little **faster** than regular functions, but they come **with a memory penalty**.
## Inline functions v.s. Macros

```cpp=
#define SQUARE(X) ((X)*(X))

inline double square(double x) {
  return x*x;
}
```

Macros 是純粹的字替換，所以像是：
```c=
c = 3;
d = SQUARE(c++); // d = c++ * c++
e = square(c++); // e = 4 * 4
```
不同點在於 square 只做加法一次，做完 pass by value 給 function 去算平方，而 Macros 加法做兩次，接著做平方（乘法）。

> The intent here is not to show you how to write C macros. Rather, it is to suggest that if you have been using C macros to perform function-like services, you should consider converting them to C++ inline functions.
## Reference in C++

```cpp=
int rats = 101;
int & rodents = rats; // rodents a reference
int * prats = &rats; // prats a pointer

// rodents, *prats 都代表 rats
// &rodents, prats 都代表 &rats
```

A reference is rather like a const pointer

```cpp=
#include <iostream>
using namespace std;

int main(){
int num = 2;
int *numptr = &num;
int &num2 = *numptr;
int num3 = 30;
numptr = &num3;
cout << num3 << " " << num2 << " " << num << endl;
return 0;
} 
```
num2(reference to num) 不會因為原本的指標改變了就改變，還是指向原本的num，A reference is rather like a const pointer 的概念。
## pass by reference vs pass by value

```cpp=
// pass by value(pointer)
int n1 = 1;
int n2 = 2;

void swap(int *n1, int *n2) {
  int temp = *n1;
  *n1 = *n2;
  *n2 = temp;
}
swap(&n1, &n2); // pass the address

//pass by reference
void swap(int &n1, int &n2) {
  int temp = n1;
  n1 = n2;
  n2 = temp;
}
swap(n1, n2);

```

Reference arguments become useful with larger data units, such as structures and classes,
## Temporary Variables, Reference Arguments, and const
- The return value of a function cannot be an array. Everything else is possible.
- Interestingly, even though a C++ function can’t return an array directly, it can return an array that’s part of a structure or object.
- C++ 的原則是 pass-by-value（記憶體的位置也是一個 value）
- 在 C++ 中規定 program 若要使用 function，必須提供 function prototype，而 C 不強制。
## function prototype
- 為何要有 function prototype？
	- 提升 compile 效率
	- function prototype 即所謂的 static type checking，可以檢查使用者在 call function 時是否傳入了具有正確型別、數量的 argument，以及在合理的範圍內對 argument 做 type casting（例如：將傳入的 int 轉成 function prototype 定義的 double）
- 在 function 內的 variable(包括 parameters)，對於 function 來說是 private 的，其佔據的記憶體空間會跟著 function 一起被建立以及消滅。這些 variable 也稱為 automatic variable，因為使用者不需要負責其 allocation & deallocation。
## function with 1D array
- `int arr[]` & `int *arr` 只有在 function header 中才是代表相同的意思，即 `arr` 的第一個 element 的 address 的 pointer(a pointer-to-int)。
- 只傳遞 array 的第一個 element 的 address 的好處在於節省 program 使用的記憶體空間。
	- 這也是為何要將 array 的 size 用第二個 parameter 傳遞，因為 `sizeof arr` 並不是整個 array 的 size，而是第一個 element 的 size，以 pointer-to-int 為例，就是 4-byte（假設在此 machine 上 a interger pointer 佔用 4 byte）
- Pointer 相關
	- Declare 一個 const data 是在告訴 compiler：請保證 data 不會被改變
	- Declare 一個 pointer to const  是在告訴 compiler：請保證 pointer 指向的 data 不會被改變，但這並不保證 pointer 本身為 const。
	- [pointer-to-const can point to non-const object - language design or technical reason?](https://stackoverflow.com/questions/13061450/pointer-to-const-can-point-to-non-const-object-language-design-or-technical-re)
	  
	      ```cpp=
	      int a = 3;
	      const int *a_ptr_1 = &a;
	      int * const a_ptr_2 = &a;
	      ```
	      > const: a_ptr_2, *a_ptr_1 
	      > non-const: *a_ptr_2, a_ptr_1
	  
	  ![](https://i.imgur.com/L4csi6Z.png)
- C++ 禁止將一個 const data 的 address assign 給一個非 const 的 pointer，由上可知，是因為 compiler 無法保證非 const pointer 不會被改變（進而改變指向的 const data）
- 可在 argument 之前加上 keyword `const`，例如 `printArray(const int arr[], int size)`，如此一來，`arr` 這個 array 在 `printArray` 這個 function 中就會被視為 read-only。
	- pointer-to-const，用來保護傳入的 data
	-
## Functions and 2D array

```cpp=
int data[3][4] = {{1,2,3,4}, {9,8,7,6}, {2,4,6,8}};
int total = sum(data, 3);

//function prototype
int sum(int (*ar2)[4], int size);
int sum(int ar2[][4], int size);
// same meaning
// Key: *ar2 or ar2[] 都表示指向 data 的第一個 element 的 pointer

ar2[r][c] == *(*(ar2 + r) + c) // same thing
  
```
## Functions with strings

其實都是傳 string 的第一個字的 address，另外，string 和 array of char 的差別在於 string 有內建 terminating character(`'\0'`)。
```c=
char ghost[15] = "galloping";
char * str = "galumphing";
int n1 = strlen(ghost); // ghost is &ghost[0]
int n2 = strlen(str); // pointer to char
int n3 = strlen("gamboling"); // address of string
```
## Functions with structure
可以直接傳 structure 的name，不像 array會是 address pointer。

```c=
#include <iostream>
using namespace std;

struct cube {
int x;
int y;
};

cube addCube(cube, cube);
int main() {
cube one = {
  .x = 2, .y = 3
};
cube two = {
  .x = 6, .y = 8
};
cube combine = addCube(one, two);
cout << combine.x << " " << combine.y << endl;
// 8 11
return 0;
}


cube addCube(cube one, cube two) {
cube temp; 
temp.x = one.x + two.x;
temp.y = one.y + two.y;
return temp;
}
```
若要省時省空間，傳 address 的話：

Because the formal parameter is a pointer instead of a structure, use the indirect membership operator (->) rather than the membership operator (dot).

```c=
#include <iostream>
using namespace std;

struct cube {
int x;
int y;
};

cube addCube(const cube *, const cube *);
int main() {
cube one = {
  .x = 2, .y = 3
};
cube two = {
  .x = 6, .y = 8
};
cube combine = addCube(&one, &two);
cout << combine.x << " " << combine.y << endl;
return 0;
}


cube addCube(const cube *one, const cube *two) {
cube temp; 
temp.x = one->x + two->x;
temp.y = one->y + two->y;
return temp;
}

```
## Function Pointer

```c=
double (*pf)(int); // pf points to a function that returns double
double *pf(int); // pf() a function that returns a pointer-to-double

double pam(int);
double (*pf)(int);
pf = pam; // pf now points to the pam() function

double x = pam(4); // call pam() using the function name
double y = (*pf)(5); // call pam() using the pointer pf
double y = pf(5); // also call pam() using the pointer pf
```

```c=
const double * f1(const double ar[], int n);
const double * f2(const double [], int);
const double * f3(const double *, int);
// f1 f2 f3 are the same
// TIP: when defining function pointer, simply replace the function name(f1) with (*pt) will do.
const double * (*p1)(const double *, int) = f1;

// an array of function pointers
const double * (*pa[3])(const double *, int) = {f1,f2,f3};

auto pb = pa;
const double * px = pa[0](av,3);
const double * py = (*pb[1])(av,3);
double x = *pa[0](av,3);
double y = *(*pb[1])(av,3);

*pd[3] // an array of 3 pointers
(*pd)[3] // a pointer to an array of 3 elements
const double *(*(*pd)[3])(const double *, int) = &pa;


**&pa == *pa == pa[0]

```

The name of a C++ function acts as the address of the function. By using a function argument that is a pointer to a function, you can pass to a function the name of a second function that you want the first function to evoke.
## C++ specific functions

ex: inline functions, pass by reference
## Inline functions

In an inline function, the compiled code is “in line” with the other code in the program.That is, the compiler replaces the function call with the corresponding function code.With inline code, the program **doesn’t have to jump to another location to execute the code and then jump back**. Inline functions thus run a little **faster** than regular functions, but they come **with a memory penalty**.
## Inline functions v.s. Macros

```cpp=
#define SQUARE(X) ((X)*(X))

inline double square(double x) {
  return x*x;
}
```

Macros 是純粹的字替換，所以像是：
```c=
c = 3;
d = SQUARE(c++); // d = c++ * c++
e = square(c++); // e = 4 * 4
```
不同點在於 square 只做加法一次，做完 pass by value 給 function 去算平方，而 Macros 加法做兩次，接著做平方（乘法）。

> The intent here is not to show you how to write C macros. Rather, it is to suggest that if you have been using C macros to perform function-like services, you should consider converting them to C++ inline functions.
## Reference in C++

```cpp=
int rats = 101;
int & rodents = rats; // rodents a reference
int * prats = &rats; // prats a pointer

// rodents, *prats 都代表 rats
// &rodents, prats 都代表 &rats
```

A reference is rather like a const pointer

```cpp=
#include <iostream>
using namespace std;

int main(){
int num = 2;
int *numptr = &num;
int &num2 = *numptr;
int num3 = 30;
numptr = &num3;
cout << num3 << " " << num2 << " " << num << endl;
return 0;
} 
```
num2(reference to num) 不會因為原本的指標改變了就改變，還是指向原本的num，A reference is rather like a const pointer 的概念。
## pass by reference vs pass by value

```cpp=
// pass by value(pointer)
int n1 = 1;
int n2 = 2;

void swap(int *n1, int *n2) {
  int temp = *n1;
  *n1 = *n2;
  *n2 = temp;
}
swap(&n1, &n2); // pass the address

//pass by reference
void swap(int &n1, int &n2) {
  int temp = n1;
  n1 = n2;
  n2 = temp;
}
swap(n1, n2);

```

Reference arguments become useful with larger data units, such as structures and classes,
## Temporary Variables, Reference Arguments, and const