## Bit Fields in Structures
- [C++: Bit-field](https://en.cppreference.com/w/cpp/language/bit_field)
  
  
    ```cpp=
    #include <iostream>
  
    struct S
    {
        // three-bit unsigned field, allowed values are 0...7
        unsigned int b : 3;
    };
  
    int main()
    {
        S s = {6};
  
        ++s.b; // store the value 7 in the bit-field
        std::cout << s.b << '\n';
  
        ++s.b; // the value 8 does not fit in this bit-field
        std::cout << s.b << '\n'; // formally implementation-defined, typically 0
    }
    ```
## Union

Union 和 structure 的差別在於：union 是 or，structure 是 and。在 Union 中，同時只能有一個 member 成立。

```cpp=
#include <iostream>
#include <cstdint>
union S
{
  std::int32_t n;     // occupies 4 bytes
  std::uint16_t s[2]; // occupies 4 bytes
  std::uint8_t c;     // occupies 1 byte
};                      // the whole union occupies 4 bytes

int main()
{
  S s = {0x12345678}; // initializes the first member, s.n is now the active member
  // at this point, reading from s.s or s.c is undefined behavior
  std::cout << std::hex << "s.n = " << s.n << '\n';
  s.s[0] = 0x0011; // s.s is now the active member
  // at this point, reading from n or c is UB but most compilers define it
  std::cout << "s.c is now " << +s.c << '\n' // 11 or 00, depending on platform
            << "s.n is now " << s.n << '\n'; // 12340011 or 00115678
}
```
## enum

每個 enum 都有一個 range，在 range 之內的 interger 都可以被 type cast 成 enum type（就算該 interger 並沒有在 enum 被 initialize 時被定義）。

range 的 upper bound 的定義為：比該 enum 最大元素大的最小的 2 的次方 - 1。 lower bound 為 0，不過若 enum 最小元素是負數，就先將其視為正數，用找 upper bound 方法去找到 bound，再加上負號，即為 lower bound。


```cpp=
#include <iostream>

using namespace std;

enum weekDay { Monday, Tuesday, Wednesday, Thursday, Friday };

int main()
{   
  weekDay Day;
  
  int number = Tuesday;
  cout << number << endl; // 1, enum type cast to int is valid
  number = Tuesday + 3; 
  cout << number << endl; // 4, same as above
  number = Thursday + Friday; 
  cout << number << endl; // 7
  
  Day = 4; // Invalid, int cannot be cast to enum type
  Day = Thursday + Friday; 
  // Invalid, since r-value is a int, int cannot be cast to a enum type
  
  Day = weekDay(3);
  cout << Day << endl; // 3
  Day = weekDay(10000);
  cout << Day << endl; // 10000, undefined behavior
  
}
```