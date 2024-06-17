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

## 함수에서 리턴하기

표준 스마트 포인터인 shared_ptr, unique_ptr, weak_ptr은 함수에서 값으로 리턴하는 것을 쉽고 효율적으로 처리함    
이는 리턴값 최적화(RVO)와 이름 있는 리턴값 최적화(NRVO), 이동 의미론 덕분   
함수에서 스마트 포인터를 효율적으로 리턴한다는 사실만 기억하면 충분     
예를 들어 다음과 같이 creator() 함수를 정의할 수 있음   

    unique_ptr<Simple> create()
    {
        auto ptr { make_unique<Simple>() };
        // 원하는 작업을 수행한다.
        return ptr;
    }

    int main()
    {
        unique_ptr<Simple> mySmartPtr1 { create() };
        auto mySmartPtr2 { create() };
    }

## enable_shared_from_this

std::enable_shared_from_this를 상속해서 클래스를 만들면 객체 대해 호출한 메서드가 자신에게 shared_ptr이나 weak_ptr을 안전하게 리턴할 수 있음    
이 클래스 없이 올바른 shared_ptr이나 weak_ptr을 리턴하는 방법은 weak_ptr을 이 클래스의 멤버로 추가한 뒤 이를 복제해서 리턴하거나, 이를 이용하여 생성한 shared_ptr을 리턴하는 것     
enable_shared_from_this 클래스를 상속하면 다음과 같은 두 메서드가 추가 됨   

* shared_from_this(): 객체의 소유권을 공유하는 shared_ptr을 리턴한다.   
* weak_from_this(): 객체의 소유권을 추적하는 weaK_ptr을 리턴한다.   

이는 자세히 설명하지 않는 고급 기능으로서 간단히 사용 방법을 소개하면 다음과 같음   
여기서 shared_from_this()와 weaK_-_from_this()는 모두 public 메서드     
그런데 public 인터페이스에서 from_this라는 부분이 좀 이상할 것임    
그러므로 다음과 같이 정의된 Foo 클래스에 있는 getPointer() 메서드를 호출하는 과정을 살펴봄  

    class Foo : public enable_shared_from_this<Foo>
    {
        public:
        shared_ptr<Foo> getPointer()
        {
            return shared_from_this();
        }
    };

    int main()
    {
        auto ptr1 { make_shared<Foo>() };
        auto ptr2 { ptr1->getPointer() };
    }

객체의 포인터가 이미 shared_ptr에 저장된 경우에만 객체에 shared_from_this()를 사용할 수 있다는 점에 주목    
다른 경우에는 bad_weaK_ptr 익셉션이 발생    
예제에서 main()을 보면 make_shared()로 Foo 인스턴스를 담은 shared_ptr인 ptr1을 생성 
그래야 Foo 인스턴스에 대한 shared_from_this()를 호출할 수 있음  
한편 weaK_from_this()는 언제나 호출할 수 있지만, 포인터가 shared_ptr에 저장되지 않은 객체 대해 호출할 경우에는 빈 weaK_-_ptr이 리턴될 수 있음

getPointer() 메서드를 잘못 구현한 예는 다음과 같음

    class Foo
    {
        public:
        shared_ptr<Foo> getPointer()
        {
            return shared_ptr<Foo>(this);
        }
    };

Foo 클래스를 이렇게 구현한 상태에서 앞에 나온 main() 코드처럼 작성하면 중복 삭제가 발생함
두 개의 shared_ptr(ptr1과 ptr2)이 동일한 객체를 가리키고 있어서 스코프를 벗아나면 서로 이 객체를 삭제하려 하기 때문

## 현재는 폐기된 auto_ptr

C++11 이전에는 표준 라이브러리에서 스마트 포인터를 간단히 구현한 auto_ptr을 제공했는데, 아쉽게도 몇 가지 심각한 단점이 있었음   
그중 하나는 vector와 같은 표준 라이브러리 컨테이너 안에서는 제대로 작동하지 않는다는 점     
C++11과 C++14부터는 auto_ptr을 공식적으로 폐기했고, C++17부터 완전히 삭제되면서 그 빈자리를 unique_ptr과 shared_ptr이 대체했음  
여기서 auto_ptr을 소개하는 이유는 이를 절대로 사용하면 안 된다는 것을 강조하기 위해서   

> 기존에 제공되던 스마트 포인터인 auto_ptr을 사용하지 말고 unique_ptr이나 shared_ptr을 사용한다.