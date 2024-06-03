# auto

auto 키워드는 다음과 같이 다양한 상황에 사용한다.
* 함수의 리턴 타입을 추론한다.
* 구조적 바인딩에 사용한다.
* 표현식의 타입을 추론하는 데도 사용한다.
* 비타입(non-type, 타입이 아닌) 템플릿 매개변수의 타입을 추론하는데 사용한다.
* 축약 함수 템플릿(abbreviated function template) 구문
* decltype(auto)에 사용한다.
* 함수에 대한 또 다른 문법으로 사용한다.
* 제네릭 람다 표현식에서 사용한다.

auto 키워드는 컴파일 시간에 타입을 자동으로 추론할 변수 앞에 붙임
이러한 용도에 대한 가장 간단한 예는 다음과 같음

    auto x { 123 }; // x는 int 타입으로 결정된다.

이 예제처럼 int 대신 auto로 쓸 때는 이점이 없지만, 이보다 복잡한 타입에 적용할 때는 편리    
예를 들어 복잡한 타입의 값을 리턴하는 getFoo()라는 함수가 있다고 하면 getFoo()를 호출한 결과를 변수에 저장할 때 그 타입을 직접 나열해도 되지만 다음과 같이 간단한 auto를 붙이면 그 작업을 컴파일러가 대신 처리해줌

    auto result { getFoo() };

또한 이렇게 하면 나중에 이 함수의 리턴 타입을 변경하더라도 코드에서 그 함수가 나온 모든 지점을 일일이 찾아서 고칠 필요 없이 간단히 수정할 수 있음

## auto&

auto를 표현식 타입을 추론하는 데 사용하면 레퍼런스와 const가 제거됨     
예를 들어 다음과 같이 정의된 상황을 생각

    const string message { "Test" };
    const string& foo() { return message; }

foo()를 호출해서 나온 결과를 auto 타입으로 지정한 변수에 저장하려면 다음과 같이 작성

    auto f1 { foo() };

auto를 지정하면 레퍼런스와 const 한정자가 사라지기 때문에 f1은 string 타입이 됨     
따라서 값이 복제됨      
const 레퍼런스 타입으로 지정하려면 다음과 같이 auto 키워드 앞뒤에 레퍼런스 타입과 const 키워드를 붙임       

    const auto& f2 { foo() };

앞에서 as_count() 유틸리티 함수를 소개한 적이 있음  
이 함수는 레퍼런스 매개변수에 대해 const 레퍼런스를 리턴함  
그런데 as_count()와 auto를 조합할 때 주의할 필요가 있음     
auto는 레퍼런스와 const를 제거하기 때문에 다음에 나오는 result 변수 타입은 const string&이 아니라 string이 됨   
따라서 복제되어버림

    string str { "C++" };
    auto result { as_count(str) };

> auto를 지정하면 레퍼런스와 const 지정자가 사라져서 값이 복제된다는 점에 주의한다. 복제 방식으로 전달되지 않게 하려면 auto&나 const auto&로 지정한다.

## auto*

auto 키워드는 포인터에도 적용할 수 있음

    int i { 123 };
    auto p { &i };

p의 타입은 int. 여기에서는 앞에 본 레퍼런스와 달리 복제가 되어버리는 경우가 발생하지 않음.  
하지만 포인터를 다룰 때는 auto* 구문을 사용하는 것이 바람직함   
대상이 포인터임을 명시적으로 드러내기 때문

    auto* p { &i };

또한 그냥 auto를 사용하지 않고 auto*를 사용하면 auto, const 그리고 포인터를 함께 쓸 때 발생하는 이상한 동작도 방지할 수 있음

    const auto p1 { &i };

이렇게 하면 대부분의 경우 의도와 다르게 작동할 것

일반적으로 포인터가 가리키는 대상을 보호해야하는 경우 const를 사용  
여기서 p1이 const int*라고 생각하겠지만 사실은 int* const   
즉, 비 const 정수에 대한 const 포인터인 것  

다음과 같이 const를 auto 뒤에 붙이면 아무런 소용이 없음     
여전히 int* const 타입

    auto const p2 { &i };

auto*와 const를 함께 쓰면 의도한 대로 작동

    const auto* p3 { &i };

이렇게 하면 p3의 타입은 const int*가 됨     
const int 대신 const 포인터를 꼭 써야 한다면 const를 끝에 붙임

    auto* const p4 {&i };

여기서 p4의 타입은 int* const가 됨

지금까지 살펴본 구문을 활용하면 const 포인터와 const int를 동시에 만들 수 있음

    const auto* const p5 { &i };

여기서 p5의 타입은 const int* const
여기서 *를 생략하면 이 작업을 수행 할 수 없음

## 복제 리스트 초기화와 직접 리스트 초기화

초기화 구문은 다음 두 가지가 있으며, 초기자 리스트를 중괄호로 묶어서 표현

* 복재 리스트 초기화: T obj = { arg1, arg2, ... };
* 직접 리스트 초기화: T obj { arg1, arg2, ... };

C++17부터는 auto 타입 추론 기능과 관련하여 복제 리스트 초기화와 직접 리스트 초기화의 차이가 커짐

C++17부터는 다음과 같음

    // 복제 리스트 초기화
    auto a = { 11 };        // intializer_list<int>
    auto b = { 11, 22 };    // intializer_list<int>

    // 직접 리스트 초기화
    auto c { 11 };          // int
    auto d { 11, 22};       // Error, too many elements

이때 복제 리스트 초기화에서 중괄호 안에 나온 원소는 반드시 타입이 모두 같아야 함
예를 들어 다음과 같이 작성하면 컴파일 에러가 발생

    auto b = { 11, 22.33 }; // 컴파일 에러

이전 버전(C++11/14)에서는 복제 리스트 초기화와 직접 리스트 초기화 둘 다 intializer_list<>로 처리 했음

    // 복제 리스트 초기화
    auto a = { 11 };        // intializer_list<int>
    auto b = { 11, 22 };    // intializer_list<int>

    // 직접 리스트 초기화
    auto c { 11 };          // intializer_list<int>
    auto d { 11, 22};       // intializer_list<int>