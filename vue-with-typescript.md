# Vue 에 typescript 발라보기

그냥하면 재미없으니깐...

## 1. Vue 프로젝트 생성하기

기본 Vue 프로젝트를 생성한다. vue의 기본 가이드가 아주 잘 되어있으므로 생략

vue-cli 로는 아직 typescript vue 프로젝트를 생성할순 없다. vue 에서 곧 제공할 계획이라고 함!

그래서 좀 불편하지만, 무엇이 필요한지 어떤 과정이 필요한지 알아가기도 할 겸 일일이 수작업으로..

## 2. Vue 에 타입스크립트 바르기

### 2.1 npm 패키지 설치

#### typescript 설치 (로컬 프로젝트)
```
npm install typescript
```
#### ts-loader 설치
웹팩에서 타입스크립트를 자동으로 로드할 수 있는 라이브러리.
```
npm install ts-loader --save-dev
```
#### vue-class-component 설치
이 라이브러리는 뷰 컴포넌트를 정의할때 @Component 데코레이터 같은 것들을 이용해서 Vue 인스턴스를 클래스 형태로 작성할수 있게끔 도와준다.
```
npm install vue-class-component --save
```
(자세한 내용은 https://vuejs.org/v2/guide/typescript.html#Class-Style-Vue-Components 를 참조)

#### vue-property-decorator 설치
vue 컴포넌트의 prop 연결용. 이 라이브러리가 제공하는 @Prop를 이용하면 Class 내부에 선언된 변수를 props와 연결해 준다. 이외에도 Vue, Component 대신해서 import 해줌.
```
npm install vue-property-decorator --save-dev
```
#### vue-typescript-import-dts 설치
.vue 파일을 타입스크립트처럼 인식할 수 있게 하는 타입 정의 라이브러리

```
npm install vue-typescript-import-dts --save-dev
```

### 2.2 typescript 설정

#### tsconfig.json 파일 생성

프로젝트폴더 바로 아래에 tsconfig.json 파일 생성. 명령줄로 자동 생성할수 있지만 불필요한 설정이 다들어있어서.. 그냥 만든다.
```
{
  "compilerOptions": {
    "module": "commonjs", (2)
    "target": "es5", (1)
    "strict": true,
    "noImplicitReturns": true,
    "lib": [ (3)
      "dom",
      "es2015",
      "es2015.promise"
    ],
    "types": [
      "vue-typescript-import-dts" (4)
    ],
    "experimentalDecorators": true (5)
  },
  "include": [
    "src/**/*"
  ],
  "compileOnSave": false
}

```
* (1) 기본적으로 vue는 es5를 지원하므로 타입스크립트 컴파일도 역시 es5로 타겟팅. (2) 그래서 컴파일에 쓰이는 모듈도 commonjs를 사용. (3) 같은 이유.
* (4) types 항목은 타입 정의 항목을 지정하는 부분인데, .vue 파일들을 타입 정의할 수 있게 하기 위해 vue-typescript-import-dts 라이브러리 추가한 것을 여기에 적용시킨다.
* (5) 타입스크립트 안에서 클래스 지정 시 데코레이터를 사용할 수 있게 함

### 2.3 webpack.base.conf.js 설정 추가/변경
```
...

entry: {
    app: './src/main.ts' (1)
  },
...
resolve: {
    extensions: ['.ts', '.js', '.vue', '.json'], (2)
...

module: {
    rules: [
    ...

      {
        test: /\.ts$/,
        loader: 'ts-loader',
        exclude: /node_modules/,
        options: {
          appendTsSuffixTo: [/\.vue$/] (4)
        }
      }, (3)
...

```
* (1) main.js 이던 것을 main.ts 로 변경
* (2) extensions 에 ts 추가
* (3) rules에 ts-loader 추가. ts 파일을 ts-loader로 처리
* (4) vue 파일도 ts 파일인 것처럼 처리한다

### 2.4 기존 vue 파일 수정

#### 2.4.1 main.js -> main.ts 파일 수정
```
import Vue from 'vue'
import App from './App.vue'
import router from './router'

new Vue({
  el: '#app',
  router,
  render: h => h(App)
});
```
template, components 속성이 빠지고 render 함수를 추가. 타입스크립트로 이미 모든 것들이 컴파일 되어 있으므로 간단히 렌더링만 한다

#### 2.4.1 App.js -> App.ts 파일 수정
```
<script lang="ts"> (1)
  import { Vue, Component, Prop } from 'vue-property-decorator' (2)

  @Component (3)
  export default class App extends Vue {
    @Prop() name: string = 'App' (4)
  }
</script>
```
* (1) script 부분에 typescript 사용을 명시
* (2) vue, Component, Prop 패키지를 한방에 import
* (3) Vue 인스턴스를 @Component 데코레이터를 이용해 클래스 형태로 작성
* (4) Class 내부에 선언된 변수를 Component의 props와 연결해 준다

#### 2.4.2 HelloWorld.vue 수정해보기
```
<script lang="ts">
  import {Vue, Component, Prop} from 'vue-property-decorator'

  @Component
  export default class Hello extends Vue {
    @Prop() name: string = 'HelloWorld'
    msg: string = 'Welcome to Your Vue.js App'
  }
</script>
```
이렇게 되었다!

#### 준비 끝!
