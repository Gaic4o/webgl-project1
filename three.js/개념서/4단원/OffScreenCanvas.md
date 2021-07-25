# OffscreenCanvas 

- 3D 장면등 무거운 작업을 할 떄 브라우저가 버벅 일 수 있는데.
    이것을 사용하면 페이지 로드 시 버벅임을 훨씬 줄일 수 있습니다.

일단 `canvas.transferControlToOffscreen` 메서드를 호출해 canvas 의 제어권을 offscreen 에 넘겨 줍니다. 

``` javascript
function main() {
    const canvas = document.querySelector('#c'); 
    const offscreen = canvas.transferControlToOffscreen(); 
}
```

new Worker(워커 스크립트 경로, { type: 'module' }) 로 워커를 생성 한 뒤, 워커에 offscreen 객체를 넘깁니다. 



그리고 new Worker(워커 스트립트 경로, { type: 'module' }) 로 워커를 생성 한 뒤, 워커에 offscreen 객체를 넘깁니다.

``` javascript
function main() {
    const canvas = document.querySelector('#c');
    const offscreen = canvas.transferControlToOffscreen();
    const worker = new Worker('offscreencanvas-cubes.js', { type: 'module' });
    worker.postMessage({ type: 'main', canvas: offscreen }, [offscreen]); 
}
main(); 
```



스크립트를 따로 써야 하는 이유 -> 워커 안에서 DOM에 접근할 수 없기 떄문입니다.
HTML 요소를 참조하거나 DOM 요소의 이벤트를 받을 수는 없습니다.

워커에 메시지를 보내려면 ?

- worker.postMessage 에 `하나 또는 두 개의 인자를 넘겨 주어야 합니다.`

1. 워커에 전달할 객체
2. 첫 번쨰 인자 중 그대로 전달하기 원하는 객체를 배열로 지정 합니다.


이것도 모든 객체를 전달할 수 있는 게 아닌, 특정 타입의 객체만을 전달 가능 합니다.


워커의 message event를 이용하면 메시지를 받을 수 있습니다.
postMessage 에서 넘긴 객체는 event.data 에 담겨 리스너에 전달됩니다.

type: 'main' 속성을 객체에 선언 해 워커에 넘거줬습니다.

``` javascript
const handlers = {
    main, 
}; 

self.onmessage = function(e) {
    const fn = handler[e.data.type];
    if (!fn) {
        throw new Error('no handler for type: ' + e.data.type); 
    }
    fn(e.data); 
}
```


type 값을 통해 호출할 함수를 찾고, 함수가 있다면 메인 스크립트에서 넘어온 data를 인자로 넘겨 호출하도록 하자.



``` javascript
function main(data) {
    const { canvas } = data;
    const renderer = new THREE.WebGLRenderer({ canvas }); 
}
```


워커에선 DOM에 접근할 수 없음.
- 