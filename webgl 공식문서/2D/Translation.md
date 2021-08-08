# WebGL 2D Translation 

Transition 은 `움직이는 걸` 의미하는 수학적 명칭입니다.


사각형의 translation, width, height, color 를 담을 변수를 만듭니다.

``` javascript
var translation = [0, 0];
var width = 100;
var height = 30;
var color = [Math.random(), Math.random(), Math.random(), 1];
```

그런 다음 전부 다시 그리는 함수를 만들어 보자.

Translation을 업데이트한 후에 이 함수를 호출할 수 있습니다.


``` javascript
// 장면 그리기.
function drawScene() {
    webglUtils.resizeCanvasToDisplaySize(gl.canvas);

    // Clip space에서 픽셀로 변환하는 방법을 WebGL에 지시.
    gl.viewport(0, 0, gl.canvas.width, gl.canvas.height); 

    // 캔버스 지우기.
    gl.clear(gl.COLOR_BUFFER_BIT); 

    // program(shader 쌍) 사용 지시.
    gl.useProgram(program);

    // position attribute 활성화.
    gl.enableVertexAttribArray(positionLocation); 

    // position buffer 할당.
    gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer); 

    // 사각형 설정.
    setRectangle(gl, translation[0], translation[1], width, height); 

    // positionBuffer(ARRAY_BUFFER)에서 데이터 가져오는 방법을 attribute 에 지시.
    var size = 2; // 반복마다 2개의 컴포넌트.
    var type = gl.FLOAT; // 데이터는 32bit float.
    var normalize = false; // 데이터 정규화 안 함.
    var stride = 0; // 0 = 다음 위치를 가져오기 위해 반복마다 size * sizeof(type) 만큼 앞으로 이동.
    var offset = 0;
    gl.vertexAttribPointer(positionLocation, size, type, normalize, stride, offset); 

    // 해상도 설정.
    gl.uniform2f(resolutionLocation, gl.canvas.width, gl.canvas.height); 

    // 색상 설정
    gl.uniform4fv(colorLocation, color); 

    // 사각형 그리기.
    var primitiveType = gl.TRIANGLES;
    var offset = 0;
    var count = 6; 
    gl.drawArrays(primitiveType, offset, count); 
}

```