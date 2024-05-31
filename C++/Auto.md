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