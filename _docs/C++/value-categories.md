---
title: Value categories
category: C++
order: 1
---

> https://cppreference.com 내용을 참고해서 상당수 번역하고 입맛에 맞게 수정

> 여기서는 C++17를 기준으로 함

# 1차 분류 (Primary category)
* lvalue
    ```cpp
    int n;  // n
    int& ir = n;  // ir
    int&& irr = static_cast<int&&>(n);  // irr 값은 rvalue타입이지만 irr로 구성된 표현식 자체는 lvalue
    struct A { int n; };  // A::n

    std::cin >> n;  // std::cin도 lvalue
    std::cout << n << std::endl;  // std::cout, std::endl 모두 lvalue

    std::getline(std::cin, str); // getline()의 리턴타입은 std::basic_istream<C,T>&
    std::cout << 1; // std::cout
    str1 = str2;  // basic_string& operator=(...);
    ++it; // iterator의 pre-increment 식은 Iterator& 타입의 lvalue를 리턴

    ++a; --a; // 내장 pre-increment, pre-decrement 식
    *p; // 내장된 포인터 간접 참조식
    a[n]; p[n]; // 내장된 참자식, a[n]에서 a,n중 하나는 array lvalue
    ```
  
  * a.m, p-\>m, a.\*mp, p-\>\*mp꼴의 객체의 멤버를 접근하는 식
  
    ```cpp
    struct B {
      enum Enum { E1, E2, E3 }
      int n;
      int foo() { return 1; }
      static int bar() { return 2; }
    } b;

    b.n;  // lvalue
    b.E;  // prvalue, E는 멤버 enum
    //a.foo;  // prvalue, foo는 비정적 멤버함수, 이 식은 pending member function call
    B().n;  // xvalue, B()는 prvalue이며 value-init을 통해 초기화
    ```
    
    ```cpp
    a, b; // 콤마식이 제일 마지막 식 b가 lvalue일때
    a ? b : c;  // 3항 조건식에서 b,c가 같은 타입의 lvalue일 때
    "Hello world";  // string literal
    static_cast<int&>(n); // lvalue 참조로의 형변환
    ```
  
    ```cpp
    void foo() {}
    void (&&bar())(){ return static_cast<void(&&)()>(foo); }
    
    bar();  //lvalue, 즉, &bar()와 같은 식이 가능함
    static_cast<void(&&)(int)>(foo);  // 함수로의 rvalue 참조로 형변환하는 식, 이 자체로도 lvalue
    ```
  
  * array rvalue   
  배열은 함수에서 값으로 리턴될 수 없고 대부분의 형변환 식에서 대상 타입이 될 수도 없다.
  하지만 배열 prvalue는 type alias를 사용해서 단어 한개짜리 타입명을 만들고 중괄호 초기화식에 대해 함수형 형변환을
  했을 때 함수형 임시 객체가 만들어 질 수도 있다.
  클래스 prvalue처럼 배열 prvalue는 temporary materialization을 통해 xvalue로 변환된다.
  배열 xvalue는 클래스 rvalue의 배열 멤버를 접근하거나 std::move 또는 다른 형변환, 또는 rvalue참조를 리턴하는 함수 호출을 사용해서
  직접적으로 생성되기도 한다.
  
    ```cpp
    void f(int (&&x)[2][3]) { ... }

    int a[2][3];
    f(std::move(a));

    using arr_t = int[2][3];
    f(arr_t{}); // int[2][3]의 prvlaue가 만들어 짐
    ```
  
  * pending member function call   
  mf가 비정적 멤버 함수일 때 `a.mf`, `p-\>mf`와 같은 식이라던지, pmf가 멤버 함수로의 포인터일 때 `a.*pmf`, `p->*pmf`와 같은 식은 prvalue 식으로 분류된다. 다만 함수 호출 연산자의 왼쪽인자로 사용되는 경우를 제외하고는, 함수인자들처럼 참조를 초기화하거나 기타 어떤 목적으로든 사용될 수 없다.
  
    ```cpp
    struct A { int foo() { ... } };
    A a;
    //auto b = a.foo; // X
    a.foo();
    ```
  
* prvalue   
    다음의 경우 prvalue
    ```cpp
    // 42, true, nullptr과 같은 literal
    int n = 42;
    bool b = true;
    float* f = nullptr;
    const char* p = "hello";    // 단 "hello"같은 string literal은 prvalue가 아니다
    
    str.substr(1, 2);   // 리턴타입이 참조가 아닌 함수의 호출식, basic_string substr(...) const
    str1 + str2;    // 리턴타입이 참조가 아닌 오버로드된 연산자식, basic_string operator+(...);
    a++;    // 내장 post-increment, post-decrement식
    a+b;    // 내장 산술식
    a && b; // 내장 논리식
    a < b;  // 내장 비교식
    &a; // 내장 address-of 식
    ```
    
    * `a.m`, `p->m` 에서 m이 멤버 enum 또는 비정적 멤버 함수 (a에 대한 단서는 따로 없다)   
    ```cpp
    //앞서 lvalue쪽 코드 참조
    ```
    * `a.*mp`, `p->*mp`에서 mp가 멤버 함수로의 포인터   
    *
    ```cpp
    a,b; // b가 rvalue이 내장 콤마식
    a? b : c;
    static_cast<double>(x); // 참조가 아닌 타입으로의 형변환
    std::string{};  //
    this;
    
    enum class E { E1, E2, E3 };
    E e = E::E1;    // E::E1
    
    template<int N>
    struct X {
        int foo() { return N; }
    };
    X<12> x;
    x.foo(1);
    
    [](int x) { return x + 1; };    // 람다식
    ```
    
* xvalue   
    `not completed`
    
# 혼합 분류 (mixed category)   
    
`not completed`
