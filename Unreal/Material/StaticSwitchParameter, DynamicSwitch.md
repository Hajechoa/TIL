출처 : https://udn.unrealengine.com/s/question/0D5QP000005wFrO0AU/intended-use-of-bdynamicswitchparameter-on-static-switch-parameters

# Static Switch Parameter의 동적 분기

"bDynamicSwitchParameter"는 생성된 HLSL 코드가 HLSL 생성 시(false) 또는 셰이더 실행 중에(true) 스위치를 평가할지 여부를 제어합니다.

false인 경우 HLSL 생성 시 스위치가 평가되고 값을 변경하는 UMaterialInstances에 대해 다른 Permutation이 만들어집니다. 

true인 경우 스위치는 HLSL 코드에서 분기로 구현됩니다. 

스위치 값을 변경하는 UMaterialInstance가 있어도 컴파일할 Permutation 세트가 달라지지는 않습니다.

<details>
<summary>원본</summary>

“bDynamicSwitchParameter” controls if the generated HLSL code will evaluate the switch at time of HLSL Generation (false), or during shader execution (true).

When it is false, the switch is evaluated at the time of HLSL Generation, and different permutations are made for UMaterialInstances that change the value.

 When true, the switch is implemented in the HLSL code as a branch.
 
Having a UMaterialInstance that changes the value of the switch does not result in a different set of permutations to compile.
</details>