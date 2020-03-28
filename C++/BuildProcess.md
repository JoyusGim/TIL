# Build Process of C++  
> C++ 소스코드가 excutable file 이 되기까지의 과정  
  
## 0. Build Process  
  * [Pre Processor](##1.Pre_Process)  
  * [Compiler](##2.Compiler)  
  * [Linker](##3.Linker)  
  
## 1.Pre Processor  
  * 소스코드를 컴파일러에게 보내기 전 거치는 단계.  
  코드속 ```#include```, ```#define```, ```#pragma``` 와 같은 [전처리 키워드](https://en.cppreference.com/w/cpp/preprocessor)를
  치환한다.  
  즉, **코드 자체가 수정**되어 컴파일러에게 전달된다.  
  전처리 과정의 결과로 각 .cpp 파일마다 별도의 ```translation unit```이 만들어진다.
  * ```#include``` 치환과정 중 redefinition 을 방지하기 위해 ```#ifndef ~ #endif``` 혹은 ```#pragma once```를 사용한다.
  
  * c++17부터는 사용할 수 있다면 ```#define``` 과 ```#ifdef``` 대신 ```constexpr``` 과 ```if constexpr```을 사용하는것이 바람직하다.  
    ````c++
    // old C++ style
    #define ABCD 2
    ...
    #ifdef ABCD == 2
        // code here
    #else
        // code here
    #endif
    ````  
  
    ````c++
    // modern C++ style
    constexpr int ABCD = 2;
    ...
    if constexpr(ABCD == 2) {
        // code here
    } else {
        // code here
    }
    ````  
  
## 2.Compiler  
  * preprocess의 결과로 만들어진 ```translation unit```을 ```.obj``` 파일로 만든다.  
    
  * ```.obj```파일에는 각 ```trnaslation unit```의 **machine code** 와 global variable의 initialization value들이 **data**형태로 들어있다.  
    
  * C++는 Compile Process  중에 **function overloading** 을 지원하기 위한 [**name mangling**](https://en.wikipedia.org/wiki/Name_mangling#How_different_compilers_mangle_the_same_functions) 을 수행한다.  
    ````console
    g++ {source file} -c  
    nm {object file}
    ````  
    명령어를 통해서 확인 가능하다.  

    C inferface를 가진 binary를 제공하기 위해서는 C++ 의 **name mangling** 을 사용하지 않도록 ```extern "c"``` 키워드를 사용한다.  
    ````c++
    extern "C" void foo();
    extern "C" void bar();
    ````
    혹은
    ````c++
    extern "C"
    {
        void foo();
        void bar();
    }
    ````
    의 형태로 사용한다.  
      
## 3.Linker  
  * 필요한 ```.obj``` 파일을 모아서 ```machine code```, ```data``` 와 더불어 실행에 필요한 추가정보를  
   포함한 하나의 ```excutable file``` 을 만든다.  
  