# Compiled with C Compiler

There is no name mangling

```sh
(cd cCode && rm -rf libMathC.so && gcc -shared math.c math.h -o libMathC.so && objdump -tT libMathC.so | grep add)
```

```sh
0000000000000630 g     F .text  0000000000000014              add
0000000000000630 g    DF .text  0000000000000014  Base        add
```

# Compiled with C++ Compiler

```sh
(cd cCode && rm -rf libMathCpp.so && g++ -shared math.c math.h -o libMathCpp.so && objdump -tT libMathCpp.so | grep add)
```


```sh
0000000000000660 g     F .text  0000000000000014              _Z3addii
0000000000000660 g    DF .text  0000000000000014  Base        _Z3addii
```

```sh
(cd cppCode && rm -rf main1Cpp && g++ -I../cCode -Wl,-L../cCode -Wl,-lMathCpp main1.cpp -o main1Cpp && LD_LIBRARY_PATH=../cCode ./main1Cpp)
```

# Using C library in C++

* Without `extern "C"` C++ compiler will do name mangling and the linker will not be able to find the reference

```sh
(cd cppCode && rm -rf main1C main1C.o && g++ -c -I../cCode main1.cpp -o main1C.o && nm -A main1C.o | grep add && g++ -L../cCode -lMathC main1C.o -o main1C && LD_LIBRARY_PATH=../cCode ./main1C)
```

```sh
main1C.o:                 U _Z3addii
main1C.o: In function `main':
main1.cpp:(.text+0xf): undefined reference to `add(int, int)'
collect2: error: ld returned 1 exit status
```

* With `extern "C"` C++ compiler will NOT do name mangling and the linker will be able to find the reference in C library without demangled name

```sh
(cd cppCode && rm -rf main2C && g++ -I../cCode -Wl,-L../cCode -Wl,-lMathC main2.cpp -o main2C && LD_LIBRARY_PATH=../cCode ./main2C)
```

```sh
7
```

```sh
(cd cppCode && rm -rf main2C main2C.o && g++ -c -I../cCode main2.cpp -o main2C.o && nm -A main2C.o | grep add && g++ -L../cCode -lMathC main2C.o -o main2C && LD_LIBRARY_PATH=../cCode ./main2C)
```

```sh
main2C.o:                 U add
7
```