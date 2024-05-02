# Header

    <memory>

# Unique Ownership

    std::unique_ptr

리소스에 대한 단독 소유권
스코프를 벗어나거나 리셋되면 참조하던 리소스를 자동으로 해제

return 문을 실행하거나 익셉션이 발생했을 때도 해제

일반적으로 동적으로 할당한 리소스는 unique_ptr 인스턴스에 저장하는 것이 바람직

    auto mySimpleSmartPtr { make_unique<Simple>() };

make_unique()는 값 초기화를 사용    
기본 타입은 0으로 초기화    
객체는 디폴트로 생성

값 초기화 방식을 사용하지 않으려면 C++20부터 추가된 make_unique_for_overwrite() 함수를 사용 
디폴트 초기화를 사용    
기본 타입의 경우 초기화되지 않고 메모리 지점에 있던 값이 그대로 남음    
객체는 디폴트로 생성

    unique_ptr<Simple> mySimpleSmartPtr { new Simple() };

생성자를 호출하는 방식으로 unique_ptr을 생성

컴파일러가 클래스 템플릿의 타입을 클래스 템플릿 생성자로 전달된 인수를 보고 추론할 수있도록 하는 CATD를 사용할 수 없으므로 템플릿 타입을 생략하면 안됨

C++17이전에는 안전을 이유로 반드시 make_unique()를 사용했음
C++17부터는 안전하게 처리가 되지만 가독성 감안하면 make_unique()를 사용하는 것이 나음

get() 메서드를 이용하면 내부 포인터에 직접 접근     
일반 포인터만 전달할 수 있는 함수에 스마트 포인터를 전달할 때 유용

reset()을 이용하면 unique_ptr의 내부 포인터를 해제하고, 필요하다면 이를 다른 포인터로 변경 가능

    mySimpleSmartPtr.reset();   // 리소스 해제 후 nullptr로 초기화
    mySimpleSmartPtr.reset(new Simple{}); // 리소스 해제 후 새로운 Simple 인스턴스로 설정

release()를 이용하면 unique_ptr과 내부 포인터의 관계를 끊을 수 있음     
리소스에 대한 내부 포인터를 리턴한 뒤 스마트 포인터를 nullptr로 설정    
스마트 포인터는 그 리소스에 대한 소유권을 잃으며, 리소스를 다 쓴 뒤 반드시 직접 해제해야 함

단독 소유권을 표현하기 때문에 복사 불가능   
std::move() 유틸리티 함수를 사용하면 이동 의미론을 적용하여 unique_ptr을 다른 곳으로 이동시킬 수 있음

c 스타일의 동적 할당 배열을 저장하는데 적합     
    
    auto myVariableSizedArray { make_unique<int[]>(10) }; // 정수 10개를 가진 C 스타일의 동적 할당 배열
    myVariableSizedArray[1] = 123;

## 커스텀 제거자

    int* my_alloc(int value) { return new int {value}; }
    void my_free(int* p) { delete p; }

    int main()
    {
        unique_ptr<int, decltype(&my_free)*> myIntSmartPtr(my_alloc(42), my_free);
    }

# Shared Ownership

    std::shared_ptr

스코프를 벗어나거나 리셋될 때 리소스를 마지막으로 가리키는 스마트 포인터만 그 리소스를 해제

