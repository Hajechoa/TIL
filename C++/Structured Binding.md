# Structured Binding

구조작 바인딩을 이용하면 여러 변수를 선언할 때 array, struct, pair 등에 담긴 원소들을 이용하여 변숫값을 한꺼번에 초기화할 수 있음

예를 들어 다음과 같이 배열이 정의되어 있음

    array values { 11, 22, 33 };

이 상태에서 x, y, z 변수를 선언할 때 각 초기값을 values 배열에 담아서 초기화할 수 있음  
구조적 바인딩을 적용하려면 반드시 auto 키워드를 붙여야 함   
예를 들어 auto 자리에 int를 지정하면 안됨   

    auto [x, y, z] { values };

구조적 바인딩에서 왼쪽에 나온 선언할 변수 개수와 오른쪽에 나온 표현식에 담긴 값의 개수는 반드시 일치해야 함

구조적 바인딩은 배열뿐만 아니라 비 static 멤버가 모두 public으로 선언된 구조체에도 적용할 수 있음

    struct Point { double m_x, m_y, m_z; };
    Point point;
    point.mx = 1.0; point.m_y = 2.0; point.m_z = 3.0;
    auto [x, y, z] { point };

마지막으로 다음 예를 보면 이 코드는 pair의 원소를 여러 변수로 나누고 있음

    pait myPair { "hello", 5 };
    auto [theString, theInt] { myPair }; // 구조적 바인딩을 이용해 나누기
    cout << format("theString: {}", theString) << endl;
    cout << format("theInt: {}", theInt) << endl;

이때 그냥 auto라 쓰지 않고 auto&나 const auto&를 이용하여 구조적 바인딩으로 비 const에 대한 레퍼런스나 const에 대한 레퍼런스를 생성할 수도 있음