# Three-way Comparsion Operator

## Header

\<compare>

## 3항 비교 연산자 

두 값의 순서를 결정하는데 사용  
연산자 기호인 <=>가 우주선 처럼 생겼기 때문에 우주선 연산자(spaceship operator)라 불리기도 함   
주어진 표현식의 평가 결과가 비교 대상이 되는 값과 같은지 아니면 그보다 크거나 작은지 알림   
true나 false가 아닌 세 가지 결과 중 하나를 알려줘야 하기때문에 부울 타입을 리턴할 수 없어 열거 타입을 리턴  
피연산자가 정수 타입이면 강한 순서라고 부름

* strong_ordering::less: 첫 번째 피연산자가 두 번째 피 연산자보다 작다.
* strong_ordering::grater: 첫 번째 피연산자가 두 번째 피 연산자보다 크다.
* strong_ordering::equal: 두 연산자가 같다.

피연산자가 부동소수점 타입이면 부분 순서

* partial_ordering::less: 첫 번째 피연산자가 두 번째 피여연산자보다 작다.
* partial_ordering::greater: 첫 번째 피연산자가 두 번째 피 연산자보다 크다.
* partial_ordering::equivalent: 두 연산자가 같다.
* partial_ordering::unordered: 두 피연산자 중 하나는 숫자가 아니다.

    int i { 11 };
    strong_ordering result { i <=> 0 };     
    if (result == strong_ordering::less) { cout << "less" << endl; };           
    if (result == strong_ordering::greater) { cout << "greater" << endl; };     
    if (result == strong_ordering::equal) { cout << "equal" << endl; };

피연산자가 자신이 직접 정의한 타입이면 약한 순서

* weak_ordering::less: 첫 번째 피연산자가 두 번째 피연산자보다 작다.
* weak_ordering::greater: 첫 번째 피연산자가 두 번째 피연산자보다 크다.
* weak_ordering::equivalent: 두 연산자가 같다.

# Named Comparison Function

## Header

\<compare>

## 이름 있는 비교 함수

std::is_eq(), is_neq(), is_lt(), is_lteq(), is_gt(), is_gteq()를 제공   
각각은 ==, != <, <=, >, >=로 비교한 결과를 true나 false로 리턴

    int i { 11 };
    strong_ordering result { i <=> 0 };
    if (is_lt(result)) { cout << "less" << endl; };
    if (is_gt(result)) { cout << "greater" << endl; };
    if (is_eq(result)) { cout << "equal" << endl; };