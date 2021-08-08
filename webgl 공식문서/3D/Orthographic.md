# 3D Orthographic 

- 행렬을 이용해서도 3D 를 만들 수 있습니다.

2D 예제에서 3x3 행렬을 곱한? 2D Point(x,y) 를 가집니다.
3D 예제에서도 4x4 행렬을 이용 하여 3D Point(x,y,z) 를 가질 수 있습니다. 

`F 를 만들어 봅시다!`

일단 vertex shader 를 만들어 봅시다.

``` javascript
<script id="vertex-shader-3d" type="x-shader/x-vertex">
attribute vec4 a_position; 

uniform mat4 u_matrix; 

void main() {
    // 위치 행렬 곱하기.
    gl_Position = u_matrix * a_position; 
}
</script>
```



``` javascript
// F 를 만들어 봅시다.

let size = 3; // 반복마다 3개 컴포넌트. 
let type = gl.FLOAT; // 데이터는 32bit float 
let normalize = false;  // 데이터 정규화 안 함.  
let stride = 0; // 0 = 다음 위치를 가져오기 위해 반복마다 size * sizeof(type) 만큼 앞으로 이동.  
let offset = 0;  // 버퍼의 처음부터 시작. 

gl.vertexAttribPointer(positionAttributeLocation, size, type, normalize, stride, offset); 

// 현재 ARRAY_BUFFER 버퍼 채우기.
// 문자 F 정의하는 값으로 버퍼 채우기.

function setGeometry(gl) {
    gl.bufferData(
        gl.ARRAY_BUFFER,
        new Float32Array([
            // 왼쪽 열.
            0, 0, 0,
            30, ..

            // 상단 가로 획.

            // 중단 가로 획. 
        ]),
        gl_STATIC_DRAW
    );
}
```


3D 버전 

``` javascript
let m4 = {
    translation: function(tx, ty, tz) {
        return [
            1, 0, 0, 0,
            0, 1, 0, 0, 
            0, 0, 1, 0,
            tx, ty, tz, 1
        ];
    }, 

    xRotation: function(angleInRadians) {
        let c = Math.cos(angleInRadians);
        let s = Math.cos(angleInRadians); 

        return [
            1, 0, 0, 0,
            0, c, s, 0,
            0, -s, c, 0,
            0, 0, 0, 1,
        ];
    },

    yRotation: function(angleInRadians) {
        let c = Math.cos(angleInRadians);
        let s = Math.sin(angleInRadians); 

        return [
            c, 0, -s, 0,
            0, 1, 0, 0, 
            s, 0, c, 0,
            0, 0, 0, 1,
        ];
    },

    zRotation: function(angleInRadians) {
        let c = Math.cos(angleInRadians); 
        let s = Math.sin(angleInRadians); 

        return [
            c, s, 0, 0,
            -s, c, 0, 0,
            0, 0, 1, 0,
            0, 0, 0, 1,
        ];
    },

    scaling: function(sx, sy, sz) {
        return [
            sx, 0, 0, 0,
            0, sy, 0, 0,
            0, 0, sz, 0,
            0, 0, 0, 1
        ];
    },
};
```


이제 3D 에서는 x, y, z 중심으로 다 회전 합니다.
`즉 3개의 rotation 함수를 가진다는 점. `

Z 회전 

newX = x * c + y * s;
newY = x * -s + y * c; 

Y 회전 

newX = x * c + z * s;
newZ = x * -s + z * c; 

X 회전 

newY = y * c + z * s;
newZ = y * -s + z * c; 



단순화된 함수들을 만들어 봅시다! 

translate: function(m tx, ty, tz) {
    return m4.multiply(m, m4.translation(tx, ty, tz)); 
},

xRotate: function(m, angleInRadians) {
    return m4.multiply()
}