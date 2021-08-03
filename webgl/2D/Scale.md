# Scale 

Scale로 위치를 곱하면 됩니다. 이전 예제에서 변경된 사항들은 다음과 같습니다.


``` javascript
attribute vec2 a_position;

uniform vec2 u_resolution;
uniform vec2 u_rotation;
uniform vec2 u_scale; 

void main() {
    // 위치 scale하기.
    vec2 scaledPosition = a_position  u_scale; 

    // 위치 회전.
    vec2 rotatedPosition = vec2(
        scaledPosition.x * u_rotation.y + scaledPosition.y * u_rotation.x,
        scaledPosition.y * u_rotation.y - scaledPosition.x * u_rotation.x); 

        // Translation 추가.
        vec2 position = rotatedPosition + u_translation; 
    )
}
```


그리고 그릴 떄 scale 하기 위해 필요한 javascript 를 추가합니다.


``` javascript

var scaleLocation = gl.getUniformLocation(program, "u_scale");
var scale = [1, 1];

// 장면 그리기.
function drawScene() {

    // Translation 설정.
    gl.uniform2fv(translationLocation, translation);

    // Rotation 설정.
    gl.uniform2fv(rotationLocation, rotation); 

    // scale 설정.
    gl.uniform2fv(scaleLocation, scale); 

    // Geometry 그리기.
    var primitiveType = gl.TRIANGLES;
    var offset = 0;
    var count = 18 
    gl.drawArrays(primitiveType, offset, count); 
}

```