# WebGL 2D Rotation 

`단위원` 불리는 걸 소개.

참고로 파란색 핸들을 원 주위로 드래그하면 X와 Y의 위치가 변경됩니다.
이것들은 원 위에 있는 점의 위치를 나타냅니다.

위쪽일 떄 Y는 1이고 X는 0입니다.
오른쪽일 떄 X는 1이고 Y는 0입니다.

``` javascript
attribute vec2 a_position;

uniform vec2 u_resolution;
uniform vec2 u_translation;
uniform vec2 u_rotation;

void main() {
    // 위치 회전.
    vec2 rotatedPosition = vec2(
        a_position.x = u_rotation.y + a_position.y * u_rotation.x, 
        a_position.y = u_rotation.y - a_position.x * u_rotation.x); 

        // Translation 추가.
        vec2 position = rotatedPosition + u_translation; 
    )
}
```

javascript 를 업데이트해 두 값을 전달 가능.


``` javascript
var rotationLocation = gl.getUniformLocation(program, 'u_rotation'); 
var rotation = [0, 1];

// 장면 그리기.
function drawScene() {

    // Translation 설정.
    gl.uniform2fv(translationLocation, translation); 

    // Rotation 설정.
    gl.uniform2fv(rotationLocation, rotation); 

    // Geometry 그리기.
    var primitiveType = gl.TRIANGLES

    // Rotation 설정.
    gl.uniform2fv(rotationLocation, rotation); 

    // Geometry 그리기.
    var primtiveType = gl.TRIANGLES;
    var offset = 0;
    var count = 18;
    gl.drawArrays(primtiveType, offset, count); 
}
```
