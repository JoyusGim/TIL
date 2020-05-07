# Type deductions in C++
> C++의 타입 추론 규칙

## 0.Type deductions
  * [Type deduction in template](##1.Type)
  * [Type deduction in auto keyword](##2.Type)

## 1.Type deduction in template

> 템플릿에서의 타입 추론은 ```ParamType```에 따라서 다음 세 가지 경우로 나뉜다.
> ````c++
> template <typename T>
> void f(ParamType param);
> 
> f(expr);  // expr로 부터 T와 ParamType 추론.
> ````
* 참조이지만 보편참조가 아닌 경우.  
    이 경우 ```ParamType```은 다음과 같이 추론된다.  
    a. 만일 ```expr```이 참조형식이면 참조부분은 무시된다.  
    b. 그런 다음 ```expr```의 형식을 ```ParamType```에 대해 패턴매칭 방식으로 대응시켜 ```T```의 형식을 결정한다.  
    ````c++
    template <typname T>
    void f(T& param);     

    int x = 27;             // x는 int
    const int cx = x;       // cx는 const int
    const int& rx = &x;     // rx는 const int인 x에 대한 참조

    f(x);                   // call void f<int>(int&)
    f(cx);                  // call void f<const int>(const int&)
    f(rx);                  // call void f<const int>(const int&)
    ````
    두 세번째 호출에서 ```cx```와 ```rx```는 ```const type``` 이므로 ```T```또한 ```const type```으로 추론된다.  
    즉, 상수성 객체를 참조 매개변수로 전달하는 호출자는 그 객체가 수정되지 않을 것이라고 기대할 수 있다.  
    세 번째 호출에서 ```rx```의 형식이 참조이지만, ```T```는 비참조로 추론되었음을 주의하자.  
    ```T&```를 ```const T&```로 바꿀경우에도 동작 원리는 같지만 더이상 ```const```가 ```T```의 일부로 추론될 필요는 없다.  
    ````c++
    template <typname T>
    void f(const T& param);

    int x = 27;             // x는 int
    const int cx = x;       // cx는 const int
    const int& rx = &x;     // rx는 const int인 x에 대한 참조

    f(x);                   // call void f<int>(const int&)
    f(cx);                  // call void f<int>(const int&)
    f(rx);                  // call void f<int>(const int&)
    ````
    
* 보편참조인 경우.  
    템플릿이 보편참조 매개변수를 받는 경우에는 매개변수의 선언은 ```r-value reference```의 형태 즉,```T&&```이지만, ```l-value```인수가 전달되면 ```r-value reference```와는 다른 방식으로 동작한다.  

    a. 만일 ```expr```이 ```l-value```이면, ```T```와 ```ParamType```모두 ```l-value reference```로 추론된다.
    + 템플릿 타입 추론에서 ```T```가 참조형식으로 추론되는 경우는 이 경우가 유일하다.
    + ```ParamType```의 선언 구문은 ```r-value reference```와 같은 모습이지만, 추론된 타입은 ```l-value reference```이다.
    
    b. 만일 ```expr```이 ```r-value```이면, "참조이지만 보편참조가 아닌경우"의 규칙이 적용된다.  
    ````c++
    template <typname T>
    void f(T&& param);          // param은 보편참조

    int x = 27;                 // x는 int
    const int cx = x;           // cx는 const int
    const int& rx = &x;         // rx는 const int인 x에 대한 참조

    f(x);                       // x는  l-value이므로, call void f<int&>(int&)
    f(cx);                      // cx는 l-value이므로, call void f<const int&>(const int&)
    f(rx);                      // rx는 l-value이므로, call void f<const int&>(const int&)
    f(27);                      // 27은 r-value이므로, call void f<int>(int&&);
    ````

* 참조가 아닌 경우(포인터 포함).  
    이 경우는 인수가 함수에 값으로 전달(pass-by-value)되는 상황이다. 따라서, param은 인수의 복사본, 즉 완전히 새로운 객체라는 사실 때문에, ```expr```에서 ```T```가 추론되는 과정에서 다음과 같은 규칙이 적용된다.  
    a. ```expr```의 형식이 참조이면, 참조 부분은 무시된다.  
    b. ```expr```의 참조성을 무시한 후, 만일 ```expr```이 ```const```이면 그 ```const```역시 무시한다.(```volatile```일 경우에도 무시한다)  
    ````c++
    template <typname T>
    void f(T param);                // param이 값으로 전달

    int x = 27;                     // x는 int
    const int cx = x;               // cx는 const int
    const int& rx = &x;             // rx는 const int인 x에 대한 참조
    const char* const ptr = "test"; // ptr은 const char* const

    f(x);                           // call void f<int>(int)
    f(cx);                          // call void f<int>(int)
    f(rx);                          // call void f<int>(int)
    f(ptr);                         // call void f<const char*>(const char*)
    ````
    ```cx```와 ```rx```는 ```const```이지만, ```param```은 ```const```가 아니다.(```param```은 ```cx```나 ```rx```와는 독립적인 복사된 객체이므로) 즉, ```cx```나 ```rx```가 수정될 수 없다는 점은 ```param```의 수정 가능 여부와 상관이 없다.  

* 인수가 배열인 경우  
    배열을 템플릿에 전달하면 매개변수가 포인터로 붕괴(decay)된다.  
    ````c++
    template <typename T>
    void f(T param);

    const char name[] = "test"; // name은 const char[5]

    f(name);                    // call void f<const char*>(const char*)
    ````  
    위 코드 처럼 함수의 매개변수를 배열로 선언할 수 없지만, 배열에 대한 참조로 선언할 수는 있다.  
    ````c++
    template <typename T>
    void f(T& param);   // param은 참조 전달

    ...
    f(name);            // call void f<const char [5]>(const char (&)[5])        
    ````
    이러한 특성을 이용하여 배열에 담긴 원소의 길이를 추론하는 템플릿을 만들 수 있다.
    ````c++
    // 배열의 크기를 컴파일 타임에 상수로 반환하는 템플릿 함수
    template <typename T, std::size_t N>
    constexpr std::size_t arraySize(T (&)[N]) noexcept
    {
        return N;
    }

    int keyVals[] = {1, 3, 7, 9, 11, 22, 35};

    int mappedVals[arraySize(keyVals)];             // c-style array
    std::array<int, arraySize(keyVals)> mappedVals; // c++ style array
    ````  
* 인수가 함수인 경우  
    배열처럼 함수타입도 함수 포인터로 붕괴(decay) 한다.
    ````c++
    void someFunc(int, double);

    template <typename T>
    void f1(T param);           // param은 값 전달

    template <typename T>
    void f2(T& param);          // param은 참조 전달

    f1(someFunc);               // param은 함수 포인터로 추론
                                // call f1<void (*)(int, double)>(void (*)(int, double))
    f2(someFunc);               // param은 함수 참조로 추론
                                // call f2<void (int, double)>(void (&)(int, double))

    ````  
<br />

## 2.Type deduction in auto keyword
> ```auto```타입 추론은 특수한 경우를 제외하곤```template```에서의 타입추론과 동일하다. 그렇기 때문에 ```template```에서와 마찬가지로 세 가지 경우로 나뉜다.
* 형식 지정자가 포인터나 참조 형식이지만 보편참조는 아닌경우
* 형식 지정자가 보편참조인 경우
* 형식 지정자가 포인터도 아니고 참조도 아닌경우
````c++
auto x = 27;        // 세 번째 경우
const auto cx = x;  // 세 번째 경우
const auto& rx = x; // 첫 번째 경우

// 두 번쨰 경우
auto&& uref1 = x;   // x는 int type l-value 이므로, int&로 추론
auto&& uref2 = cx;  // cx는 const int type l-value 이므로, int&로 추론
auto&& uref3 = 27;  // 27은 int type r-value 임로, int&&로 추론

// 배열의 경우
const char name[] = "test"; // name은 const char[5]

auto arr1 = name;           // arr1은 const char*
auto& arr2 = name;          // arr2는 const char (&)[5]

// 함수의 경우
void someFunc(double, int);

auto func1 = someFunc;      // func1은 void (*)(double, int)
auto& func2 = someFunc;     // func2는 void (&)(double, int)
````
이처럼 대부분의 경우에 템플릿과 똑같은 타입 추론을 한다.  

그러나 ```uniform initializeation```을 사용할 경우에는 서로 다르다.  
이 경우```auto```는 ```std::initializer_list<T>```로 타입추론을 하지만, 템플릿의 경우 타입추론을 할 수 없다.
````c++
auto x1 = 27;       // x1은 int type 27
auto x2(27);        // x2는 int type 27
auto x3 = { 27 };   // x3는 std::initializer_list<int> type { 27 }
auto x4{ 27 };      // x4는 int type 27 (since N3922)
````
````c++
template <typename T>
void f(T param);

f({ 11, 23, 9 });       // 컴파일 에러. T에대한 타입추론 불가능
````  
하지만 ```param```의 타입이 ```std::initializer_list<T>```라면 타입추론이 가능하다.
````c++
template <typename T>
void f(std::initializer_list<T> param);

f({ 11, 23, 9 });       // call void f<int>(std::initializer_list<int>)
````  
* C++14부터는 함수의 반환타입과 ```lambda```의 매개변수에 ```auto```를 사용할 수 있다. 그러나 이러한 용법에는 ```auto```형식이 아니라 "템플릿에서의 타입추론"의 규칙이 사용된다. 따라서 함수에서 ```uniform initialization```을 이용한 값을 반환하거나 ```lambda```의 매개변수로 사용하면 컴파일이 실패한다.  
````c++
auto createInitList()
{
    return { 1, 2, 3 };     // 컴파일 실패
}
````
````c++
std::vector<int> v;
...
auto resetV = [&v](const auto& newValue) { v = newValue; };     // since C++14
...
resetV({ 1, 2, 3 });    // 컴파일 실패
````