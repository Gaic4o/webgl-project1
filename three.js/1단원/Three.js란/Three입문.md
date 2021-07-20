# Three.js 
## 공부하기 위해 오셨군요! 환영합니다.

`Three.js 는 페이지에 3D 객체를 만들 수 있는 라이브러리 입니다.`
씬, 광원, 그림자, 물체, 텍스처 등 모두 종합 해 3 차원 세계를 만들 수 있습니다.

(사진 추가) 


* Renderer : Three.js 핵심 객체.  Scene 과 Camera 객체를 넘겨 받아 2차원 적인 이미지로 렌더링 합니다.

* Scene Graph : 배경색(background-color), 안개(fog) 등의 요소를 포함. 
    `Scene 은 최상위 노드로 여기에 포함된 객체들은 부모/자식 트리 구조.`

* Camera :  말 그대로 카메라 역할 우리가 보는 시각. 꼭 필요하지는 않음. `다른 노드의 자식 관계가 될 떄 가 많다`

* Mesh : Geometry(사람 눈 코 입 피부 색) + Material(도형으로 사람 만든 모형) = Mesh(사람) 
        `이 외에도 Mesh 는 물체의 위치, 방향, 크기를 담은 객체 입니다!` 

* Geometry : Geometry 는 예로 들면 사람, 나무, 사과 등이 될 수 있음. 
        `즉 물체들 사각형, 직사각형, 삼각형, 색, 밝기 등을 조합 해 나무를 만들면 그것이 Geometry 입니다.`


* Material : 객체를 만드는 데 표면적인 속성. 
            `색, 밝기, 질감 등`

* Texture : Image 를 뜻합니다. `직사각형에 이미지를 넣고 싶다면? 이런 것들이 Texture에 해당`

* Light : 밝기 



Material (색, 밝기, 이미지) -> Mesh (정사각형) 
Material (색) -> Mesh (직사각형) * 4 
Material (색) -> Mesh (원)  

이것들 다 합쳐서 사람 모형을 만들 수 있음. - Geometry(사람) 

``` javascript
// 모듈 불러오기
import * as THREE from './resources/threejs/r127/build/three.module.js';

function main() {
    const canvas = document.querySelector('#c');
    const renderer = new THREE.WebGLRenderer({canvas});
}
```

`WebGLRenderer` 우리가 입력한 코드 (색, 밝기, 기하학 등) 를 canvas 에 그려주는 역할.





이제 카메라가 필요하니 PerspectiveCamera (원근 카메라) 객체를 생성 해 보자.

* fov : 도(degree) 좀 더 설명.
* aspect : canvas 사이즈. 내가 프로젝트 할 페이지에 Three.js canvas 공간을 얼마나 차지 할 것 인지.
* near :  눈에 보이는 시각 모서리.
* far  : 맨 뒤에 보이는 시각 모서리.

이 4 가지 속성으로 `절두체`를 만듭니다.

절두체라는 개념을 잘 모르시면 구글에 검색하셔서 보시면 됩니다.
제가 쉽게 알려 드릴 려고 했지만 수학 공식들이 나오고 내용이 워낙 어려워서 한 번씩 읽어 보세요!


near 와 far - 시야각(fov).
`우리가 보는 시야각에 따라 aspect 보는 도형의 너비가 다릅니다.`

기본 설정은 카메라 -Z 축, +Y 축, 아래를 봅니다.

``` javascript
camera.position.z = 3;
```

-z 기준으로 z 3 앞에 서서 Mesh(도형)을 봅니다.

`절두체는 카메라 앞 0.1 칸에서 부터 5칸 까지 차지합니다.`






이제 어느정도 개념을 익혔다고 생각 한다.

- Scene 을 만들어 봅시다!

Three.js 에서 Scene 은 `핵심 역할` 입니다.
화면에 무엇을 넣거나 렌더링 하고 싶으면 무조건 `Scene` 이 있어야 합니다.

``` javascript
const scene = new THREE.Scene();
```

