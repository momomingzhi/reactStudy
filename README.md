---
# Webpack, Babel 이용해서 React 초기 환경 셋팅하기
1. 파일 초기화 시키기
    mkdir app && cd app
    npm init -y
2. 바벨 설정
바벨은 개발 단계에서만 사용하기 때문에 -D 옵션으로 devDependencites에 추가
    npm i -D @babel/core @babel/preset-env @babel/preset-react
3. .babelrc 파일 만들어서 코드 추가
    {
    "presets": [
        "@babel/preset-env",
        "@babel/preset-react"
    ]
    }
4.웩팩 설정
* webpack: 모듈 번들러인 웹팩
* webpack-cli: 웹팩의 커맨드 라인 인터페이스
* webpack-dev-server: 개발 서버 제공
    npm i -D webpack webpack-cli webpack-dev-server
5.webpack.config.js 만들기
* entry: application 진입점
* output: 번들된 파일 저장할 경로 나타냄
* filename: 번들된 파일 이름 나타냄 . [hash]는 application이 컴파일 될 때 웹팩에서 생성된 해시 사용
    module.exports = {
        mode: 'development', // 1
        entry: './src/index.js', // 2
        output: { // 3
            filename: 'bundle.[hash].js' // 4
        },
    };
6. package.json 에서 웹팩 명령어를 추가
    "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack"
    },
7. src/index.js 코드 작성
    console.log(1);
8. npm start 로 웹팩을 실행시키면 dist 폴더와 bundle.[hash].js 파일이 생성
.
+-- app 
|  +-- dist 
   |  +--bundle.b5c7e3feb71b5d2c8cab.js 이런식으로

9. loader 사용
* babel-loader: es6를 es5로 바꿔주는 바벨을 웹팩에서 사용할 수 있게 해줌
* html-loader: 웹팩이 html 읽을 수 있게 해줌
    npm i -D babel-loader html-loader
* 정의
    module: {
    rules: [
        {
        test: '빌드할 파일 확장자 정규식'
        exclude: '제외할 파일 정규식'
        use: {
            loader: '사용할 로더 이름'
            option: '로더 옵션'
        }
        }
    ]
    }
10. webpack.config.js에 코드 추가
    module.exports = {
    // ...
    module: {
        rules: [
        { // 1
            test: /\.(js|jsx)$/,  //.js .jsx 확장자 같이 번들
            exclude: /node_modules/, //node_modules안에 있는 파일은 번들에서 제외
            use: {
            loader: 'babel-loader',
            },
        },
        { // 2
            test: /\.html$/,
            use: [
            {
                loader: 'html-loader',
                options: {
                minimize: true,
                },
            },
            ],
        },
        ],
    },
11. plugin
* html-webpack-plugin은 html 관련 plugin
    npm i -D html-webpack-plugin
* 코드 추가
    const HtmlWebpackPlugin = require('html-webpack-plugin');
    module.exports = {
    // ...
    plugins: [
        new HtmlWebpackPlugin({
        template: 'public/index.html',  //public/index.html를 템플릿으로 지정
        })
    ],
    // ...
12. 웹팩에서 개발 서버를 제공
    npm i -D webpack-dev-server
13. package.json 의 스크립트를 변경
    "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "start": "webpack-dev-server"
    },
14. webpack.config.js 에서 개발 서버를 설정
    const port = process.env.PORT || 3000;
    module.exports = {
    // ...
    devServer: {
        host: 'localhost',
        port: port,
        open: true,
    },
    };
15. 리액트 코드 작성
* 리액트 패키지 설치
    npm i react react-dom
* src/index.js 추가
    import React from 'react';
    import ReactDOM from 'react-dom';
    import App from './App';
    ReactDOM.render(<App />, document.getElementById('root'));
* src/App.js 추가
    import React from 'react';
    const App = () => (
    <div>
        Hello, Webpack!
    </div>
    );
    export default App;
* public/index.html 추가
    <html lang="en">
    <head>
    <meta charset="UTF-8">
    <title>Webpack-for-react</title>
    </head>
    <body>
    <div id="root"></div>
    </body>
    </html>
16. npm start로 어플리케이션 실행
* 바벨 통해 es6 문법은 es5로 변환하고 웹팩 통해 하나의 파일(bundle.[hash].js)로 만들어서 브라우저에 띄어짐
 