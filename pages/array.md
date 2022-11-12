## Initialization
- 只有在 define array 時才可以使用大括號（`int A[4] = {1, 2, 3, 4}`），後續若要更改 array element 必須使用中括號（`A[2] = 1`）
- 可以不寫 size，讓 compiler 幫你數，不過要小心，compiler 有可能會數的跟你不一樣（`int A[] = {1,2,3,4}`）
- 若 paritially initialize，則沒寫到的通通會被 compiler 設為 0
  
    ```c=
    long totals[500] = {0}; // all elements are set to 0
    ```
- C++11 以上 only：可省略等號以及初始的 0
    ```cpp
    int A[100] {} // all elements are 0
    ```