# C++20 Designated Initizlizer

C++20부터 지정 초기자가 도입되었음  
묶음 타입의 데이터 멤버를 초기화하는 데 사용    
묶음 타입(Aggregate type)이란 public 데이터 멤버만 갖고, 사용자 정의 생성자나 상속된 생성자가 없고,     
virtual 함수도 없으며, virtual, private, protected 베이스 클래스도 없는 배열 타입 객체나 구조체 객체, 클래스 객체를 말함    
지정 초기자는 점 뒤에 데이터 멤버의 이름을 적는 방식으로 표기   
지정 초기자에 나오는 데이터 멤버는 반드시 데이터 멤버가 선언된 순서를 따라야 함     
지정 초기자와 비지정 초기자를 섞어 쓸 수는 없음 
지정 초기자로 초기화되지 않은 데이터 멤버는 모두 디폴트 값으로 초기화   

* 클래스 내부 초기자를 가진 데이터 멤버는 거기서 지정된 값을 갖게 된다.
* 클래스 내부 초기자가 없는 데이터 멤버는 0으로 초기화된다.

salary 데이터 멤버의 디폴트 값이 75,000

    struct Employee
    {
        char firstInitial;
        char lastInitial;
        int employeeNumber;
        int salary { 75'000 };
    };

앞에서는 이런 구조체를 균일 초기자로 초기화

    Employee anEmployee { 'J', 'D', 42, 80'000 };

지정 초기자로 초기화하면 다음과 같이 작성

    Employee anEmployee
    {
        .firstInitial = 'J',
        .lastInitial = 'D',
        .employeeNumber = 42,
        .salary = 80'000
    };

이렇게 지정 초기자를 사용하면 균일 초기자를 사용할 때보다 초기화할 대상을 훨씬 쉽게 파악

지정 초기자를 사용할 때 주어진 디폴트값을 사용하고 싶은 멤버에 대해 초기화를 생략 가능  
예를 들어 Employee 객체를 생성할 때 employeeNumber에 대한 초기화를 생략 가능    
이 멤버는 클래스 내부 초기자가 없기 때문에 0으로 초기화 

    Employee anEmployee
    {
        .firstInitial = 'J',
        .lastInitial = 'D',
        .salary = 80'000
    };

균일 초기자 구문을 사용할 때는 이렇게 생략할 수 없고, 다음과 같이 employeeNumber 자리에 0을 지정해야 함

salary 데이터 멤버를 다음과 같이 초기화하면 salary는 디폴트값을 갖게 됨     
이 멤버의 클래스 내부 초기화값에 의해 75,000이 됨

    Employee anEmployee
    {
        .firstInitial = 'J',
        .lastInitial = 'D',
    };

지정 초기자의 마지막 장점은 구조체에 멤버를 추가하더라도 지정 초기자를 이용한 코드는 그대로 작동    
새로 추가된 데이터 멤버는 디폴트값으로 초기화