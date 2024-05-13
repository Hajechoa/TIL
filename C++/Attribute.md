# Attribute

소스 코드에 벤더에서 제공하는 정보나 옵션을 추가하는 메커니즘
C++ 표준 어트리뷰트가 추가되기 전에는 벤더마다 이런 정보를 지정하는 방법이 달랐음
C++11부터 [[어트리뷰트]]와 같이 대괄호를 이용한 형식을 사용하도록 표준화되기 시작함
C++표준에서는 함수 문맥에 유용하게 사용할 수 있는 표준 어트리뷰트를 몇 가지 정의하고 있음

## [[nodiscard]]

어떤 값을 리턴하는 함수에 대해 지정 할 수 있음
컴파일러는 이 함수가 호출될 때 리턴값에 아무런 작업을 하지 않으면 경고 메세지를 출력

    [[nodiscard]] int func()
    {
        return 42;
    }

    int main()
    {
        func();
    }

이 코드를 실행하면 다음과 같은 경고 메세지를 출력

> warning C4834: discarding return value of function with 'nodiscard' attribute

이 기능은 에러 코드를 리턴하는 함수 등에 활용할 수 있음
그런 함수에 [[nodiscard]] 어트리뷰트를 붙이면 에러 코드를 무시하지 않음

일반적으로 [[nodiscard]] 어트리뷰트는 클래스, 함수, 열거형에 적용할 수 있음

## [[maybe_unused]]

뭔가 사용하지 않았을 때 컴파일러가 경고 메시지를 출력하지 않도록 설정하는데 사용

    int func(int param1, int param2)
    {
        return 42;
    }

컴파일러 경고 수준을 높게 설정하고 위와 같이 함수를 정의하면 두 가지 컴파일 경고를 출력

> warning C4100: 'param2': unreferenced formal parameter
> warning C4100: 'param1': unreferenced formal parameter

이 때 다음과 같이 [[maybe_unused]] 어트리뷰트를 지정할 수 있음

    int func(int param1, [[maybe_unused]] int param2)
    {
        return 42;
    }

어트리뷰트가 지정된 두 번째 매개변수에 대해서는 경고 메시지를 출력하지 않고, param1에 대한 경고만 출력
> warning C4100: 'param1': unreferenced formal parameter

클래스, 구조체, 비 static 데이터 멤버, 유니온, typedef, 타입 앨리어스, 변수, 함수, 열거형, 열것값 등에 대해 지정할ㅍ> warning C4100: 'param1': unreferenced formal parameter 수 있음

## [[noreturn]]

함수에 [[noreturn]] 어트리뷰트를 지정하면 호출 지점으로 다시 돌아가지 않음  
주로 프로세스나 스레드 종료와 같이 뭔가가 끝나게 만들거나, 익셉션을 던지는 함수가 여기에 해당함     
이 어트리뷰트를 이용하면 컴파일러가 특정한 경고나 에러 메시지를 출력하지 않게 만들 수 있음

    [[noreturn]] void forceProgramTermination()
    {
        std::exit(1); // <cstdlib>에 정의됨
    }

    bool isDongleAvailable()
    {
        bool isAvailable { false };
        // 라이선싱 동글을 사용할 수 있는지 확인
        return isAvailable;
    }

    bool isFeatureLicensed(int featureId)
    {
        if (!isDongleAvailable())
        {
            // 사용 가능한 라이선싱 동글이 없다면 프로그램을 중단한다.
            forceProgramTermination();
        }
        else
        {
            bool isLicensed { featureId == 42 };
            // 동글이 있다면 주어진 기능에 대한 라이선스 검사를 한다.
            return isLicensed;
        }
    }

    int main()
    {
        bool isLicensed { isFeatureLicensed(42) };
    }

이 코드를 컴파일하면 아무런 경고나 에러 메시지가 출력되지 않음  
[[noreturn]] 어트리뷰트를 제거하고 컴파일하면 다음과 같은 경고를 출력
> warning C4715: 'isFeatureLicensed': not all control paths return a value

## [[deprecated]]

지원 중단된 대상임을 지정하는데 사용    
현재 사용할 수는 있지만 권장하지 않는 대상임을 표시     
지원 중단된 이유를 표현하는 인수를 옵션으로 지정할 수 있음  

    [[deprecated("Unsafe method, plesae use xyz")]] void func();

> warning: 'void func()' is deprecated: unsafe method, please use xyz