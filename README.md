당 내용은 코딩애플🍎 수업을 듣고 정리한 글입니다.
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
- 예시2: [SpringBoot 프로젝트]()
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
  - `node`: 이미지 이름. Docker 프로그램 상단에서 node 검색하여 나온 Docker Official Image 중 나한테 맞는 이미지로 선택.
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

