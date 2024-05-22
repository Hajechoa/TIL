# enumerated type, enum

숫자를 나열하는 방식과 범위를 마음대로 정의해서 변수를 선언하는 데 활용 할 수 있음
예를 들어 체스 프로그램을 작성할 때 각 말의 종류를 다음과 같이 int 타입의 상수로 표현한다고 했을 때
말의 종류를 나타내는 정수 타입의 변수 앞에 const란 키워드를 붙이면 그 값이 변하지 않게 할 수 있음

    const int PieceTypeKing { 0 };
    const int PieceTypeQueen { 0 };
    const int PieceTypeRook { 0 };
    const int PieceTypePawn { 0 };
    // 기타
    int myPiece { PieceTypeKing };

이렇게 표현해도 되지만 자칫 위험한 상황이 발생할 수 있음
말을 표현하는 myPiece를 일반 int 값으로 표현했기 때문에 다른 프로그래머가 myPiece 값을 증가시키는 코드를 추가하면
킹을 나타내는 값에 1을 더하면 퀸이 되어버려 체크 게임이 망가짐
심지어 누군가 갑자기 말을 나타내는 값을 앞서 정의한 상수에 나오지 않는 -1과 같은 값으로 지정하면 더 심각한 문제가 발생할 수 있음

강타입 열거 타입(strongly typed enumeration type)을 적용하면 변수에 지정할 수 있는 값의 범위를 엄격하게 제한하기 때문에 이러한 문제를 방지할 수 있음
예를 들어 체스의 말을 표현하는 변수가 네 가지 말에 해당하는 값만 가지도록 제한하고 싶다면 다음과 같이 PieceType라는 enum 타입을 새로 정의
    
    enum class PieceType { King, Queen, Rook, Pawn };

이 새로운 타입은 다음과 같이 사용할 수 있음

    PieceType piece { PieceType::King };

enum 타입을 구성하는 멤버는 내부적으로 정숫값으로 표현됨
King, Queen, Rook, Pawn에 실제로 할당된 값은 각각 0, 1, 2, 3
이때 각 멤버마다 할당되는 값의 범위를 별도로 지정할 수 있음

    enum class PieceType
    {
        King = 1,
        Queen,
        Rook = 10,
        Pawn
    };

열거 타입의 멤버에 값을 따로 할당하지 않으면 컴파일러는 이전 멤버의 값에 1을 더한 값으로 알아서 할당
첫 번째 멤버 값을 지정하지 않았다면 컴파일러는 0으로 설정
위 예제에서는 King을 1로 지정했으므로 Queen의 값은 컴파일러가 2로 할당하고, Rook은 10이라고 지정했으므로
Pawn의 값은 컴파일러가 11로 할당

참고로 열거 타입의 값이 내부적으로 정수로 표현된다고 해서 자동으로 정수로 변환되지 않음
다음과 같이 작성하면 에러가 발생

    if(PieceType::Queen == 2) { ... }

기본적으로 열거 타입의 값은 정수 타입(int)으로 저장되지만 다음과 같이 내부 표현 타입을 다른 타입으로 바꿀 수 있음

    enum class PieceType : unsigned long
    {
        King = 1,
        Queen,
        Rook = 10,
        Pawn
    };

enum class로 정의한 열거한 타입 값의 스코프는 자동으로 확장되지 않음
다시 말해 상위 스코프에 똑같은 이름이 있더라도 충돌되지 않음
따라서 서로 다른 열거 타입에 동일한 이름의 멤버가 존재할 수 있음

    enum class State { Unknown, Started, Finished};
    enum class Errir { None, BadInput, DiskFull, Unknown, Started, Finished};

이런 원칙의 가장 큰 장점은 열거 타입의 값 이름을 짧게 지정할 수 있다는 것
예를 들어 UnknownError나 UnknownError나 UnknownState 대신 그냥 Unknown이라고 지정할 수 있음

하지만 열거값을 길게 풀어 쓰거나 using enum 또는 using 선언문을 적어줘야함

# C++20

C++20부터는 using enum으로 선언하면 열것값을 길게 풀어 쓰지 않아도 됨

    using enum PieceType;
    PieceType piece { King };

또한 using 선언문을 사용해도 열것값을 길게 풀어 쓰지 않아도 됨

    using PieceType::King;
    PieceType piece { King };
    piece = PieceType::Queen;

> C++20부터 열것값을 짧게 쓰도록 지원하지만 이 기능은 조심해서 사용해야 함
> 사용하더라도 using enum 또는 using 선언문의 스코프를 최소화하기 바람
> 스코프가 너무 크면 이름이 충돌하는 현상이 발생할 수 있음