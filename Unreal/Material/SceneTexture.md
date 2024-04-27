# SceneDepth

ceneDepth 는 기존의 씬 깊이를 출력하는 표현식입니다. PixelDepth 와 비슷하나, PixelDepth 는 현재 그려지고 있는 픽셀의 깊이만 샘플링할 수 있는 반면, SceneDepth 는 어느 위치의 깊이도 샘플링할 수 있습니다.

# CustomDepth

이 별도의 기능으로 특정 오브젝트들을 (커스텀 뎁스 버퍼라는) 다른 뎁스 버퍼에 렌더링하여 마스킹 가능합니다. 드로 콜은 추가되겠지만 머티리얼이 들지는 않습니다. 뎁스만 출력하기에 렌더링 비용은 꽤나 쌉니다. 이 기능은 메시상에서 활성화 가능합니다 (예: Static Mesh Properties / Render Custom Depth)