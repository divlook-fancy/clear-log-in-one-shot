# console.log 한방에 지우기

console.log는 사용하기 간편하여 자바스크립트로 개발할 때 많이 사용되는데요.

보통 개발할 때만 사용하고 상용(production)에 배포할 때는 모두 지워서 배포합니다.

console.log를 반드시 지워야되는 건 아니지만 남겨두게 된다면 웹사이트에 접속한 누구나 개발자 도구를 통해 쉽게 log를 볼 수 있게되니까 되도록 지우도록 합시다.

신입때 팀장님이 console.log를 전부 지우라고해서 무식하게 일일이 지웠던 기억이 있는데요... 작업할 때는 console.log를 다시 입력하고, 배포할 때 다시 지우고... 정말 귀찮...

## 소개

[babel-plugin-transform-remove-console](https://www.npmjs.com/package/babel-plugin-transform-remove-console)이라는 [babel](https://babeljs.io/docs/en/next/babel-cli.html) 플러그인을 사용할건데요. [webpack](https://webpack.js.org/)과 연동만 해주면 자동으로 모든 console.log를 지워주는 멋진 플러그인입니다. webpack을 사용하지 않는 프로젝트에서도 babel만 설치하여 사용할 수 있습니다.

## 준비물

- NodeJs 최신버전(LTS) : [https://nodejs.org/](https://nodejs.org/)
- 삽질할 시간 (필수입니다 ^^)

## 방법

```bash
mkdir clear-log-in-one-shot
cd clear-log-in-one-shot
npm init -y
```

> 프로젝트 생성

```
npm install --save-dev webpack webpack-cli babel-loader @babel/core
npm install --save-dev babel-loader @babel/core
npm install --save-dev babel-plugin-transform-remove-console
```

> webpack, babel, transform-remove-console 설치

```js
const path = require('path')

module.exports = {
    mode: 'production',
    entry: './src/index.js',
    output: {
        filename: 'main.js',
        path: path.resolve(__dirname, 'dist'),
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                loader: 'babel-loader',
            },
        ],
    },
}
```

> webpack.config.js 생성 (웹팩설정파일)

```json
{
    "plugins": [
        ["transform-remove-console", { "exclude": [ "error", "warn"] }]
    ]
}
```

> .babelrc 추가 (바벨설정파일)

```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>console.log 한방에 지우기</title>
    <script src="main.js"></script>
</head>
<body>
    <h1>console.log 한방에 지우기</h1>
    <p>F12 or cmd+alt+i를 누르고 console을 확인해보세요.</p>
</body>
</html>
```

> dist/index.html 생성

```js
console.log('console.log')
console.warn('console.warn')
console.error('console.error')
console.dir('console.dir')
console.info('console.info')
```

> src/index.js 생성

```bash
npx webpack
```

> webpack 빌드 (자동으로 dist/main.js 생성)

## 결과확인

이제 dist/index.html을 열고 F12 or cmd+alt+i를 누르면 console에 console.error와 console.warn만 남아 있을겁니다.

```json
{
    "plugins": [
        ["transform-remove-console"]
    ]
}
```

> 만약 console.error와 console.warn도 지우고 싶다면 .babelrc를 위와 같이 수정하시면 됩니다.

## 연습용 소스

위 소스는 전부 github에 올려 놓았습니다. 빠르게 연습해보고 싶으신 분들은 git을 pull 받아서 `dist/main.js`만 지우시고 `npm i && npx webpack`을 실행하시면 됩니다.

- https://github.com/divlook/clear-log-in-one-shot

## 빌드를 했는데 console이 지워지지 않았을 때

빌드를 시작하기 전에 `node_modules/.cache` 디렉토리를 지워주면 잘 동작합니다.

babel 및 typescript는 내부적으로 이미 컴파일된 파일이 다시 컴파일 되지 않도록 설정되어 있습니다.

console을 지우지 않는 모드를 development라 하고 console을 지우는 모드를 production이라 할 때

development로 빌드를 실행한 뒤 파일내용의 변동사항이 없으면 이미 캐싱된 파일이 있기때문에 컴파일이 무시되게 됩니다.

```bash
# 맥, 리눅스
rm -rf node\_modules/.cache
# 윈도우
rimraf -rf ./node\_modules/.cache
```

> 참고 : https://cli.vuejs.org/guide/cli-service.html#caching-and-parallelization

# 참고사이트

- [https://babeljs.io/docs/en/babel-plugin-transform-remove-console](https://babeljs.io/docs/en/babel-plugin-transform-remove-console)
- [https://cli.vuejs.org/guide/cli-service.html#caching-and-parallelization](https://cli.vuejs.org/guide/cli-service.html#caching-and-parallelization)
