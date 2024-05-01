# new nothrow

    int* ptr { new(std::throw) int };

기본적으로 new를 사용하여 동적 메모리를 할당 받을 때 메모리가 부족하면 익셉션이 발생하는데 nothrow를 사용하면 nullptr을 반환