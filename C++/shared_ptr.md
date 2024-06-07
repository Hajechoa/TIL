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