# constexpr

상수 표현식이란 컴파일 시간에 평가되는 표현식   
상수 표현식이 꼭 필요한 경우가 있음 
예를 들어 배열을 정의할 때는 크기를 상수 표현식으로 지정해야 함     
다음과 같이 작성하면 에러가 발생

    const int getArraySize() { return 32; }

    int main()
    {
        int myArray[getArraySize()]; // C++에서 허용하지 않는 표현
    }

constexpr 키워드를 사용하면 앞에 나온 getArraySize() 함수를 상수 표현식에서 호출되게 만들 수 있음

    constexpr int getArraySize() { return 32; }

    int main()
    {
        int myArray[getArraySize()]; // OK
    }

심지어 다음과 같이 작성할 수도 있음

    int myArray[getArraySize() + 1]; // OK

함수에 constexpr을 적용하면 그 함수에 상당히 많은 제약사항이 적용   
컴파일러가 그 함수를 컴파일 시간에 평가해야 하기 때문   
예를 들어 constexpr 함수는 다른 constexpr 함수를 호출할 수 있지만, constexpr이 아닌 함수는 호출할 수 없음   
이런 함수는 부작용이 발생해서도 안 되고 익셉션을 던질 수도 없기 때문    
constexpr 함수는 C++의 고급 기능에 해당 
이 책에서는 더 이상 자세히 다루지 않음

constexpr 생성자를 정의하면 사용자 정의 타입에 대한 상수 표현식 변수를 만들 수 있음     
constexpr 함수와 마찬가지로 constexpr 클래스를 적용하면 상당히 많은 제약사항이 적용     
여기에 대해 자세한 사항은 이 책에서 다루지 않음
예를 들어 다음 코드에 나오는 Rect 클래스는 constexpr 생성자를 정의  
또한 몇 가지 계산을 수행하도록 constexpr getArea() 메서드도 정의    

    class Rect
    {
        public:
            constexpr Rect(size_t width, size_t height)
                : m_width { width }, m_height { height } {}

            constexpr size_t getArea() const { return m_width * m_height; }
        pricate:
            size_t m_width { 0 }, m_height { 0 };
    };

이렇게 클래스를 정의하면 다음과 같이 constexpr 객체를 간단히 선언할 수 있음

    constexpr Rect r { 8, 2 };
    int myArray[r.getArea()]; // OK