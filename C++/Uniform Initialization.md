# Uniform Initialization

C++11 이전에는 타입 초기화 방식이 일정하지 않았음   
예를 들어 원을 구조체로 정의한 경우와 클래스로 작성한 경우를 살펴보자   

    struct CircleStruct
    {
        int x, y;
        double radius;
    };

    class CircleClass
    {
        public:
            CircleClass(int x, int y, double radius)
                : m_x { x }, m_y { y }, m_radius { radius } {}
        private:
            int m_x, m_y;
            double m_radius;
    };

C++11 이전에는 CircleStruct 타입 변수와 CircleClass 타입 변수를 초기화하는 방법이 서로 달랐음

    CircleStruct myCircle1 = { 10, 10, 2.5 };
    CircleClass myCircle2(10, 10, 2.5);

구조체에 대해서는 { ... } 문법을 적용한 반면 클래스에 대해서는 함수 표기법인 ( ... )로 생성자를 호출했음

그런데 C++11부터 타입을 초기화할 때 다음과 같이 { ... } 문법을 사용하는 균일 초기화(중괄호 초기화, 유니폼 초기화)로 통일되었음

    CircleStruct myCircle3 = { 10, 10, 2.5 };
    CircleClass myCircle4 = { 10, 10, 2.5 };

myCircle4를 정의하는 문장이 실행될 때 CircleClass의 생성자가 자동으로 호출됨.
또한 다음과 같이 등호(=)를 생략해도 됨

    CircleStruct myCircle5 { 10, 10, 2.5 };
    CircleClass myCircle6 { 10, 10, 2.5 };

또 다른 예로, Employee 구조체를 다음과 같이 초기화 했음

    Employee anEmployee;
    anEmployee.firstInitial = 'J';
    anEmployee.LastInitial = 'D';
    anEmployee.employeeNumber = 42;
    anEmployee.salary = 80'000;

균일 초기화를 이용하면 다음과 같이 한 줄로 초기화 할 수 있음

    Employee anEmployee { 'J', 'D', 42, 80'000 };

이러한 균일 초기화 구문은 구조체나 클래스뿐만 아니라 C++에 있는 모든 대상을 초기화하는데 적용됨.    
예를 들어 다음 코드는 네 변수를 모두 3이란 값으로 초기화함

    int a = 3;
    int b(3);
    int c = { 3 }; // 균일 초기화
    int d { 3 }; // 균일 초기화

균일 초기화는 변수를 영 초기화(제로 초기화)할 때도 적용할 수 있음.  
다음과 같이 중괄호로 빈 집합 표시만 해주면 됨

    int e { }; // 균일 초기화, e는 0이 된다.

균일 초기화를 사용하면 축소 변환(narrowing, 좁히기)을 방지할 수 있음.   
C++에서는 다음과 같이 암묵적으로 축소 변환될 때가 있음

    void func(int i) { /* ... */ }

    int main()
    {
        int x = 3.14;
        func(3.14);
    }

main()의 두 줄 모두에 대해 C++는 x에 값을 대입하거나 func()를 호출할 때 3.14를 3으로 잘라냄.    
컴파일러에 따라 이렇게 축소할 때 경고 메시지를 생성하기도 하고 그렇지 않을 수도 있음.   
어떤 경우에도 균일 초기화를 사용하면 축소 변환으로 인한 미묘하거나 그다지 미묘하지 않은 버그가 발생할 수 있으므로 무시해서는 안됨   
균일 초기화를 사용할 때 C++11 표준을 완전히 지원하는 컴파일러를 사용한다면 x에 값을 대입하거나 func()를 호출하는 문장이 담긴 코드에 대해 에러 메시지가 생성됨.  

    int x { 3.14 }; // 축소 변환으로 인한 에러 발생
    func({ 3.14 }); // 축소 변환으로 인한 에러 발생

축소 변환 캐스트를 하려면 GSL(Guideline Support Library)에서 제공하는 gsl::narrow_cast() 함수를 사용하기 바람.

균일 초기화는 동적으로 할당되는 배열을 초기화할 때도 적용할 수 있음
예를 들면 다음과 같음

    int* pArray = new int[4] { 0, 1, 2, 3 };

C++20부터 배열의 크기를 생략할 수 있음

    int* pArray = new int[] { 0, 1, 2, 3 };

또한 생성자의 초기자에서 클래스 멤버로 정의한 배열을 초기화할 때도 사용할 수 있음

    class MyClass
    {
        public:
            MyClass() : m_array { 0, 1, 2, 3 } {}
        private:
            int m_array[4];
    };

균일 초기화는 std::vector와 같은 표준 라이브러리 컨테이너에도 적용할 수 있음.

> 이 모든 장점을 감안할 때 변수 초기화에 대입 구문을 이용하기보다는 균일 초기화를 사용하는 것이 바람직하다.