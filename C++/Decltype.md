# decltype

인수로 전달한 표현식의 타입을 알아냄
예를 들면 다음과 같음

    int x { 123 };
    decltype(x) y { 456 };

이렇게 작성하면 컴파일러는 y의 타입이 x의 타입인 int라고 추론함

decltype은 레퍼런스나 const 지정자를 삭제하지 않는다는 점에서 auto와 다름   
여기서 다시 string을 가리키는 const 레퍼런스를 리턴하는 함수 foo()를 살펴보면   
f2를 다음과 같이 decltype으로 정의하면 const string& 타입이 되어 복제 방식으로 처리하지 않음

    const string& foo() { return message; }
    decltype(foo()) f2 { foo() };

얼핏 보면 decltype을 사용한다고 해서 특별히 나아질 게 없다고 생각할 수 있지만 템플릿을 사용할 때    
상당히 강력한 효과를 발휘함