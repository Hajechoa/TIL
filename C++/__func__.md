# \_\_func__

모든 함수는 내부적으로 __func__라는 로컬 변수가 정의되어 있음   
이 변수의 값은 현재 함수의 이름이며, 주로 로그를 남기는데 활용

    int addNumbers(int number1, int number2)
    {
        cout << "Entering function " << __func__ << std::endl;
        return number1 + number2;
    }
