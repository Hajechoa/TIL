# C++20 consteval

constexpr 키워드는 함수가 컴파일 시간에 실행될 수도 있다고 지정할 뿐 반드시 컴파일 시간에 실행되도록 보장하는 것은 아님

    constexpr double inchToMm(double inch) { return inch * 25.4; }

다음과 같이 호출하면 의도한 대로 컴파일 시간에 평가

    constexpr double const_inch { 6.0 };
    constexpr double mm1 { inchToMm(const_inch) }; // 컴파일 시간에 평가됨

하지만 다음과 같이 호출하면 컴파일 시간이 아닌 실행 시간에 평가

    double dynamic_inch { 8.0 };
    double mm2 { inchToMm(dynamic_inch) }; // 실행 시간에 평가됨

함수가 항상 컴파일 시간에 평가되도록 보장하고 싶다면 C++20부터 제공하는 consteval 키워드로 해당 함수를 즉시 실행 함수로 만듬    
예를 들어 앞에 나온 inchToMm()) 함수를 다음과 같이 바꿀 수 있음

    consteval double inchToMm(double inch) { return inch * 25.4; }

그러면 이전의 inchToMm() 함수에 대한 첫 번째 호출은 아무런 에러 메시지 없이 컴파일 시간에 평가  
하지만 두 번째 호출문은 컴파일 시간에 평가될 수 없기 때문에 에러가 발생