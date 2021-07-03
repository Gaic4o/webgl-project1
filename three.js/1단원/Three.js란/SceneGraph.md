# 씬 그래프.


지구는 태양을 중심으로 공전.

- 달은 지구를 중심으로 공전.



`달의 관점에서 보면 지구의 "지역 공간" 안에서 공전하는 셈입니다.`
우리의 삶은 `지역 공간` 안에서 이루어집니다.

``` javascript
// 회전값을 업데이트 할 객체들.
const objects = [];

// 하나의 geometry 로 만든 태양, 지구, 달을 생성.
const radius = 1;
const widthSegments = 6;
const heightSegments = 6;
const sphereGeometry = new THREE.SphereGeometry(
    radius, widthSegments, heightSegments); 
)

const sunMaterial = new THREE.MeshPhongMaterial({ emissive: 0xFFFF00 });
const sunMesh = new THREE.Mesh(sphereGeometry, sunMaterial); 
sunMesh.scale.set(5,5,5); // 태양의 크기를 키움. 
scene.add(sunMesh);
objects.push(sunMesh); 
```


태양의 mesh를 5배로 설정 해 줍니다.

MeshPhongMaterial 의 emissive 를 속성(property) 을 노랑으로 지정.
퐁-메터리얼의 emissive 속성은 빛을 반사하지 않은 표면 색상으로, 대신 광원에 해당 색상이 더해 집니다.

씬 가운데 조명 하나를 더 추가 합니다 (`광원`)

``` javascript
{
    const color = 0xFFFFFF;
    const intensity = 3;
    const light = new THREE.PointLight(color, intensity); 
    scene.add(light); 
}

```


이제 그 다음 할 일은 예제를 쉽게 확인하기 위해
`카메라를 중점 바로 위에서 아래로 내려다보게 설치합니다.`


카메라 시점을 바꾸는 가장 간단 방법 - `lookAt` 메서드를 활용하는 것.


대부분의 경우 양의 y(positive y) 방향을 위로 설정하면 되지만,
예제의 경우 위에서 아래를 내려다 볼 것이므로 양의 z 방향이 위가 됩니다.


``` javascript

const camera = new THREE.PerspectiveCamera(fov, aspect, near, far);
camera.position.set(0, 50, 0);
camera.up.set(0, 0, 1);
camera.lookAt(0, 0, 0);

```


이전 예제처럼 렌더링 루프에서 objects 배열의 모든 객체를 회전시킵니다.

``` javascript

objects.forEach((obj) => {
    obj.rotation.y = time; 
})

```
sunMesh 를 objects 배열 안에 넣어놨으므로 태양 모델이 회전하는 것을 확인 할 수 있습니다.
