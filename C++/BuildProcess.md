# Build Process of C++  
> C++ �ҽ��ڵ尡 excutable file �� �Ǳ������ ����  
  
## 0. Build Process  
  * [Pre Processor](##1.Pre_Process)  
  * [Compiler](##2.Compiler)  
  * [Linker](##3.Linker)  
  
## 1.Pre Processor  
  * �ҽ��ڵ带 �����Ϸ����� ������ �� ��ġ�� �ܰ�.  
  �ڵ�� ```#include```, ```#define```, ```#pragma``` �� ���� [��ó�� Ű����](https://en.cppreference.com/w/cpp/preprocessor)��
  ġȯ�Ѵ�.  
  ��, **�ڵ� ��ü�� ����**�Ǿ� �����Ϸ����� ���޵ȴ�.  
  ��ó�� ������ ����� �� .cpp ���ϸ��� ������ ```translation unit```�� ���������.
  * ```#include``` ġȯ���� �� redefinition �� �����ϱ� ���� ```#ifndef ~ #endif``` Ȥ�� ```#pragma once```�� ����Ѵ�.
  
  * c++17���ʹ� ����� �� �ִٸ� ```#define``` �� ```#ifdef``` ��� ```constexpr``` �� ```if constexpr```�� ����ϴ°��� �ٶ����ϴ�.  
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
  * preprocess�� ����� ������� ```translation unit```�� ```.obj``` ���Ϸ� �����.  
    
  * ```.obj```���Ͽ��� �� ```trnaslation unit```�� **machine code** �� global variable�� initialization value���� **data**���·� ����ִ�.  
    
  * C++�� Compile Process  �߿� **function overloading** �� �����ϱ� ���� [**name mangling**](https://en.wikipedia.org/wiki/Name_mangling#How_different_compilers_mangle_the_same_functions) �� �����Ѵ�.  
    ````console
    g++ {source file} -c  
    nm {object file}
    ````  
    ��ɾ ���ؼ� Ȯ�� �����ϴ�.  

    C inferface�� ���� binary�� �����ϱ� ���ؼ��� C++ �� **name mangling** �� ������� �ʵ��� ```extern "c"``` Ű���带 ����Ѵ�.  
    ````c++
    extern "C" void foo();
    extern "C" void bar();
    ````
    Ȥ��
    ````c++
    extern "C"
    {
        void foo();
        void bar();
    }
    ````
    �� ���·� ����Ѵ�.  
      
## 3.Linker  
  * �ʿ��� ```.obj``` ������ ��Ƽ� ```machine code```, ```data``` �� ���Ҿ� ���࿡ �ʿ��� �߰�������  
   ������ �ϳ��� ```excutable file``` �� �����.  
  