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