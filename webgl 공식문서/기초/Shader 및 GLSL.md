# Shader 및 GLSL 

vertex shader, fragment shader 는 함께 `shader program`으로 연결됩니다.
`일반적 WebGL 앱은 많은 shader program을 가집니다.`



Vertex shader 

- clip space 좌표를 생성하는 것.
  
``` javascript
void main() {
    gl_Position = doMathToMakeClipspaceCoordinates 
}
```


삼각형 3개를 그린다고 하면
정점 3개. 

한 정점마다 `Shader` 가 호출됩니다.
호출될 떄 마다 특수 전역 변수, gl_Position을 어떤 clip space 좌표로 설정해야 합니다.


`Vertex shader는 데이터가 필요, 3 가지 방법으로 데이터를 가져올 수 있습니다.`

1. Attribute (버퍼에서 가져온 데이터)
2. Uniform (단일 그리기 호출의 모든 정점에 대해 동일 유지되는 값) 
3. Texture (pixel/texel의 데이터)


Attribute 

- buffer 와 attribute를 통하는 것.

``` javascript
// 버퍼를 만든 뒤.
var buf = gl.createBuffer();

// 버퍼 -> 데이터를 넣은 뒤
gl.bindBuffer(gl.ARRAY_BUFFER, buf);
gl.bufferData(gl.ARRAY_BUFFER, someData, gl.STATIC_DRAW); 

// 만든 shader program을 통해 초기화 시 attribute의 location 찾고.
var positionLoc = gl.getATtribLocation(someShaderProgram, "a_position"); 
```


렌더링할 떄 WebGL에 해당 버퍼에 attribute로 데이터를 어떻게 가져올지 지시함.

``` javascript
// 이 attribute에 대한 버퍼에서 데이터 가져오기 활성화.
gl.enableVertexAttribArray(positionLoc);

var numComponents = 3; // (x,y,z) 
var type = gl.FLOAT; // 32bit 부동 소수점 값.
var normalize = false; // 값 원본 그대로 유지.
var offset = 0; // 버퍼의 처음부터 시작.
var stride = 0; // 다음 정점으로 가기 위해 이동하는 byte 수.
                // 0 = type 과 numComponents 에 맞는 stride 사용.

gl.vertexAttribPointer(positionLoc, numComponents, type, false, stride, offset); 
```

``` javascript
attribute vec4 a_position;

void main() {
    gl_Position = a_position; 
} 
```

Clip space 정점을 버퍼에 넣으면 동작 할 겁니다.

Attribute는 type으로 float, vec2, vec3 .. mat4 를 사용 할 수 있습니다.



Uniform 

`Shader uniform은 그리기 호출의 모든 정점에 대해 똑같이 유지되며`
shader에 전달되는 값 입니다.

간단 예로 위 vertex shader에 offset을 추가할 수 있습니다.

``` javascript
attribute vec4 a_position;
uniform vec4 u_offset;

void main() {
    gl_Position = a_position + u_offset; 
}
```

먼저 초기화 시 uniform의 location을 찾아야 합니다.

``` javascript 
var offsetLoc = gl.getUniformLocation(someProgram, "u_offset"); 
```

그런 다음 그리기 전 uniform 을 설정합니다.

``` javascript
gl.uniform4fv(offsetLoc, [1, 0, 0, 0]); // 화면 오른쪽 절반으로 offset 
```

참고로 uniform은 개별 shader program에 속합니다.

만약 이름이 같은 uniform을 가진 shader program이 여러 개 있다면?
두 uniform 모두 고유 location과 값을 가집니다.

gl.uniform??? 을 호출 -> current program의 uniform만 설정합니다.
Current program은 gl.useProgram에 전달한 마지막 program 입니다.


uniform은 여러 type을 가질 수 있습니다.
각 type 마다 설정을 위해 해당하는 함수를 호출해야 합니다.

``` javascript
gl.uniform1f (floatUniformLoc, v);                 // float
gl.uniform1fv(floatUniformLoc, [v]);               // float 또는 float 배열
gl.uniform2f (vec2UniformLoc, v0, v1);             // vec2
gl.uniform2fv(vec2UniformLoc, [v0, v1]);           // vec2 또는 vec2 배열
gl.uniform3f (vec3UniformLoc, v0, v1, v2);         // vec3
gl.uniform3fv(vec3UniformLoc, [v0, v1, v2]);       // vec3 또는 vec3 배열
gl.uniform4f (vec4UniformLoc, v0, v1, v2, v4);     // vec4
gl.uniform4fv(vec4UniformLoc, [v0, v1, v2, v4]);   // vec4 또는 vec4 배열
 
gl.uniformMatrix2fv(mat2UniformLoc, false, [  4x element array ])  // mat2 또는 mat2 배열
gl.uniformMatrix3fv(mat3UniformLoc, false, [  9x element array ])  // mat3 또는 mat3 배열
gl.uniformMatrix4fv(mat4UniformLoc, false, [ 16x element array ])  // mat4 또는 mat4 배열
 
gl.uniform1i (intUniformLoc, v);                   // int
gl.uniform1iv(intUniformLoc, [v]);                 // int 또는 int 배열
gl.uniform2i (ivec2UniformLoc, v0, v1);            // ivec2
gl.uniform2iv(ivec2UniformLoc, [v0, v1]);          // ivec2 또는 ivec2 배열
gl.uniform3i (ivec3UniformLoc, v0, v1, v2);        // ivec3
gl.uniform3iv(ivec3UniformLoc, [v0, v1, v2]);      // ivec3 또는 ivec3 배열
gl.uniform4i (ivec4UniformLoc, v0, v1, v2, v4);    // ivec4
gl.uniform4iv(ivec4UniformLoc, [v0, v1, v2, v4]);  // ivec4 또는 ivec4 배열
 
gl.uniform1i (sampler2DUniformLoc, v);             // sampler2D (texture)
gl.uniform1iv(sampler2DUniformLoc, [v]);           // sampler2D 또는 sampler2D 배열
 
gl.uniform1i (samplerCubeUniformLoc, v);           // samplerCube (texture)
gl.uniform1iv(samplerCubeUniformLoc, [v]);         // samplerCube 또는 samplerCube 배열

```




