# typedef

타입 앨리어스는 C++11부터 도입  
그전에는 타입 앨리어스로 하는 일을 typedef로 구현해야 했는데 코드가 다소 복잡했음   
오래된 방식이긴 하지만 아직까지 레거시 코드에서 종종 볼 수 있음 

typedef도 타입 앨리어스와 마찬가지로 기존에 선언된 타입에 다른 이름을 붙임  
예를 들어 지금은 다음과 같이 타입 앨리어스를 정의   

    using IntPtr = int*

이 타입 앨리어스 정의를 typedef로 표현하면 다음과 같음  

    typedef int* IntPtr;

보면 알겠지만 가독성이 훨씬 떨어짐  
선언하는 순서가 반대라서 전문 C++ 프로그래머도 헷갈리기 쉬움    
코드가 좀 더 지저분해진다는 점을 제외하면 typedef는 기본적으로 타입 앨리어스와 같음     
예를 들어 이렇게 typedef 문을 선언한 뒤에 다음과 같이 활용할 수 있음    

    IntPtr p;

타입 앨리어스와 typedef가 완전히 똑같은 것은 아님   
템플릿에 활용할 때는 typedef보다 타입 앨리어스를 사용하는 것이 훨씬 유리    

> typedef보다는 타입 앨리어스를 사용하기 바란다.