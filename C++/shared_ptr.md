# shared_ptr

때로는 포인터 하나를 여러 객체나 코드에서 복제해서 사용할 때가 있음     
unique_ptr은 복제할 수 없기 때문에 이런 용도로 사용할 수 없음       
대신 std::shared_ptr이란 스마트 포인터를 통해 복제 가능한 공동 소유권을 제공        
하지만 동일한 리소스를 가리키는 shared_ptr 인스턴스가 여러 개일 때 해당 리소스를 해체할 시점을 레퍼런스 카운팅이란 기법을 통해 해결할 수 있음       

## shared_ptr을 생성해서 사용하기

shared_ptr의 사용법은 unique_ptr과 비슷함   
shared_ptr은 make_shared()로 생성함     
이렇게 하는 것이 shared_ptr을 직접 생성하는 것보다 훨씬 효율적      
예를 들면 다음과 같음

    auto mySimpleSmartPtr { make_shared<Simple>() };

> shared_ptr을 생성할 때는 반드시 make_shared()를 사용한다.

참고로 unique_ptr과 마찬가지로 shared_ptr도 CTAD을 사용할 수 없음   
따라서 반드시 템플릿 타입을 명시해야 함

make_shared()도 make_unique()처럼 값 초기화를 사용함    
값 초기화를 사용하고 싶지 않다면 C++20부터 제공하는 make_shared_for_overwrite()로 디폴트 초기화를 할 수 있음    
make_unique_for_overwrite()와 원리는 비슷함     

C++17부터 Shared_ptr도 unique_ptr과 마찬가지로 기존 C 스타일 동적 할당 배열에 대한 포인터를 저장할 수 있음  
또한 C++20부터 (make_unique() 처럼) make_shared()도 사용할 수 있게 되었음   
하지만 shared_ptr에 C 스타일 배열을 저장할 수 있다 해도 표준 라이브러리 컨테이너를 사용하는 것이 바람직함   

shared_ptr도 unique_ptr처럼 get()과 reset() 메서드를 제공   
다만 rest()을 호출하면 레퍼런스 카운팅 메커니즘에 따라 마지막 shared_ptr이 제거되거나 리셋될 때 리소스가 해제된다는 점이 다름   
참고로 shared_ptr은 release()를 지원하지 않음   
현재 동일한 리소스를 공유하는 shared_ptr의 개수는 use_count()로 알아낼 수 있음

shared_ptr도 unique_ptr처럼 C 스타일 배열을 저장할 때 new/new[]와 delete/delete[] 연산자로 메모리를 할당하거나 해제할 수 있음   
이 동작은 다음과 같이 변경할 수 있음

    shared_ptr<int> myIntSmartPtr { my_alloc(42), my_free };

여기서 볼 수 있듯이 커스텀 제거자의 타입을 템플릿 타입 매개변수로 지정하지 않아도 됨    
그러므로 unique_ptr로 커스텀 제거자를 작성할 때보다 훨씬 간편함 

다음 코드는 shared_ptr로 파일 포인터를 저장하는 예를 보여줌     
shared_ptr이 리셋되면(여기에서는 스코프를 벗어나면) close()가 호출되면서 파일 포인터가 자동으로 닫힘    
참고로 C++는 파일을 다루는 객체지향 스타일의 클래스를 별도로 제공   
이 클래스도 파일을 자동으로 닫음    
다음 예제는 shared_ptr을 메모리가 아닌 다른 리로스에도 사용할 수 있다는 것을 보여주기 위해 기본 C 함수인 fopen()과 fclose()를 사용함    

    void close(FILE* filePtr)
    {
        if (filePtr == nullptr) { return; }
        fclose(filePtr);
        cout << "File closed." << endl;
    }

    int main()
    {
        FILE* f { fopen("data.txt", "w") };
        shared_ptr<FILE> filePtr { f, close };
        if (filePtr == nullptr)
        {
            cerr << "Error opening file." << endl;
        }
        else
        {
            cout << "File opened." << endl;
            // filePtr을 사용하는 코드
        }
    }

## 레퍼런스 카운팅이 필요한 이유

앞에서 설명했듯이 공공 소유권을 제공하는 shared_ptr과 같은 스마트 포인터가 스코프를 벗어나거나 리셋되면 해당 리소스를 마지막으로 참조하는 스마트 포인터만 그 리소스를 해제할 수 있음    
표준 라이브러리 스마트 포인터에서 사용하는 레퍼런스 카운팅을 이용하는 것    

