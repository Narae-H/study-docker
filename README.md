당 내용은 [코딩애플🍎](https://codingapple.com/) 수업을 듣고 정리한 글입니다.
<br/>
<br/>

# docker란?
Linux container를 기반으로 만든 OS 레벨 가상화 구현을 도와주는 프로그램 <br/>
```sh
 # Dockerfile: 밀키트의 레시피를 작성하는 곳
 - 어떤 OS를 쓸건지
 - 어떤 프로그램을 설치할지
 - 어떤 터미널 명령어를 실행할지
 - 어떤 파일을 집어넣을건지
 - ...
```
<br/>

  ex. 치킨집 프랜차이즈를 운영한다면 어떻게 똑같은 맛을 구현할까<br/>

  |  <center>Docker</center>     |  <center>치킨집 프랜차이즈</center>         |
  |-------------|---------------------------------------------------------------|
  | `Image` (OS + 개발용SDK + 라이브러리 + 실행할코드 + 필요한 프로그램) | `밀키트` (레시피 + 요리재료) |
  | `컨테이너` (가상컴퓨터, 실제 코드가 실행되는 곳) | `치킨집 가게`(주방시설&조리도구, 실제 음식이 만들어지는 곳) |
  <br/>

## 등장배경
- 같은 코드를 각각 다른 컴퓨터에서 실행하는 경우 에러가 날 가능성 높음 <br/>
- 가상환경을 만들어주던 VM이 무겁고 성능이 좋지 않음 <br/>
=> 해결책으로 나온게 docker & container! 이전보다 훨씬 가볍게 가상환경 만들어줌.
<br/>

## 장점
- VM 보다 매우 가볍게 동작
- MSA (**M**icro **S**ervice **A**rchitecture) 구현하여, 분업하기 좋고 기능 업데이트도 편리하게 만듬.
- 프로그램 빌드하고 테스트하고 배포하는 작업이 쉬어짐.
- 프로그램 원하는 버전으로 원하는 OS위에 쉽게 설치하고 삭제 가능.
<br/>

## 단점
- 컨테이너 침입과 같은 새로운 보안이슈 챙겨야함
- 컨테이너를 많이 사용하면 관리 시간과 서버비용 증가
- 안정적인 DB 같은 걸 띄울 땐 딱히 장점 없음
- 컨테이너간 완전한 격리를 원하면 가벼운 VM 쓰는게 나을 수 있음.
<br/>
<br/>


# 설치방법
1. [Docker 설치 사이트](https://www.docker.com/products/docker-desktop/) 접속
2. 가운데 쯤에서 `Download Docker Desktop` 눌러서 맞는 버전 설치

> <details>
> 
> <summary> Docker(Desktop ver.)에서 로그인 안될 경우 해결방법 </summary>
> 1. Terminal 오픈 (오른쪽 하단의 `>_` 클릭) <br/>
> 2. 아래 명령어 입력  <br/>
> 
> ```sh
>   docker login
> ```
> 
> 3. Username, Password 입력 <br/>
> 
> </details>
<br/>
<br/>

# Image
- OS + 개발용SDK + 라이브러리 + 실행할코드 + 필요한 프로그램 <br/>
- 장점: 환경 맞추기 쉽고, 내 프로그램을 다른 곳에서 실행하기 쉬움.


## 실행방법
이미 올라와 있는 이미지 실행해보자 <br/>

### 방법 1. GUI 이용하여 실행
1. 상단 검색창에 `hello world` 검색
2. `rancher/hello-world` 옆의 `pull` 선택하여 다운로드 
3. 다운받은 이미지가 Images 메뉴에 추가 되었는지 확인
4. 다운받은 이미지 옆의 재생(▷)버튼 선택
5. `Optional settings` 열어서 `Ports` 부분에 `8080` 입력 
6. `Run` 선택
7. 터미널에서 정상 실행 확인
<br/>

### 방법 2. 터미널 명령어 이용하여 실행
1. 상단 검색창에 `hello world` 검색

2. Terminal 이용하여 다운로드
```sh
docker pull
```

3. 다운받은 이미지 확인
```sh
docker image ls
```

4. 다운받은 이미지 실행
```sh
docker run 이미지이름:태그명
```

5. 터미널에서 실행된 것 확인
<br/>
<br/>
 
# Image 만들기 & Docker로 서버 실행
어떤 서버의 이미지를 만들던 아래의 단계를 따라 진행하면 됨.

## 1. 서버코드 생성
Back-end 코드 작성

## 2. `Dockerfile` 생성
서버 하나 개발하는데는 여러가지 라이브러리가 필요하고, 그걸 다 쓰긴엔 오류날 확률도 높고, 귀찮고, 힘들고, 버전도 하나 하나 맞춰야 함. 때문에, 설정 파일을 복사해서 거기서 필요한 라이브러리를 알아서 설치하라고 명령하는 `Dockerfile` 파일을 생성하고, 그 안에 서버 종류에 따라 아래의 명령어와 매칭되는 명령어를 순차적으로 입력.
```sh
# Dockerfile

# 1. 필요한 OS 및 서버 언어에 따라 이미지 검색하여 생성
FROM [이미지 이름]:[버전]   

# 2. 컨테이너 경로로 이동 또는 경로가 존재하지 않는다면 경로에 맞는 새로운 폴더 생성
WORKDIR [이동하고 싶은 경로] 

# 3. 내 컴퓨터의 경로에 있는 파일을 컨테이너의 경로로 복사사
COPY [내 컴퓨터 경로(from)] [컨테이너의 경로(to)]

# 4. 프로젝트 빌드
RUN [빌드 명령어]

# 5. 포트 명시
EXPOSE 8080

# 6. 서버 실행
CMD [서버실행 명령어] 
```
- 예시1: [node.js 프로젝트](#nodejs-프로젝트)
- 예시2: [SpringBoot 프로젝트](#springboot-프로젝트)
- 예시3: [nginx 서버 - reverse proxy](#nginx)
<br/>

## 3. 이미지 생성
터미널에 아래 명령어 입력하여서 이미지 생성

```sh
# docker build -t 이미지이름:태그 .
#   docker build: docker 이미지 빌드 명령어어
#   -t: -t(또는 -tag)는 빌드하는 docker 이미지에 이름과 태그를 지정
#   이미지이름: 빌드된 이미지 이름으로 내가 원하는대로 만듬 ex) my-app, nginx-custom
#   태그: 내가 원하는대로. 보통은 버전명 넣음. ex) v1.0, latest
#   .: Dockerfile이 있는 디렉토리 경로를 지정. '.'는 현재경로라는 뜻

docker build -t 이미지이름:태그 .  
```

## 4. 이미지 실행
  1. Docker desktop app으로 이동 </br>
  2. `Image` 메뉴로 이동 </br>
  3. [Image생성](#3-image-생성) 에서 만든 이미지 제대로 생성됐는지 확인 </br>
  4. 만들어진 이미지 옆의 재생(▷)버튼 클릭 </br>
  5. `Optional settings` 열어서 `Ports` 부분에 `8080` 입력  </br>
  6. `Run` 설정 </br>
  7. 터미널에서 서버 정상 실행 확인 </br>
  8. 서버([https//localhost:8080](https//localhost:8080)) 접속 확인 </br>

<br/>
<br/>

# Node.js 프로젝트
## 1. 서버코드 생성
1. `Node.js` & `VS Code` 설치 <br/>
2. 서버 작업할 폴더 하나 만들고 그 안에 server.js 생성 <br/>
3. `express` 라이브러리 설치 <br/>
```sh
# 어떤 라이브러리 설치했는지 기록할 수 있는 파일 하나 생성
npm init -y

# express 설치
npm install express
```

3. 코드작성
아래코드를 `server.js`에 붙여넣기 <br/>
```js
const express = require('express');
const app = express();

app.listen(8080, () => {
  console.log('서버 실행중 http://localhost:8080');
});

app.get('/', (req, res) => {
  res.send('안녕');
}); 
```

4. 터미널로 코드띄우고 [서버](http://localhost:8080. 접속
```sh
node server.js
```

5. 터미널 종료 (터미널 창에서 `Ctrl + C` ) <br/>
<br/>

  > <details>
  > <summary> 서버 재시작 없이node.js 반영을 실시간으로 하고 싶다면?</summary>
  > 
  > ```sh
  > # nodemon 라이브러리 설치
  > npm install -g nodemon
  > 
  > # nodemon 으로 서버 실행
  > nodemon server.js
  > ```
  > </details> 
<br/>

## 2. `Dockerfile` 및 `.dockerignore` 파일 생성
### 2.1 `Dockerfile`
```sh
#  dockerfile

FROM node:22-slim   
WORKDIR /app 
COPY . .
RUN ["npm",  "install"]
EXPOSE 8080
CMD ["node", "server.js"] 
```
<br/>

***- dockerfile 설명***
- `FROM node:22-slim`: OS & Node.js 설치 명령어
  - `FROM`: dockerfile의 시작을 알림
  - `node`: 이미지 이름. Docker 프로그램 상단에서 node 검색하여 나온 Docker Official Image의 `Supported tags and respective Dockerfile links` 섹션에서 나한테 맞는 이미지로 선택.
  - `:22-slim`: 버전 명시 부분. `22`는 나의 node.js와 같은 버전인 22버전을 선택했고, `slim`은 필요없는 내용 지운 Debian linux 버전을 선택. (참고로, alpine: 용량 가장 작은 linux )
<br/>

- `WORKDIR /app`: 필요한 라이브러리가 설치될 폴더로 이동. 나의 현재 경로의 모든 파일을 가상컴퓨터의 기본 경로로 옮기면 더러우니깐 폴더하나 만들어서 그곳에 담음
  - `WORKDIR`: 이 명령어 다음으로 오는 폴더로 이동하라는 뜻(cd 명령어와 비슷). 없으면 폴더 만들어줌.
  - `/app`: 가상컴퓨터의 현재 경로 밑의 app 폴더로 이동해라. (없으면 폴더 만들어서 이동해라)
<br/>

- `COPY . .`: 현재경로에 있는 모든 파일과 폴더들을 가상컴퓨터의 현재경로로 복사
  - `COPY`: 이 명령어 다음 나온 '첫번째 경로의 것'을 '두번째 경로'로 복사해라.
  - `.`: 첫번째 `.`은 내 컴퓨터의 경로를 의미.
  - `.`: 두번째 `.`은 가상컴퓨터(이미지 내부파일)의 경로를 의미. 이전에 WORKDIR을 해서 app폴더로 이동했으므로 여기서 의미하는 가상컴퓨터의 경로는 app임.
<br/>

- `RUN ["npm",  "install"]`: package.json에 기재된 라이브러리를 설치
  - `RUN`: 이 명령어 다음에 오는 명령어를 실행해라.
  - `[]`: `[]`를 안치면 예상치 못한 명령어가 실행될 수 있으므로 RUN 다음엔 오는 명령어는 대괄호로 입력.
  - `"npm",  "install"`: `npm install`란 명령어로 `package.json`을 보고 필요한 파일을 설치하라는 뜻.
<br/>

- `EXPOSE 8080`: 어떤 포트번호로 열어줄지 알려줌. (실질적으로는 이미지 실행할 때 포트 열어두라고 명령하는데, 이건 일종의 가이드. 포트 설정할 때 헤깔리지말라고.)
  - `EXPOSE`: 이 명령어 다음에 오는 포트로 열어둬야 함.
  - `8080`: 포트넘버
<br/>

- `CMD ["node", "server.js"]`: 서버 실행
  - `CMD`: 이 명령어 다음에 오는 명령어를 실행해라. 보통 마지막 명령어는 `RUN` 대신에 `CMD`를 씀.
  - `[]`: `[]`를 안치면 예상치 못한 명령어가 실행될 수 있으므로 RUN 다음엔 오는 명령어는 대괄호로 입력.
  - `"node", "server.js"`: `node server.js`란 명령어로 `server.js` 서버를 실행하라는 뜻.

  > <details>
  > 
  > <summary> <small>`CMD` 대신 사용할 수 있는 `ENTRYPOINT`</small> </summary>
  > - CMD: 이미지를 실행 시에 매번 다른 명령어로 실행하고 싶으면 사용
  > ```sh
  > docker run 이미지명
  > docker run 이미지명 node server1.js
  > ```
  > - ENTRYPOINT: 이미지를 실행 시에 매번 다른 명령어로 실행 불가능.
  > </details>
<br/>

### 2.2. `.dockerignore`: 복사하고 싶지 않은 것들 설정
`COPY` 명령어를 통해서 파일/폴더 복사할 때, 굳이 나의 현재 경로의 모든 파일을 복사할 필요는 없음. 불필요한 파일은 복사하지 말라고 설정.

  **1. 파일 생성**: 현재 경로에 `.dockerignore` 파일 생성 <br/>
  **2. 내용 입력**: 복사하지 않은 파일 나열
  ```sh
  # .dockerignore

  .git
  Dockerfile
  node_modules
  ```
<br/>

## 3. 이미지 생성
[이미지 생성](#3-이미지-생성)
<br/>

## 4. 이미지 실행
[이미지 실행](#4-이미지-실행)
<br/>

## 성능을 높이려면?
프로젝트가 커지면 docker build 입력해서 기다리는 시간도 늘어남. 때문에 `dockerfile` 작성 시에 몇 가지만 고려하면 성능 개선 가능

- 아래의 `좋은 관습 4가지`가 적용된 버전
  ```sh
  FROM node:20-slim
  WORKDIR /app
  COPY package*.json . 
  RUN ["npm", "ci"]

  COPY . .
  EXPOSE 8080
  ENV NODE_ENV=production
  USER node
  CMD ["node", "server.js"]
  ```
<br/>

### 좋은 관습 1. `캐싱`
원래 빌드작업할 때 `COPY`, `RUN` 명령어 실행할 때 마다 docker가 몰래 캐싱을 해놓고 나중에 재사용 하는데, 변동사항이 생긴부분 부터는 캐싱된 걸 사용하지 않음. 때문에 변동사항이 많이 생기는 부분(예를 들면, 소스코드)들을 최대한 아래쪽에 적기.

예시) node.js의 dockerfile 작성 시에 라이브러리 설치 먼저하고 코드옮기기
```sh
(Dockerfile)

# 1. 자주 변동이 안될 것 같은 모듈 설치까지 먼저 진행하기.
FROM node:20-slim
WORKDIR /app
COPY package*.json . ## 설정파일(package로 시작 && 확장자가 .json인 모든 파일) 복사
RUN ["npm", "install"]
# 여기까지는 캐싱이 되서 변동 사항이 없다면 빠르게 실행될 수 있음.


# 2. 변동이 자주 생기는 소스코드 부분은 그 뒤에 복사하고 실행행
COPY . .
EXPOSE 8080
CMD ["node", "server.js"]
```
<br/>

### 좋은 관습 2. npm `ci`
node.js 개발 할 때 라이브러리 정확한 버전을 설치하려면 `npm install` 대신 `npm ci` 쓰는게 좋음. 
- `npm install`: `package.json`에 기록된걸 설치. 근데, 이 파일은 버전 명시할 때 보면 `"express" : "^4.21"`처럼 `^`로 시작되는 경우가 있음. 이건 '맨 앞자리가 4만 되면 된다'는 뜻이라서 나중에 라이브러리가 업데이트되면 실수로 4.21이 아닌 4.22버전이 설치될 수도 있음. 이걸 막고 싶다면 `^`을 지우거나, 아니면 package-lock.json(실제 내가 쓰는 라이브러리 버전 명시)을 바탕으로 설치하면 좋음.
- `npm ci`: `package-lock.json`에 기록된걸 설치

```sh
RUN ["npm", "ci"]
```
<br/>

### 좋은 관습 3. `ENV`
`ENV`라는 명령어를 쓰면 환경변수를 넣어서 이미지 빌드 가능.
옛날부터 존재하던 `express` 같은 라이브러리들은 `NODE_ENV=production` 를 집어넣어야 로그 출력량을 좀 줄이고 성능이 향상되는 케이스가 있음.

```sh
ENV NODE_ENV=production
CMD 어쩌구~
```
<br/>

### 좋은 관습 4. `권한` 낮추기
원래 Dockerfile에 적은 명령어들은 전부 `root 권한`으로 실행되는데, 보안적으로 root 권한으로 실행하는 것보다 권한을 좀 낮춰서 실행하는게 더 안전.

```sh
# node 공식 이미지인 경우에는 node라는 이름의 유저가 이미 만들어져 있으므로 그걸 사용
USER node   # node 유저로 변경
CMD 어쩌구~  # 그 다음 코드 실행
```
<sup>[node 공식 이미지 설명서 참고](https://github.com/nodejs/docker-node/blob/main/docs/BestPractices.md)</sup>
<br/>

## 번외: 사실, Dockerfile 자동 생성 가능
굳이 수동으로 `Dockerfile`생성하지 않고, 터미널에 아래 명령어 입력하고 몇개 질문 답하면 알아서 `Dockerfile` 만들어줌.
```sh
docker init
```
<br/>
<br/>

# SpringBoot 프로젝트
## 1. 서버코드 생성
<br/>

## 2. `Dockerfile` 생성
```sh
#  dockerfile
FROM amazoncorretto:21.0.4
WORKDIR /app
COPY . .
RUN ./gradlew build
CMD ["java", "-jar", "/build/libs/myapp.jar"] 
```
<br/>

***- dockerfile 설명***
- `FROM amazoncorretto:21.0.4`: OS & SpringBoot 설치 명령어
  - `FROM`: dockerfile의 시작을 알림
  - `amazoncorretto`: 이미지 이름. Docker 프로그램 상단에서 springboot 검색하여 나온 Docker Official Image 중 나한테 맞는 이미지로 선택.
  - `:21.0.4`: 버전 명시 부분. `21`은 `Java 21버전`이라는 뜻.
<br/>

- `WORKDIR /app`: 필요한 라이브러리가 설치될 폴더로 이동. 나의 현재 경로의 모든 파일을 가상컴퓨터의 기본 경로로 옮기면 더러우니깐 폴더하나 만들어서 그곳에 담음
  - `WORKDIR`: 이 명령어 다음으로 오는 폴더로 이동하라는 뜻(cd 명령어와 비슷). 없으면 폴더 만들어줌.
  - `/app`: 가상컴퓨터의 현재 경로 밑의 app 폴더로 이동해라. (없으면 폴더 만들어서 이동해라)
<br/>

- `COPY . .`: 현재경로에 있는 모든 파일과 폴더들을 가상컴퓨터의 현재경로로 복사
  - `COPY`: 이 명령어 다음 나온 '첫번째 경로의 것'을 '두번째 경로'로 복사해라.
  - `.`: 첫번째 `.`은 내 컴퓨터의 경로를 의미.
  - `.`: 두번째 `.`은 가상컴퓨터(이미지 내부파일)의 경로를 의미. 이전에 WORKDIR을 해서 app폴더로 이동했으므로 여기서 의미하는 가상컴퓨터의 경로는 app임.
<br/>

- `RUN ./gradlew build`: package.json에 기재된 라이브러리를 설치
  - `RUN`: 이 명령어 다음에 오는 명령어를 실행해라.
  - `./`: 현재 디렉토리에 있는 실행 파일을 호출
  - `gradlew`: `Gradle Wrapper`를 실행하여 Gradle 프로젝트에서 로컬에 Gradle을 설치하지 않고도 프로젝트를 빌드할 수 있도록 해줌.
  - `build`: 빌드하여 `.jar`파일 생성
<br/>

- `CMD ["java", "-jar", "/build/libs/myapp.jar"]`: 서버 실행
  - `CMD`: 이 명령어 다음에 오는 명령어를 실행해라. 보통 마지막 명령어는 `RUN` 대신에 `CMD`를 씀.
  - `[]`: `[]`를 안치면 예상치 못한 명령어가 실행될 수 있으므로 RUN 다음엔 오는 명령어는 대괄호로 입력.
  - `"java"`: Java 실행 명령어.
  - `"-jar"`: 특정 JAR 파일을 실행. 이 옵션 뒤에 실행할 JAR 파일 경로 지정.
  - `"/build/libs/myapp.jar"`: 실행할 JAR 파일의 경로. 일반적으로 Gradle로 빌드 된 파일은 `build/libs/` 디렉토리에 저장.
<br/>

## 3. 이미지 생성
[이미지 실행](#4-이미지-실행)
<br/>

## 4. 이미지 실행
[이미지 실행](#4-이미지-실행)
<br/>

## 성능을 높이려면?
SpringBoot는 `.jar`파일만 있으면 서버 실행가능하므로 .jar 파일만 남겨두고 다 삭제하는 방법을 고려해볼 수 있음.

### multi-stage build
`FROM`을 여러번 쓰는 것을 `multi-stage build` 라고 하는데 그러면 빌드과정이 필요한 프로젝트들은 이런 식으로 작성해서 용량을 줄이고 보안도 약간 챙길 수 있음.

```sh
# Dockerfile

# 1. 첫번째 FROM 공간
# /app 폴더에 .jar파일 생성하고 build라고 이름 붙임.
FROM amazoncorretto:21.0.4 AS build # AS build: 'build'라고 이름 붙임
WORKDIR /app
COPY . .
RUN ./gradlew build

# 2. 두번째 ROM 공간: Runtime stage
# FROM을 2번이상 작성가능. FROM을 만날 때마다 위의 있는 작업 내역 삭제할 수 있는데,
# 위의 작업내역에서 만든 파일들을 몰래 훔쳐오는 것 가능.
FROM amazoncorretto:21.0.4 AS runtime # AS runtime: 'runtime'라고 이름 붙임
WORKDIR /app
# COPY --from=[빌드된곳이름] [복사할파일] [이동할경로]
# 이전 FROM에서 나온 .jar 파일을 /app/server.jar 경로로 훔쳐와라.
COPY --from=build /app/build/libs/*.jar /app/server.jar
CMD ["java", "-jar", "/app/server.jar"] 
```
<br/>

## 번외: 사실, Dockerfile 자동 생성 가능
SpringBoot에서 gradle을 쓰는 경우 이미지 만드는 명령어가 내장되어 있음
```sh
./gradlew bootBuildImage
```
<br/>
<br/>


# nginx
`Reverse proxy`로 서버로 들어오는 요청을 중간에 가로채서 컨테이너 간 통신하기 위해 쓰임.<br/>
<br/>
<img src="https://raw.githubusercontent.com/Narae-H/study-docker/refs/heads/main/assets/readme/Reverse%20proxy.png" width="500px" alt="Reverse proxy"/><br/>
<sup>이미지 출처: [코딩애플](https://codingapple.com/)</sup>
<br/>

## 1. 서버코드 생성
1. `nginx` 폴더 생성 <br/>
2. `nginx` 폴더 안에 `myconfig1.conf` 파일 생성 <br/>
3. `myconfig1.conf` 파일에 아래 설정 붙여넣기. 아래파일은 `nginx.conf` 라는 기본 설정파일의 `http { }` 안에 자동으로 넣어서 실행됨. <br/>
```conf
# myconfig1.conf

server {
  # 누군가가 80번 포트로 들어오면 밑의 내용 실행해라라
  listen 80; 

  # location / { } 부분은 누가 /로 시작하는 모든 경로로 들어오면 localhost:8080으로 보내라는 뜻
  location / {
      proxy_pass http://localhost:8080; 

      # header라는 부분에 IP 주소 등 여러 정보를 채우라는 뜻. 지금은 별 의미없음.
      proxy_set_header Host $host;
      proxy_set_header X-Real-IP $remote_addr;
      proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
      proxy_set_header X-Forwarded-Proto $scheme;
  }
}
```
<br/>

## 2. `Dockerfile` 생성
```sh
# Dockerfile

FROM nginx:1.27.3-alpine-slim

COPY ./myconfig1.conf /etc/nginx/conf.d/myconfig1.conf
RUN rm /etc/nginx/conf.d/default.conf

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

***- dockerfile 설명***
- `FROM nginx:1.27.3-alpine-slim`: OS & Nginx 설치 명령어
  - `FROM`: dockerfile의 시작을 알림
  - `nginx:1.27.3-alpine-slim`: 이미지 이름:버전전. Docker 프로그램 상단에서 nginx 검색하여 나온 Docker Official Image 중 나한테 맞는 이미지로 선택.
<br/>

- `COPY ./myconfig1.conf /etc/nginx/conf.d/myconfig1.conf`: myconfig1.conf파일 실행을 하기 위해서 폴더에 복사
  - `COPY`: 이 명령어 다음 나온 '첫번째 경로의 것'을 '두번째 경로'로 복사.
  - `./myconfig1.conf`: 첫번째 경로는 내 컴퓨터의 경로를 의미.
  - `/etc/nginx/conf.d/myconfig1.conf`: 두번째 경로는 가상컴퓨터(이미지 내부파일)의 경로를 의미. `/etc/nginx/conf.d/` 폴더에 넣어줘야 nginx가 알아서 .conf파일을 가져가서 사용함.
<br/>

- `RUN rm /etc/nginx/conf.d/default.conf`: `/etc/ngingx/conf.d/default.conf(자동생성파일)`이 가끔 먼저 적용되기도 하니 삭제
  - `RUN`: 이 명령어 다음에 오는 명령어를 실행
  - `rm`: 삭제(remove)
  - `/etc/nginx/conf.d/default.conf`: 삭제할 파일
<br/>

- `EXPOSE 80`: 어떤 포트번호로 열어줄지 알려줌. (실질적으로는 이미지 실행할 때 포트 열어두라고 명령하는데, 이건 일종의 가이드. 포트 설정할 때 헤깔리지말라고)
  - `EXPOSE`: 이 명령어 다음에 오는 포트로 열어둬야 함.
  - `80`: 포트넘버
<br/>

- `CMD ["nginx", "-g", "daemon off;"]`: 서버 실행
  - `CMD`: 이 명령어 다음에 오는 명령어를 실행해라. 보통 마지막 명령어는 `RUN` 대신에 `CMD`를 씀.
  - `[]`: `[]`를 안치면 예상치 못한 명령어가 실행될 수 있으므로 RUN 다음엔 오는 명령어는 대괄호로 입력.
  - `"nginx", "-g", "daemon off;"`: `nginx -g daemon off`란 명령어로 서버를 실행하라는 뜻.

## 3. 이미지 생성
Dockerfile이 현재 경로가 아니라 다른 경로에 있으므로 마지막에 경로 명시

```sh
# docker build -t 이미지이름:태그 Dockerfile경로
docker build -t nginx:1 ./nginx
```
<br/>

## 4. 이미지 실행
[이미지 실행](#4-이미지-실행)
<br/>

## 5. 서버 접속 확인
1. [localhost:80](http://localhost:80) 접속 => `502 Bad Gateway`뜸 (이유는 아래에)<br/>

2. nginx 설정에다가 누가 `80 포트`로 접속하면 `[http://localhost:8080](http://localhost:8080)로 보내주라`라고 코드 짰으니 저번에 만들었던 nodeserver를 8080번 포트로 실행.
<br/>
<br/>

**Q. 왜 `502 Bad Gateway`에러가 날까?** <br/>
**A:** 현재 가상 컴퓨터 2대가 띄어져 있고, 내 컴퓨터의 포트로 각각 연결해 둠. <br/>
왼쪽 nginx 가상컴퓨터에는 "누가 80번 포트로 들어오면 8080포트로 보내기" 라고 코드를 짜놨지만, 왼쪽 nginx 가상컴퓨터에는 8080포트에서 동작 중인 프로그램이 없음. <br/>
`왼쪽과 오른쪽으로 다른 가상 컴퓨터`이기 때문에 서로 독립적으로 움직이고 내 컴퓨터 80번 포트인 localhost:80으로 들어가도 아무것도 안나오고 에러가 남. <br/>
<br/>
<img src="https://raw.githubusercontent.com/Narae-H/study-docker/refs/heads/main/assets/readme/nginx%20server.png" width="500px" alt="nginx server"/><br/>
<sup>이미지 출처: [코딩애플](https://codingapple.com/)</sup>
<br/>

**해결방안**
- 방법1: "누가 80번 포트로 접속하면 다시 올라가서 내 컴퓨터의 8080번 포트로 들어가라"<br/>
<br/>
<img src="https://github.com/Narae-H/study-docker/blob/main/assets/readme/connect_virtual_computers1.png?raw=true" width="500px" alt="Reverse proxy"/><br/>
<sup>이미지 출처: [코딩애플](https://codingapple.com/)</sup>
<br/>

- 방법2: 방법1보다 더 안전하고 간단하게 하려면 `network`라는걸 만들어서 그 안에 가상 컴퓨터를 담아놔도 됨. 같은 network 안에 들어있는 가상 컴퓨터들은 가상IP로 서로 통신이 가능하므로. [Container끼리 통신](#network를-이용한-container끼리-통신) 부분 참고<br/>
<br/>
<img src="https://github.com/Narae-H/study-docker/blob/main/assets/readme/connect_virtual_computers2.png?raw=true" width="500px" alt="Reverse proxy"/><br/>
<sup>이미지 출처: [코딩애플](https://codingapple.com/)</sup>
<br/>
<br/>

# Container
## 구조
리눅스 OS가 제공하는 이것저것(namespaces + cgroups + unionFS) 기능 섞은 것. 
- `namespace`: 프로그램마다 서로 영향을 끼치지 않게 독립적으로 프로세스와 파일구조 같은걸 분리 가능.
- `cgroup`: 프로그램마다 CPU나 RAM의 점유율 결정 
<br/>

## 터미널 명령어
Docker desktop app으로는 Docker의 모든 기능 사용 불가능하기 때문에 terminal 명령어도 알아두면 좋음.
### image 실행
```sh
# 방법 1) docker run 이미지명:태그명
# 터미널 점유당해서 더 이상 명령어 못씀
docker run nodeserver:1

# 방법 2) docker run -p 내포트:컨테이너포트 -d 이미지명:태그명 
#   -p: port 설정
#   8080:8080: 내컴퓨터포트:컨테이너포트
#   -d: detached (분리하여 실행)
# 터미널 점유안하고 백그라운드에서 실행하기
docker run -p 8080:8080 -d nodeserver:1
```
<br/>

### 실행 중 컨테이너 확인
```sh
docker ps
```
<br/>

### 실행 중 컨테이너 로그 출력
```sh
# 방법 1) 전부 다 보기
docker logs

## 방법 2) 특정 컨테이너 log 보기
docker logs 컨테이너명
```
<br/>

### 특정 컨테이너 접속
```sh
# 이 명령어 실행 다음에는 그 컨테이너 터미널 안에서 실행 됨.
docker exec -it 컨테이너명 sh

# 컨테이너 빠져나오기
# ctrl + p 후에 ctrol + q 입력
```
<br/>

### 컨테이너 일시정지
```sh
docker stop 컨테이너이름
```
<br/>

### 컨테이너 삭제
```sh
# 방법1) 정지된 컨테이너 삭제
docker rm 컨테이너이름

# 방법2) 실행 중인 컨테이너 삭제
docker rm 컨테이너이름 -f
```
<br/>

## docker desktop app 에서 컨테이너 정보 확인
실행중인 image 선택하면 `Logs`, `Inspect`, `Bind mounts`, `Exec`, `Files`, `Stats` 확인 가능

- `Logs`: 해당 컨테이너 log 확인
- `Inspect`: 해당 컨테이너 정보 보여줌.
- `Bind mounts`
- `Exec`: 원하는 명령어 실행
- `Files`: 디렉토리 구조 보여줌
- `Stats`: 현재 CPU 점유율 등 상태 보여줌.

<br/>
<br/>

# Network를 이용한 Container끼리 통신
컨테이너는 각각의 독립적인 컴퓨터기때문에 서로 만날 수 없지만, Network를 만들어서 그 안에 집어넣으면 각기 다른 두 컨테이너(ex. `Nginx`와 `Node server(웹서버)`)의 가상 IP 주소를 통해서 통신 가능. <br/>

***Prerequisite***
1. Docker에 `Node server 이미지`가 있다. ([Node서버 설정](#nodejs-프로젝트)) <br/>
2. Docker에 `Nginx 이미지`가 있다. ([Nginx서버 설정](#nginx)) <br/>
<br/>

***Steps***
1. [Network 생성](#1-network-생성)
2. [컨테이너를 Network에 넣기](#2-컨테이너를-network에-넣기)
3. [Nginx, Node Server 둘 다 같은 네트워크에서 띄어져 있는지 확인 ](#3-nginx-node-server-둘-다-같은-네트워크에서-띄어져-있는지-확인)
4. [컨테이너끼리 통신 테스트 - Docker App 내부에서](#4-컨테이너끼리-통신-테스트---docker-app-내부에서)
5. [conf 파일 수정하여 웹서버 연동](#5-conf-파일-수정하여-웹서버-연동)
<br/>
<br/>

### 1. Network 생성
컨테이너 통신을 위해서 Network 생성하기 위해 Docker Container App의 terminal에서 아래 명령어 입력 <br/>
```sh
# docker network create 네트워크이름
docker network create mynet1

# Network 목록 조회
docker network ls
```
<br/>

### 2. 컨테이너를 Network에 넣기
컨테이너 실행할 때 네트워크에 넣어서 실행하기 위해 Docker Container App의 terminal에서 아래 명령어 입력 <br/>
```sh
# docker run -d(detached mode) -p(port number) 내포트:컨테이너포트 --network 네트워크이름 --name 컨테이너이름작명 내이미지이름:태그
#  --network 네트워크이름: "네트워크 안에 담아주세요"란 뜻
#  --name 컨테이너이름작명: 쉽게 관리하기 위해 컨테이너 이름 짓기기

# 1. webserver(node.js) 서버 띄우기
docker run -d -p 8080:8080 --network mynet1 --name server-container nodeserver:v1

# 2. nginx 서버 띄우기
docker run -d -p 80:80 --network mynet1 --name nginx-container nginx:1
```
<br/>

### 3. Nginx, Node Server 둘 다 같은 네트워크에서 띄어져 있는지 확인 
1. Docker Desktop App의 `Container` 메뉴 접속
2. 컨테이너 선택 > `Inspect` > `Network`
3. 이름이 `mynet1`(내가 부여한 네트워크이름)인 것과 `IPAddress` 생성 확인
  ```json
  "Networks": {
      "mynet1": {
        ...
        "IPAddress": "172.XX.X.X",
        ...
      }
  }
  ```
<br/>

### 4. 컨테이너끼리 통신 테스트 - Docker App 내부에서
컨테이너끼리 통신이 잘되나 확인하려면 하나의 컨테이너로 들어가서 거기다가 다른 컨테이너를 불러보면 됨. <br/>
`nodeserver의 가상 IP` 확인하고, `nginx` 컨테이너 들어가서 `nodeserver의 가상IP 호출`

  **방법 1**. 터미널로 접속
  ```sh
  docker exec -it 컨테이너이름 /bin/sh
  ```

  **방법 2**. Exec 메뉴 이용
  ```
  Container 메뉴 > nginx 컨테이너 선택 > Exec 탭
  ```
<br/>

**IP주소로 접속하기**
```sh
# curl(접속해라) 아이피주소:포트주소
curl 172.18.0.3:8080

# 컨테이너 이름으로도 접속가능
curl server-container:8080
```

> <details>
>
> <summary> <small>`curl: not found` 가 뜬다면?</small> </summary>
> 컨테이너 이미지에 따라 기본적으로 포함된 패키지가 다름. 예를 들어, Alpine Linux 기반의 이미지는 매우 경량화되어 있으며, curl 같은 유틸리티가 기본적으로 설치되어 있지 않을 수 있음.
> 컨테이너 내부에서 직접 설치 필요
>
> 1. Debian/Ubuntu 기반 이미지
> ```sh
> apt-get update && apt-get install -y curl
> ```
> 
> 2. Alpine 기반 이미지
> ```sh
> apk add --no-cache curl
> ```
> </details>

<br/>

### 5. .conf 파일 수정하여 웹서버 연동
- `.conf` 파일 수정
```conf
# myconfig1.conf 

server {
  listen 80;
  location / {
    # 'http://[nodeserver IP 또는 컨테이너이름]:포트'로 수정
    proxy_pass http://server-container:8080; 
    
    # 이하 동일
  }
}
```

- 이미지 빌드
```sh
docker build -t nginx:1 ./nginx
```

- 컨테이너 전부 삭제 & 이미지 재실행 <br/>
 주의: 웹서버가 떠있어야 nginx에서 접속가능하므로 서버 실행할 때 `nodeserver -> nginx `순으로 띄우기
 ```sh
 # 1. webserver(node.js) 서버 띄우기
docker run -d -p 8080:8080 --network mynet1 --name server-container nodeserver:v1

# 2. nginx 서버 띄우기
docker run -d -p 80:80 --network mynet1 --name nginx-container nginx:1
 ```

- 접속확인: [localhost:80](http://localhost:80) 접속해서 nodeserver 제대로 뜨는지 확인.

<br/>
<br/>


# 이미지를 공유(업로드/다운로드)하는 법
~~USB를 이용하던 이메일을 보내던 자유지만,~~ 우리는 개발자인 만큼 이미지를 호스팅해주는 사이트에 업로드하고 필요할 때 다운로드하는 방법 사용. 훨씬 편리하기도 함.
- 호스팅 사이트: Docker Hub, AWS ECR, Azure Container Registry 등.

## 이미지 업로드/다운로드
해당 글에서는 일부 기능을 무료로 사용할 수 있는 `Docker Hub`를 이용하여 이미지 업로드하는 방법 기술.

### 리포지토리 생성
1. [hub.docker.com](hub.docker.com) 이동
2. 가입 & 로그인
3. 메인메뉴에서 `Create repository` 선택. 여기서, Repository란 이미지 보관용 폴더
4. Repository 이름 입력 ex) myserver
5. public 선택 (뮤료버전 쓸꺼니깐)
6. Create
7. `내아이디/4번에서입력한아이디`가 repository 이름임.

### 이미지 업로드 (docker push)
1. Docker Desktop App 열기
2. 왼쪽의 Images 메뉴 선택
3. 업로드 하고싶은 이미지 오른쪽의 `Push to Docker Hub` 선택 또는 아래 터미널 명령어로 업로드
```sh
# 1. 이미지의 이름을 바꾸는 명령어
# docker tag 이미지이름:태그명 리포지토리이름:태그명작명
# nodeserver:v1(from)를 naraehh/myserver:v1(to)로 바꿔라 => 이름바뀐 새로운 이미지가 리스트에 보임 (Id는 같으므로 이전과 동일한 이미지. 태그명만 여러개 붙였을 뿐)
docker tag nodeserver:v1 naraehh/myserver:v1

# 2. 실제로 업로드
# docker push 리포지토리이름:태그명작명 
docker push naraehh/myserver:v1
```

4. [hub.docker.com](hub.docker.com)가서 새로고침하면 방금 업로드한 이미지 확인 가능

<small>이미지 업데이트된거 올리고 싶으면? => 태그명을 다르게 작성해서 이미지 새로 빌드하고 올리면 됨.</small>
<br/>

### 이미지 다운로드 (docker pull)
1. 다운로드 하고싶은 이미지 오른쪽의 `Pull` 선택 또는 아래 터미널 명령어로 다운로드드
```sh
# docker pull 이미지명:태그
docker pull naraehh/myserver:v1
```

## 다른 종류의 이미지도 업로드 하고 싶다면?
**Q.** 예를 들어 `서버 담은 이미지`, `프론트엔드 담은 이미지`, `DB 담은 이미지` 이런게 있으면 각각 어디에 업로드하는게 좋을까? <br/>
**A.** 이런 경우엔 각각 별도의 리포지토리를 만들어서 거기에 업로드하는게 깔끔하고 관리쉬움.<br/>
근데 귀찮아서 혹은 private 리포지토리 하나가지고 계속 쓰고 싶어서 하나의 리포지토리에 태그명만 다르게 해서 쑤셔넣는 경우도 있긴함.
<br/>
<br/>

# Volume 사용법과 PostgreSQL DB 띄우기
DB를 컨테이너로 실행 및 volume 장착

## PostgreSQL 이미지 다운 및 실행
`PostgreSQL`은 엑셀처럼 테이블에 자료를 저장할 수 있는 관계형데이터베이스 (RDS)

### 1. `Postgres` 이미지 검색 및 다운로드
```sh
# docker pull <이미지 이름>:<태그>
docker pull postgres:17.2-alpine
```

### 2. 다운로드받은 이미지 실행

- GUI로 설정하고 실행
<img src="https://github.com/Narae-H/study-docker/blob/main/assets/readme/docker_db_env.png?raw=true" width="500px" alt="docker_db_settings">
<br/>

- 터미널로 실행
```sh
docker run -d --name db-container -e POSTGRES_USER=admin -e POSTGRES_PASSWORD=qwer1234 postgres:17.2-alpine 
```
<br/>

### 3. DB 접속
Dbeaver 같은 프로그램 쓰면 비주얼적으로 쉽게 접속해 볼 수 있고, 아니면 터미널에서 DB 접속도 가능

```sh
# DB 접속: Container 메뉴 > 컨테이너 선택 > Exec 탭
# psql -U [user이름] -W
psql -U admin -W

# 데이터베이스 리스트 조회
\l

# 데이터베이스 접속 
\c [데이터베이스명]

# 테이블 조회
\d
```

***문제점*** <br/>
컨테이너로 DB를 띄우고 재실행하면 항상 안에 있던 내용 초기화 되고 DB에 있던 내용도 날라감.
껐다켜도 데이터를 유지하고 싶으면 데이터를 volume에 저장.
<br/>

## Volume
`Volume`이란 데이터를 저장할 수 있는 공간으로 하드디스크에 폴더 하나 만드는 것과 똑같음. 컨테이너에 볼륨 장착하여 실시간으로 DB에 있는 내용 볼륨에 저장가능하고 나중에 컨테이너 띄울 때 볼륨에 있던 데이터를 다시 컨테이너에 집어넣어서 컨테이너 띄우기 가능.

### Volume 생성 방법
- GUI 이용
```
Volumes 메뉴 이동 > Create 버튼으로 볼륨 생성
```

- 터미널 이용
```sh
# 볼륨 생성
docker volume create 볼륨이름

# 볼륨 목록 조회
docker volume ls

# 볼륨 상세 조회
docker volume inspect 볼륨이름

# 볼륨 삭제
docker volume rm 볼륨이름
```

### Volume 장착해서 DB 컨테이너 띄우기
장착할 Volume이 존재한다면 Volume의 이름을 그게아니라면 새롭게 생성하고 싶은 Volume의 이름을 적으면 됨. <br/>
`/var/lib/postgresql/data`는 PostgreSQL에서 DB가 저장되어 있는 경로. 경로는 DB 종류마다 다름 <br/>

- GUI 이용 <br/>
<img src="https://github.com/Narae-H/study-docker/blob/main/assets/readme/docker_db_env2.png?raw=true" width="500px" alt="docker_db_settings2">
<br/>

- 터미널 이용 <br/>
```sh
docker run -v postgres-vol:/var/lib/postgresql/data postgres:17.2-alpine
```

### 볼륨 정상 작동하는지 확인
DB 데이터가 볼륨에 복사되었는지 확인하기 위해 몇개의 테스트 데이터 만들어 입력

- DB 컨테이너 접속
```sh
# Container 메뉴 > Container 선택 > Exec 탭

# 1. bash터미널 사용
bash

# 2. DB 접속
psql -U admin -W

# 3. DB 조회
\l

# 4. DB 접속
\c postgres
```

- 테이블 생성 및 데이터 입력
```sql
-- 테이블 생성
CREATE TABLE product ( title VARCHAR(100) );

-- 데이터 입력
INSERT INTO product values('shirt');
INSERT INTO product values('shirt1');

-- 데이터 조회
SELECT * from product;
```

- 컨테이너에 volume 붙여서 재실행
```sh
# 컨테이너 띄울 때 볼륨지정
# docker run -v(볼륨) 볼륨이름:컨테이너경로 이미지이름:버전
docker run -v postgres_vol:/var/lib/postgresql/data postgres:17.2-alpine
```

- 데이터가 볼륨에 복사되어서 남아있는지 확인
```sh
# Container 메뉴 > Container 선택 > Exec 탭

# 1. bash터미널 사용
bash

# 2. DB 접속
psql -U admin -W

# 3. DB 조회
\l

# 4. DB 접속
\c postgres
```

```sql
-- 데이터 조회
SELECT * FROM product;
```
<br/>

> <details>
> 
> <summary>서버에서 PostgreSQL에 접속해서 이거저거 SQL 실행하려면?</summary>
> 
> 1. 터미널에서 `npm install pg` 입력해서 라이브러리 설치
> 
> 2. 아래 코드 작성
> ```js
> const { Pool } = require('pg');
> const pool = new Pool({
>   host: 'db', //DB컨테이너이름
>   database: 'postgres', //접속할Database이름
>   user: 'admin', //유저이름
>   password: 'qwer1234', //비번
>   port: 5432,
> });
> ```
> 3. SQL 요청이 필요할때마다 `pool.query()` 사용
> ```js
> app.get('/add', async (req, res) => {
>   try {
>     await pool.query(`CREATE TABLE IF NOT EXISTS products (
>       id SERIAL PRIMARY KEY,
>       title VARCHAR(255),
>       price INT
>     );`);
>     res.send('SQL 실행 완료')
>   }
>   catch (err) {
>     console.log(err)
>     res.send('실패')
>   }
> });
> ```
> </details>
<br/>

## 결론
- 컨테이너 `데이터를 영구적으로 보존`해야 한다면 `볼륨을 만들어서 장착`. <br/>
- DB를 컨테이너로 쓰는 경우는 많이 없음. <br/>
- 컨테이너는 쉽게띄우고, 교체하고, 다른곳으로 옮기고 이런게 장점인데, 데이터베이스는 그런것보다 안정성이 중요해서 데이터베이스 호스팅을 받아서 안전하게 데이터 관리하는게 더 나음. <br/>
<br/>
<br/>

# Docker Compose
- `docker compose` 란? <br/>
`docker run` 명령어가 길어지면 사용하기가 힘들어지니, 명령어들을 한 파일에 입력해놓고 그걸 실행하는 걸 도와주는 프로그램. 여러개의 컴포넌트를 동시에 작성하는것도 가능. <br/>
<br/>
- `service` 란? <br/>
컨테이너를 어떻게 띄울지 정의하는 일종의 `가이드`. 만약, 컨테이너를 수백개 띄어야한다면 그걸 관리하는 프로그램(kubernetes, AmazonECS, Docker Swarm, Azure Service Fabric)을 사용하게 됨. 이런걸 써도 항상 서비스와 비슷한걸 작성한 뒤에 컨테이너들을 띄우게 됨.
<br/>

## 사용법
### 1. `docker-compose.yml` 파일 생성
- `yml` 파일이란? <br/> 
데이터 저장하는 용도의 파일형식으로 .json과 유사. yml파일은 `데이터이름: 값` 이런식으로 저장. 하위아이템일 경우 들여쓰기 하면 됨.
<br/>

### 2. 파일 내용 입력
하나의 .yml 파일에서 `servers: 하위항목`에 있는 컨테이너들은 자동으로 네트워크 하나 만들어서 그 네트워크에 전부 넣어줌.

```yml
#docker-compose.yml

# services:
#   컨테이너이름:
#     image: 이미지이름             # 이미지를 컨테이너로 띄어줌.
#     ports:                       # 몇 번 포트 할당할지 정함.
#       - 내컴퓨터포트:컨테이너포트  
#     environment:                 # 환경 변수 설정. .env파일 만들어놓고 docker compose 설정파일에서 가져와서 사용도 가능.
#       - 환경변수이름=값
#       - 환경변수이름=값
#     depends_on:                  # 의존성을 작성할 때. 해당 컨테이너 다음에 이 서비스를 실행해라.
#       - 컨테이너이름
#     deploy:                      # 서비스마다 같은 컨테이너를 동일한 설정으로 복제해서 띄우기
#       mode: replicated
#       replicas: 3
#     volumes:
#       - 볼륨이름: /var/lib/postgresql/data
#     restart: always              # 서버가 죽어도 자동 재시작
#
# networks:  # 네트워크 설정. 컨테이너는 여러개의 네트워크에 속해있을 수 있음.
#   mynet1:
#   mynet2: 
#
# volumes: # volumes 사용하기 위해 설정
#   test_vol: # volume 이름
#     external: true

services: 
  webserver:
    image: nodeserver:1
    ports: 
      - 8080:8080
    networks:
      - mynet1
      - mynet2

  nginx:
    image: nginx:1
    ports: 
      - 80:80
    networks:
      - mynet1
      
  db:
    image: postgres:17.2-alpine
    environment:
      -POSTGRES_USER=admin
      -POSTGRES_PASSWORD=qwer1234
    networks:
      - mynet2

networks:
  mynet1:
  mynet2: 
```

### 3. 실행
```sh
docker compose up
```

### 4. 정지/삭제
```sh
# 정지
docker compose stop

# 삭제
docker compose down
```
<br />

## 컨테이너로 미리보기 띄울 때
소스코드에 변동사항이 있을 때, 아래의 과정 없이 바로바로 서버에 반영되도록 하고싶다면 `docker-compose.yml`파일에서 설정 추가 필요.

**소스코드 반영**: 총 `3단계` 필요
1. `docker compose down`
2. 웹서버 이미지 다시 만듬
3. `docker compose up`
<br/>
<br/>


### 방법 1. `build` 옵션
`docker-compose.yml` 파일에 항상 docker build 하고 나서 컨테이너 띄우라고 `build` 설정 추가하면 됨.

```yml
## docker-compose.yml 

services:
  webserver:
    image: nodeserver:1
    build: . # 이 부분 추가
    ports:
      - 8080:8080
```

***코드설명***
- `build: .`: `docker compose up`할 때마다 해당 경로에 있는 도커파일을 가져다가 자동으로 `docker build` 명령을 수행해라.
  - `build:`: 빌드 명령어
  - `.`: 도커파일 경로. '.'은 현재경로를 의미
<br/>
<br/>

**소스코드 반영**: 총 `2단계` 필요. but, 소스 코드 수정되면 재실행 필요.
1. `docker compose down`
2. `docker compose up --build`: build 옵션 추가했을 때는 마지막에 `--build` 옵션 추가
<br/>
<br/>

### 방법 2. `watch` 옵션 
`watch` 특정파일이 변경될 때마다 컨테이너에 바로바로 복사를 해줘서 매번 빌드할 필요가 없음.

```yml
services:
  webserver:
    build: . # 1) 'build' 옵션 추가하고
    ports:
    - 8080:8080

    # 2) 'develop: watch:...' 옵션도 추가
    develop:
      watch:
        - action: sync+restart
          path: .
          target: /app
          ignore:
            - node_modules/
        - action: rebuild
          path: package.json 
```
***코드설명***
- `develop:`: 실시간 반영 원하는 컨테이너 하위에 추가 
  - `watch:`: 특정파일이 변경될 때마다 바로바로 컨테이너에 복사 
    - `- action:`: 내 코드에 변동사항이 생기면 어떤 `action`을 할지 
      - `sync`: 변동사항과 컨테이너 sycn를 맞춰라 (변동사항을 컨테이너에 복사해라)
      - `restart`: 서버 수정사항 반영하기 위해서 서버 재시작
    - `path:`: 어느 파일들을 감시할지 경로
      - `.`: 현재경로 (docker-compose.yml과 같은 경로)
    - `target:`: 컨테이너에 생긴 변동사항을 어디에(to) 붙여 넣을지 경로
      - `/app`: `/app`에 붙여 넣음.
    - `ignore:`: 변동사항을 복사할 필요가 없는 파일/폴더. `.dockerignore`파일이 있다면 따로 작성할 필요 없음.
      - `- node_modules/`:
    - `- action:`: 내 코드에 변동사항이 생기면 어떤 `action`을 할지. `action`은 여러개 집어 넣을 수 있음.
      - `rebuild`: path에 있는 파일이나 폴더가 변동사항이 생기면 그냥 아예 docker build를 다시 해달라는 뜻.(즉, 이미지를 다시 만들어라)
    - `path: package.json`: Node.js로 개발하는 경우, 라이브러리 변동사항이 생기면 이미지를 다시 만드는게 깔끔하기 때문에 package.json 파일 변경되면 rebuild 하라고 코드 짜놓음.
<br/>
<br/>

- 소스코드 변경되는지 확인법: `Docker Desktop App` > `Container` 메뉴 > 해당 container 선택 > `Files` 탭 밑에 해당 파일눌러서 코드 변경 되었는지 확인

<br/>

**소스코드 반영**: 총 `2단계` 필요. but, 소스 코드 수정되도 재시작 필요 없음.
1. `docker compose down`
2. `docker compose up --watch`: build 옵션 추가했을 때는 마지막에 `--watch` 옵션 추가
<br/>
<br/>

> <details>
> 
> <summary>node.js의 nodemon 사용</summary>
>
> 참고로, nodejs로 서버개발하는 경우에는 `nodemon`같은 라이브러리를 설치해서 `nodemon server.js`라는 명령어로 서버를 띄우면 파일변경이 생길 때마다 재시작을 알아서 해줌.
> 그래서, 명령어를 node server.js에서 nodemon server.js로 바꾸면 됨.
> 
> `Dcokerfile`을 수정해도 되지만, 귀찮다면 `docker-compose.yml`파일에서 `command` 옵션 추가하여 바로 CMD 명령어 덮어 쓰는 것도 가능
> ```yml
> services:
>   webserver:
>     build: .
>     ports:
>     - 8080:8080
> 
>     command: ["nodemon", "server.js"] # 이 부분 추가
>     develop:
>         (생략)
> ```
> </details>

<br/>

## graceful shutdown
- 프로그램을 종료할 때 필요한 task를 챙겨서 안전하게 종료하는 것. ex) DB 종료
<br/>

### 종료 코드 작성
- 컨테이너 끄고 재시작하는데 너무 느린 경우들이 있음. 특히, 웹서버 하나 끄는데 10초넘게 걸린다면 그건 `서버 꺼지는 코드를 짜지않았기 때문`.
- docker엔진이 컨테이너를 종료시킬 때는 '종료해라'라는 메시지를 컨테이너 프로그램으로 보내고 종료하는 형식인데, 컨테이너 종료 메시지가 없다면 10초 정도를 기다렸다가 강제로 꺼지기 때문.


  ```js
  /** server.js */

  const server = app.listen(어쩌구) 

  // SIGTERM: Docker엔진이 컨테이너 종료하기 위해서 종료 메세지보내는 경우
  process.on('SIGTERM', () => {
    server.close(() => {
      console.log('HTTP server closed')
    })
  })

  // SIGINT: 유저가 터미널에서 'ctrl + c` 누르거나 터미널에서 'kill 1' 입력하는 경우
  process.on('SIGINT', () => {
    server.close(() => {
      console.log('HTTP server closed')
    })
  }) 
  ```
<br/>
<br/>

# Orchestraion
MSA (**M**icro **S**ervice **A**rchitecture)가 등장하면서 컨테이너 수가 많아지고 이걸 관리하기 위해서 `orchestration tool` 등장.

- MSA 장점:
  - 유저가 많이 몰리는 그런 서비스가 있으면 그것만 딱 찝어서 CPU 사용량 늘리고 그럴 수 있어서 자원 사용이 효율적임
  - 회원기능을 수정했으면 회원기능 서비스만 따로 배포하면 되니까 기능 업데이트도 매우 빨라짐

- MSA 단점:
  - 마이크로서비스끼리 통신해야하면 그게 좀 귀찮아짐
  - 마이크로서비스가 많아지면 그걸 관리하는데 시간과 인력이 추가로 필요함
  - 서버비도 초반엔 비쌈

<br/>

## Orchestration Tools
### Kubernates
- Orchestration 툴은 Kubernates가 가장 유명
- 직접 셋팅하는 것보다 편하게 Kubernates를 사용하기 위해서 등장한 서비스도 있음
  - Amazon Elastic Kubernates Service
  - Azure Kubernates Service
  - Google Kubernetes Engine
<br/>
<br/>

### AWS ECS (Elastic Container Service)
-  AWS에서 만든 orchestration 툴
<br/>
<br/>

**용어 정리**
- `Cluster`: 하나의 프로젝트
- `Servie`: 하나의 마이크로 서비스
  - Service를 이용하여 task를 쉽게 복제
  - 고장난 task는 쉽게 replace.
- `Task`: 서로 붙어 있어야 할 컨테이너들을 묶는 단위. docker compse와 비슷. 
  - Task 안에서 통신가능. 볼륨장착, 컨테이너간 통신, 서비스간 통신 가능.
- `Load balancer`: 유저가 서버에 들어왔을 때 task들에 균등하게 안내해주는 역할
<br/>
<br/>

- **ECS 구조** <br/>
<img src="" alt="aws ecs 구조"><br/>
<br/>

- **치킨집 프랜차이즈로 비유** <br/>
<img src="" alt="aws ecs 구조비유"><br/>
<br/>


