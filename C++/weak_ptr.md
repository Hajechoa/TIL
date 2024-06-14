# weak_ptr

shared_ptr과 관련하여 C++에서 제공하는 또 다른 클래스로 weak_ptr이 있음     
weak_ptr은 shared_ptr이 관리하는 리소스에 대한 레퍼런스를 가질 수 있음  
weak_ptr은 리소스를 직접 소유하지 않기 때문에 shared_ptr이 해당 리소스를 해제하는 데 아무런 영향을 미치지 않음  
weak_ptr은 삭제될 때(예를 들어 스코프를 벗어날 때) 가리키던 리소스를 삭제하지 않지만, shared_ptr이 그 리소스를 해제했는지 여부를 확인하는데 사용할 수 있음  
weak_ptr의 생성자는 shared_ptr이나 다른 weak_ptr을 인수로 받음  
weak_ptr에 저장된 포인터에 접근하려면 shared_ptr로 변환해야하 함    
변환 방법은 다음 두 가지    

* weak_ptr 인스턴스의 lock() 매서드를 이용하여 shared_ptr을 리턴받는다. 이때 shared_ptr에 연결된 weak_ptr이 해제되면 shared_ptr의 값은 nullptr이 된다.
* shared_ptr의 생성자에 weak_ptr을 인수로 전달해서 생성한다. 이때 shared_ptr에 연결된 weak_ptr이 해제되면 std::bad_weak_ptr 익셉션을 던진다.

weak_ptr을 사용하는 예는 다음과 같음

    void useResource(weak_ptr<Simple>& weakSimple)
    {
        auto resource{ weakSimple.lock() };
        if (resource)
        {
            cout << "Resource still alive." << endl;
        }
        else
        {
            cout << "Resource has been freed!" << endl;
        }
    }

    int main()
    {
        auto sharedSimple { make_shared<Simple>() };
        weak_ptr<Simple> weakSimple { sharedSimple };

        // weak_ptr을 사용한다.
        useResource(weakSimple);

        // shared_ptr을 리셋한다.
        // Simple 리소스에 대한 shared_ptr은 하나뿐이므로
        // weak_ptr이 살아 있더라도 리소스가 해제된다.
        sharedSimple.reset();

        // weak_ptr을 한 번 더 사용한다.
        useResource(weakSimple);
    }

이 코드를 실행한 결과는 다음과 같음

    Simple constructor called!
    Resource still alive.
    Simple destructor called!
    Resource has been freed!

C++17부터 weak_ptr도 shared_ptr과 마찬가지로 C 스타일 배열을 지원함

## 함수에 전달하기

매개변수에서 포인터를 받는 함수는 소유권을 전달하거나 공유할 경우에만 스마트 포인터를 사용해야 함   
shared_ptr의 소유권을 공유하려면 shared_ptr을 값으로 전달받으면 됨  
마찬가지로 unique_ptr의 소유권을 전달하려면 unique_ptr을 값으로 받으면 됨   
후자의 경우 이동 의미론이 필요함    

소유권 전달과 공유가 전혀 없다면 비 const 대상에 대한 레퍼런스나 const에 대한 레퍼런스로 매개변수를 정의해야 함     
또는 매개변수에 nulltpr을 가지는 것이 적합하다면 일반 포인터로 정의함   
매개변수 타입을 const shared_ptr\<T>&나 const unique_ptr\<T>&로 정의하는 것은 큰 의미가 없음  