# Initializer list

## Header

<initializer_list>

## 초기자 리스트

std::initializer_list 타입은 클래스 템플릿  
그러므로 vector에 저장할 객체의 타입을 지정할 때처럼 원소 타입에 대한 리스트를 꺾쇠괄호로 묶어서 지정해야 함

    #include <initializer_list>

    using namespace std;

    int makeSum(initializer_list<int> values)
    {
        int total { 0 };
        for (int value : values)
        {
            total += value;
        }

        return total;
    }

여기서 makeSum() 함수는 정수에 대한 초기자 리스트를 인수로 받음     
그리고 함수 안에서 범위 기반 for 문을 사용해서 인수로 주어진 정수들을 모두 더함     
이 함수는 다음과 같이 호출  

    int a { makeSum({ 1, 2, 3}) };
    int b { makeSum({ 10, 20, 30, 40, 50, 60, 60 }) };