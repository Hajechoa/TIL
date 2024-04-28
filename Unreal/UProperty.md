# Transient

CurrentHP 같은 값은 게임을 시작할 때마다 변경되므로 이 값을 보관하는 것은 의미가 없고 오히려 오브젝트를 저장할 때 필요 없는 디스크 공간만 차지한다. 이러한 속성에는 Transient 키워드를 추가해 해당 속성을 직렬화에서 제외시키는 것이 좋다.

# Meta

## EditCondition

    meta = (EditCondition = "bConditionVar")

EditCondition 설정한 변수가 True면 해당 변수가 노출되고 False면 노출되지 않는다.