일반적으로 레퍼런스 카운팅은 클래스의 인스턴스 수나 현재 사용 중인 특정 객체를 추적하는 메커니즘    
레퍼런스 카운팅을 지원하는 사마트 포인터는 실제 포인터를 참조하는 스마트 포인터의 개수를 추적함     
레퍼런스 카운팅을 지원하는 스마트 포인터가 복제될 때마다 동일한 리소스를 가리키는 인스턴스가 새로 생성되면서 레퍼런스 카운터가 하나 증가    
이런 스마트 포인터 인스턴스가 스코프를 벗어나거나 리셋되면 레퍼런스 카운터가 하나 감소  
레퍼런스 카운터가 0에 다다르면 해당 리소스를 아무도 갖고 있지 않다는 뜻이므로 마지막 남은 스마트 포인터가 그 리소스를 해제함    

리소스 카운팅을 지원하는 스마트 포인터는 중복 삭제를 비롯한 메모리 관리 문제를 크게 줄여줌  
예를 들어 동일한 메모리를 가리키는 일반 포인터가 다음과 같이 두 개 있다고 할 때 Simple클래스는 이전 예제와 같으며, 인스턴스가 생성되거나 제거될 때마다 메시지를 출력함

    Simple* mySimple1 { new Simple() };
    Simple* mySimple2 { mySimple1 }; // 포인터를 복제한다.

이 두 포인터를 삭제하면 중복 삭제가 발생

    delete mySimple2;
    delete mySimple1;

물론 이런 코드를 사용하지 않는 것이 가장 좋지만, 여러 계층에 거쳐 함수를 호출하는 과정에서 얼마든지 이런 일이 발생할 수 있음    
즉, 다른 함수가 이미 메모리를 삭제했는데 다음 함수가 그 메모리를 삭제하는 것    

이럴 때는 레퍼런스 카운팅을 적용한 shared_ptr을 사용하면 중복 제거 문제를 피할 수 있음  

    auto smartPtr1 { make_shared<Simple>() };
    auto smartPtr2 { smartPtr1 }; // 포인터를 복제한다.

이렇게 하면 두 스마트 포인터가 모두 스코프를 벗안거나 리셋되더라도 Simple 인스턴스가 단 한 번만 해제됨  

지금까지 설명한 내용은 일반 포인터가 하나도 없을 때만 제대로 작동함     
예를 들어 new로 메모리를 할당한 뒤 동일한 스마트 포인터를 참조하는 shared_ptr 인스턴스 두 개를 생성한 경우를 생각

    Simple* mySimple { new Simple() };
    shared_ptr<Simple> smartPtr1 { mySimple };
    shared_ptr<Simple> smartPtr2 { mySimple };

두 스마트 포인터 모두 동일한 객체를 제거하려고 함   
사용하는 컴파일러의 종류에 따라 이 부분에서 프로그램이 뻗어버릴 수 있음     
실행 결과는 다음과 같음

    Simple constructor called!
    Simple destructor called!
    Simple destructor called!

생성자는 한 번만 호출되었는데 소멸자는 두 번이나 호출되어버렸음     
unique_ptr을 사용할 때도 이런 문제가 발생할 수 있음     
레퍼런스 카운팅을 지원하는 shared_ptr 클래스로도 이런 일이 발생해서 의아할 수 있지만, C++ 표준에 따른 정상적인 동작임   
여러 shared_ptr 인스턴스가 동일한 메모리를 가리키게 하기 위한 안전한 방법은 shared_ptr 인스턴스를 그냥 복제하는 것뿐    

## shared_ptr 캐스팅하기

일반 포인터에서 다른 타입으로 캐스팅할 수 있듯이 shared_ptr도 지정된 것과 다른 타입으로 캐스팅할 수 있음    
물론 캐스팅할 수 있는 타입의 종류에 제약이 있음     
모든 타입을 지원하는 것은 아님  
shared_ptr에 대한 캐스팅을 지원하는 함수로 const_pointer_cast(), dynamic_pointer_cast(), static_pointer_cast(), reinterpret_pointer_cast()가 있음   
이 함수들은 일반 캐스팅 함수인 const_cast(), dynamic_cast(), static_cast(), reinterpret_cast()와 작동 방식이 비슷함     
참고로 이 캐스팅 함수는 shared_ptr에만 적용되고, unique_ptr에는 사용할 수 없음  