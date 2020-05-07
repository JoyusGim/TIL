# Type deductions in C++
> C++�� Ÿ�� �߷� ��Ģ

## 0.Type deductions
  * [Type deduction in template](## 1.Type deduction in template) 

## 1.Type deduction in template

> ���ø������� Ÿ�� �߷��� ```ParamType```�� ���� ���� �� ���� ���� ������.
> ````c++
> template <typename T>
> void f(ParamType param);
> 
> f(expr);  // expr�� ���� T�� ParamType �߷�.
> ````
* ���������� ���������� �ƴ� ���.  
    �� ��� ```ParamType```�� ������ ���� ����ȴ�.  
    a. ���� ```expr```�� ���������̸� �����κ��� ���õȴ�.  
    b. �׷� ���� ```expr```�� ������ ```ParamType```�� ���� ���ϸ�Ī ������� �������� ```T```�� ������ �����Ѵ�.  
    ````c++
    template <typname T>
    void f(T& param);     

    int x = 27;             // x�� int
    const int cx = x;       // cx�� const int
    const int& rx = &x;     // rx�� const int�� x�� ���� ����

    f(x);                   // call void f<int>(int&)
    f(cx);                  // call void f<const int>(const int&)
    f(rx);                  // call void f<const int>(const int&)
    ````
    �� ����° ȣ�⿡�� ```cx```�� ```rx```�� ```const type``` �̹Ƿ� ```T```���� ```const type```���� �߷еȴ�.  
    ��, ����� ��ü�� ���� �Ű������� �����ϴ� ȣ���ڴ� �� ��ü�� �������� ���� ���̶�� ����� �� �ִ�.  
    �� ��° ȣ�⿡�� ```rx```�� ������ ����������, ```T```�� �������� �߷еǾ����� ��������.  
    ```T&```�� ```const T&```�� �ٲܰ�쿡�� ���� ������ ������ ���̻� ```const```�� ```T```�� �Ϻη� �߷е� �ʿ�� ����.  
    ````c++
    template <typname T>
    void f(const T& param);

    int x = 27;             // x�� int
    const int cx = x;       // cx�� const int
    const int& rx = &x;     // rx�� const int�� x�� ���� ����

    f(x);                   // call void f<int>(const int&)
    f(cx);                  // call void f<int>(const int&)
    f(rx);                  // call void f<int>(const int&)
    ````
     
* ���������� ���.  
    ���ø��� �������� �Ű������� �޴� ��쿡�� �Ű������� ������ ```r-value reference```�� ���� ��,```T&&```������, ```l-value```�μ��� ���޵Ǹ� ```r-value reference```�ʹ� �ٸ� ������� �����Ѵ�.  

    a. ���� ```expr```�� ```l-value```�̸�, ```T```�� ```ParamType```��� ```l-value reference```�� �߷еȴ�.
    + ���ø� Ÿ�� �߷п��� ```T```�� ������������ �߷еǴ� ���� �� ��찡 �����ϴ�.
    + ```ParamType```�� ���� ������ ```r-value reference```�� ���� ���������, �߷е� Ÿ���� ```l-value reference```�̴�.
    
    b. ���� ```expr```�� ```r-value```�̸�, "���������� ���������� �ƴѰ��"�� ��Ģ�� ����ȴ�.  
    ````c++
    template <typname T>
    void f(T&& param);          // param�� ��������

    int x = 27;                 // x�� int
    const int cx = x;           // cx�� const int
    const int& rx = &x;         // rx�� const int�� x�� ���� ����

    f(x);                       // x��  l-value�̹Ƿ�, call void f<int&>(int&)
    f(cx);                      // cx�� l-value�̹Ƿ�, call void f<const int&>(const int&)
    f(rx);                      // rx�� l-value�̹Ƿ�, call void f<const int&>(const int&)
    f(27);                      // 27�� r-value�̹Ƿ�, call void f<int>(int&&);
    ````

* ������ �ƴ� ���(������ ����).  
    �� ���� �μ��� �Լ��� ������ ����(pass-by-value)�Ǵ� ��Ȳ�̴�. ����, param�� �μ��� ���纻, �� ������ ���ο� ��ü��� ��� ������, ```expr```���� ```T```�� �߷еǴ� �������� ������ ���� ��Ģ�� ����ȴ�.  
    a. ```expr```�� ������ �����̸�, ���� �κ��� ���õȴ�.  
    b. ```expr```�� �������� ������ ��, ���� ```expr```�� ```const```�̸� �� ```const```���� �����Ѵ�.(```volatile```�� ��쿡�� �����Ѵ�)  
    ````c++
    template <typname T>
    void f(T param);                // param�� ������ ����

    int x = 27;                     // x�� int
    const int cx = x;               // cx�� const int
    const int& rx = &x;             // rx�� const int�� x�� ���� ����
    const char* const ptr = "test"; // ptr�� const char* const

    f(x);                           // call void f<int>(int)
    f(cx);                          // call void f<int>(int)
    f(rx);                          // call void f<int>(int)
    f(ptr);                         // call void f<const char*>(const char*)
    ````
    ```cx```�� ```rx```�� ```const```������, ```param```�� ```const```�� �ƴϴ�.(```param```�� ```cx```�� ```rx```�ʹ� �������� ����� ��ü�̹Ƿ�) ��, ```cx```�� ```rx```�� ������ �� ���ٴ� ���� ```param```�� ���� ���� ���ο� ����� ����.  

* �μ��� �迭�� ���  
    �迭�� ���ø��� �����ϸ� �Ű������� �����ͷ� �ر�(decay)�ȴ�.  
    ````c++
    template <typename T>
    void f(T param);

    const char name[] = "test"; // name�� const char[5]

    f(name);                    // call void f<const char*>(const char*)
    ````  
    �� �ڵ� ó�� �Լ��� �Ű������� �迭�� ������ �� ������, �迭�� ���� ������ ������ ���� �ִ�.  
    ````c++
    template <typename T>
    void f(T& param);   // param�� ���� ����

    ...
    f(name);            // call void f<const char [5]>(const char (&)[5])        
    ````
    �̷��� Ư���� �̿��Ͽ� �迭�� ��� ������ ���̸� �߷��ϴ� ���ø��� ���� �� �ִ�.
    ````c++
    // �迭�� ũ�⸦ ������ Ÿ�ӿ� ����� ��ȯ�ϴ� ���ø� �Լ�
    template <typename T, std::size_t N>
    constexpr std::size_t arraySize(T (&)[N]) noexcept
    {
        return N;
    }

    int keyVals[] = {1, 3, 7, 9, 11, 22, 35};

    int mappedVals[arraySize(keyVals)];             // c-style array
    std::array<int, arraySize(keyVals)> mappedVals; // c++ style array
    ````