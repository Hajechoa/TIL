# std::optional

## Header

\<optional>

## std::optional

특정한 타입의 값을 가질 수도 있고, 아무 값도 가지지 않을 수도 있음  
기본적으로 함수 매개변수에 전달된 값이 없을 수도 있는 상황에 사용되고 값을 리턴할 수도 있고 그렇지 않을 수도 있는 함수의 리턴 타입으로 사용되기도 함    
기존에 리턴 값이 없는 경우를 표현하기 위한 nullptr, end(), -1, EOF와 같은 특수한 값을 사용하지 않아도 됨    
또한 함수의 리턴값은 수행 결과의 성공 여부를 나타내는 부울 타입으로 표현하고, 실제 결과는 출력용 매개변수를 이용하여 함수의 인수에 전달하는 식으로 작성하지 않아도 됨

클래스 템플릿으르모 optional\<int>와 같이 실제 타입을 꺾쇠괄호 안에 반드시 지정해야 함

    optional<int> getData(bool giveIt)
    {
        if (giveIt)
        {
            return 42;
        }

        return nullopt; // 또는 그냥 {};
    }

    optional<int> data1 { getData(true) };
    optional<int> data2 { getData(false) };

optional에 값이 있는지 확인하려면 has_value() 메서드를 사용하거나, 간단히 다음과 같이 if 문을 사용

    cout << "data1.has_value = " << data1.has_value() << endl;
    if (data2)
    {
        cout << "data2 has a value." << endl;
    }

optional에 값이 있을 때는 value()나 역참조 연산자로 그 값을 가져올 수 있음

    cout << "data1.value = " << data1.value() << endl;
    cout << "data1.value = " << *data1 << endl;

값이 없는 optional에 대해 value()를 호출하면 std::bad_optional_access 익셉션이 발생함

value_or()을 사용하면 optional에 값이 있을 때는 그 값을 리턴하고, 값이 없을 때는 다른 값을 리턴함

    cout << "data2.value = " << data2.value_or(0) << endl;