`bool, bvec2, bvec3, bvec4 type도 있습니다.`
gl.uniform?f? 또는 gl.uniform?i? 함수를 사용합니다.

배열의 경우 모든 배열의 uniform 을 한번에 설정할 수 있습니다.

``` javascript
// shader
uniform vec2 u_someVec2[3];

// 초기화 시 javascript
var someVec2Loc = gl.getUniformLocation(someProgram, "u_someVec2"); 

// 렌더링할 떄
gl.uniform2fv(someVec2Loc, [1,2,3,4,5,6]); // u_someVec2의 전체 배열 설정.
```



하지만 배열의 개별 요소를 설정하고 싶다면?
- 각 요소의 location을 개별적으로 찾아야 합니다.

``` javascript
// 초기화 시 javascript 
var someVec2Element0Loc = gl.getUniformLocation(someProgram, 'u_someVec2[0]');
var someVec2Element1Loc = gl.getUniformLocation(someProgram, 'u_someVec2[1]');
var someVec2Element2Loc = gl.getUniformLocation(someProgram, 'u_someVec2[2]');


// 렌더링할 떄
gl.uniform2fv(someVec2Element0Loc, [1, 2]); // 요소 0 설정.
gl.uniform2fv(someVec2Element1Loc, [3, 4]); // 요소 1 설정.
gl.uniform2fv(someVec2Element2Loc, [5, 6]); // 요소 2 설정.
```


마찬가지로 struct를 생성하면.

``` javascript
struct Somestruct {
    bool active;
    vec2 someVec2;
}
uniform SomeStruct u_someThing; 
```


각 field를 개별적으로 찾아야 합니다.

``` javascript
var someThingActiveLoc = gl.getUniformLocation(someProgram, 'u_someThing.active'); 
var someThingSomeVec2Loc = gl.getUniformLocation(someProgram, 'u_someThing.someVec2'); 
```


Vertex shader의 텍스처.





<h2>Fragment shader</h2>

Fragment shader의 역할은 래스터화하는 현재 픽셀의 색상을 제공하는 것.
항상 이런 형식을 취합니다.

``` javascript
precision mediump float;

void main() {
    gl_FragColor = doMathToMakeAColor; 
}
```


Fragment shader 도 마찬가지로 한 번씩 호출됩니다.

- 호출될 떄 마다 특수 전역 변수, gl_FragColor 를 어떤 색상으로 설정해줘야 합니다.

Fragment shader는 데이터가 필요한데요, 3가지 방법으로 데이터를 가져올 수 있습니다.

1. Uniform (단일 그리기 호출의 모든 정점에 대해 동일 유지되는 값) 
2. Texture (pixel/texel의 데이터) 
3. Varying (vertex shader에서 전달되는 보관된 데이터) 




Fragment shader의 Uniform 

Fragment shader의 텍스처.

- Shader의 텍스처에서 값을 가져오면 `sampler2D` uniform 을 생성하고 값을 추출하기 위해 GLSL 함수 texture2D 를 사용합니다.

``` javascript
precision mediump float;

uniform sampler2D u_texture;

void main() {
    vec2 texcoord = vec2(0.5, 0.5); // 텍스처 중앙에서 값 가져오기.
    gl_FragColor = texture2D(u_texture, texcoord); 
}
```



Varying 

Varying 은 `vertex shader`에서 `fragment shader`로 값을 전달하는 방법.

Varying을 사용하면 `vertex shader와 fragment shader` 양쪽에 일치하는 varying 을 선언해야 함.

각 정점마다 vertex shader의 varying 을 어떤 값으로 설정합니다.
WebGL이 픽셀을 그릴 떄 이 값들 사이를 보관 후, fragment shader에서 대응하는 varying 으로 전달할 겁니다.



Vertex shader

``` javascript
attribute vec4 a_position;
uniform vec4 u_offset;
varying vec4 v_positionWithOffset;

void main() {
    gl_Position = a_position + u_offset;
    v_positionWithOffset = a_position + u_offset; 
}
```


Fragment shader 

``` javascript
precision mediump float; 
varying vec4 v_positionWithOffset; 


void main() {
    // clip space에서 (-1 <-> +1) color space로 (0 -> 1) 변환.
    vec4 color = v_positionWithOffset * 0.5 + 0.5
    gl_FragColor = color;
}
```



GLSL 

- Graphics Library Shader Language 의 약자.

그래픽을 래스터화하기 위한 계산을 하는데, 일반적 필요 수학적 계산을 하도록 설계되어 있습니다.

예를 들어 각각 2개의 값, 3개의 값, 4개의 값을 나타내는 vec2, vec3, vec4 같은 type 들이 내장되어 있습니다.

마찬가지 2x2, 3x3, 4x4 행렬을 나타내는 mat2, mat3, mat4 가 있습니다.
vec 에 스칼라를 곱하는 것 같은 작업을 수행 가능.

``` javascript
vec4 a = vec4(1, 2, 3, 4);
vec4 b = a * 2.0; 
// 현재 b는 vec4(2,4,6,8);
``` 



