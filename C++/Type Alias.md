# type alias

타입 앨리어스란 기존에 선언된 타입에 다른 이름을 붙이는 것  
타입을 새로 정의하지 않고 기존 타입 선언에 대한 동의어를 선언하는 문법이라 생각할 수 있음   
예를 들어 다음 문장은 int*라는 타입 선언을 IntPtr이란 새 이름으로 부르게 만듬   

    using IntPtr = int*;

이렇게 정의한 앨리어스는 기존 타입 표현 대신 쓸 수 있게 됨. 
예를 들어 다음 두 문장은 같음

    int* p1;
    IntPtr p2;

새로 정의한 타입 이름으로 생성한 변수는 기존 타입 표현으로 생성한 변수와 완벽히 호환됨  
따라서 위와 같이 정의한 상태에서 다음과 같이 작성해도 아무런 문제가 없음    
단순히 호환되는 게 아니라 두 타입이 완전히 똑같기 때문  

    p1 = p2;
    p2 = p1;

타입 앨리어스는 너무 복잡하게 선언된 타입 표현을 좀 더 간편하게 만들기 위한 용도로 많이 사용    
흔히 템플릿을 이용할 때 이런 경우가 많음    
예를 들어 표준 라이브러리에서 제공하는 스트링은 std::basic_string<T>와 같이 작성해야 함     
이 표현은 템플릿 클래스로서, 여기 나온 T는 스트링을 구성하는 각 문자의 타입(예:char)을 가리킴   
이런 타입을 언급할 때마다 반드시 템플릿 매개변수도 함께 지정해야 함     
변수를 선언할 때뿐만 아니라 함수 매개변수나 리턴 타입을 지정할 때도 basic_string<char>와 같이 적어줘야 함   

    void processVector(const vector<basic_string<char>>& vec) { /* ... */ }

    int main()
    {
        vector<basic_string<char>> myVector;
        processVector(myVector);
    }

이처럼 basic_string<char>를 사용할 일이 많기 때문에 표준 라이브러리는 다음과 같이 간결하고 의미가 분명히 드러나는 타입 앨리어스를 제공  

    using string = basic_string<char>;

이렇게 타입 앨리어스를 이용하면 앞에 나온 코드를 다음과 같이 보다 세련되게 작성할 수 있음   

    void processVector(const vector<string>& vec) { /* ... */ }

    int main()
    {
        vector<string> myVector;
        processVector(myVector);
    }