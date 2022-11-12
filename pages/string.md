## C-style string

string 和 array of char 的差別在於最後面是否有 terminating null character `\0`。
```cpp=
char a[] = "abcde"; // is a string
char b[] = {'a', 'b', 'c', 'd', 'e'}; // not a string!

char c = 'a'; // Valid!
char c = "A"; // Invalid! "A" is a string, which has terminating null character, so it cannot be assing to a char type
```
## C++ string class

將 C-style string 看成是一串 char 的集合，將 C++ string 看成一個 object。和 C-style string 相關的操作會牽涉到搬運記憶體，但 string class 幫你處理好這些，所以不需要 `strcpy`、`strcat`、`strlen` 這些 function，直接使用 =、+ 、.size() 就行了。

```cpp=
// strtype3.cpp -- more string class features
#include <iostream>
#include <string> // make string class available
#include <cstring> // C-style string library
int main()
{
using namespace std;
char charr1[20];
char charr2[20] = "jaguar";
string str1;
string str2 = "panther";
// assignment for string objects and character arrays
str1 = str2; // copy str2 to str1
strcpy(charr1, charr2); // copy charr2 to charr1
// appending for string objects and character arrays
str1 += " paste"; // add paste to end of str1
strcat(charr1, " juice"); // add juice to end of charr1
// finding the length of a string object and a C-style string
int len1 = str1.size(); // obtain length of str1
int len2 = strlen(charr1); // obtain length of charr1
cout << "The string " << str1 << " contains "
<< len1 << " characters.\n";
cout << "The string " << charr1 << " contains "
<< len2 << " characters.\n";
return 0;
}
```

另外一個差別是，uninitialize 的 `char charr[10]` 和 `string str` 的初始 size 會不一樣。對於 `char charr[10]` 來說，`strlen(charr)` 可能會 return 一個超過 10 的數字，原因是 `strlen` 是藉由找到 `\0` 來計算，而 `str.size()` 會 return 0，因為 `str` object 初始長度會被 initialize 為 0。
## Raw string
```cpp=
#include <iostream>

using namespace std;

int main()
{
  cout << R"(abcd \n \n)" << endl; // abcd \n \n
  cout << R"+*("(Who wouldn't?)", she whispered.)+*" << endl;
  // Use "+*(, )+*" to print out (), "" in raw string
  // ("(Who wouldn't?)", she whispered.)
  return 0;
}
```