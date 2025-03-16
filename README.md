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


## 설치방법
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


## 실행 방법
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

## Docker Image 태그
| 버전  | 크기         | 기반 OS  | 패키지 관리자 | 특징                                               | 사용 사례 |
|-------|------------|---------|--------------|------------------------------------------------|---------|
| `Alpine` | 5MB 이하    | Alpine Linux | `apk` | - 초경량화 패키지 <br> - glibc 미포함 (musl 사용) | - 초경량 컨테이너 필요할 때 <br/> - 가장 기본에서 원하는 패키지 직접 설치하고 싶을 때 |
| `Slim`   | 20~50MB   | Debian<sup>1)</sup>  | `apt` | - 불필요한 패키지 제거한 가벼운 Debian 버전 <br> - 일반적인 앱 실행 가능 | - 기본적인 환경을 갖추면서 가벼운 이미지가 필요할 때 |
| `Full`   | 200MB 이상 | Debian  | `apt` | - 필요한 패키지가 다 포함됨 <br> - 개발 편리하지만 무거움 | - 빠른 개발 & 디버깅이 필요할 때 <br/> - 최적화 없이 바로 사용하고 싶을 때 |

<small>1) Debian: 리눅스 배포판 운영체제</small>
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
- [Spring Boot with Docker 공식 웹사이트](https://spring.io/guides/gs/spring-boot-docker)

## Docker 이미지 빌드 및 배포하기
### 1. 서버코드 생성
짜여진 코드 없다면 [pre-initialized project](https://start.spring.io/) 사용

### 2. `Dockerfile` 생성

1. 프로젝트 루트 디렉토리에 `Dockerfile` 생성: Spring Boot 관련 공식 이미지는 없으므로 `Amazon Corretto` 이미지를 Java 버전 맞춰서 다운로드 하고 필요한 프로그램 설치
    - [Docker Hub - Amazon Corretto 이미지 리스트](https://hub.docker.com/_/amazoncorretto?uuid=AB617CA7-708C-4BA3-B23D-C5C2BACC79AE)
    -  Amazon Corretto 이미지 태그 의미:
        <details>
        <summary>OS</summary>

          - `al2 / al2-generic`: Amazon Linux 2
          - `al2023`: Amazon Linux 2023 (최신 배포판)
          - `alpine`: Alpine Linux (경량)
          - `debian`: Debian (안정적)
        </details>

        <details>
        <summary>Image subtypes(설치된 프로그램)</summary>

          | 태그       | 특징                                                     | 용도                                            | 이미지 크기             |
          |------------|----------------------------------------------------------|-------------------------------------------------|-------------------------|
          | `headful` | GUI 라이브러리(AWT, Swing 등) 포함.                         | 데스크탑 애플리케이션에서 GUI가 필요한 환경 사용         | 상대적으로 큼 (GUI 포함)   |
          | `headless` | GUI 라이브러리 제외.                                        | 서버, 컨테이너 등 GUI가 필요 없는 환경에서 사용          | 경량화 (작은 크기)         |
          | `jdk`      | 전체 JDK (컴파일러, 디버거 등) 포함.                       | 개발과 빌드 환경, JDK 도구가 필요한 경우               | JRE보다 큼                    |
          | `slim`     | 최소 OS 기반 이미지로 최적화.                              | 배포, 컨테이너 환경에서 이미지 크기 최소화, 보안 강화      | 매우 경량화됨               |
        </details>

2. 사용한 빌드 언어에 따라 아래 코드를 `Dockerfile`에 추가
    <details>
    <summary> Gradle 사용한 경우 </summary>

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

      ***번외***: 사실, Gradle에서는 Dockerfile 자동 생성 가능. SpringBoot에서 gradle을 쓰는 경우 이미지 만드는 명령어가 내장되어 있음
      ```sh
      ./gradlew bootBuildImage
      ```  
    </details>

    <details>
    <summary> Maven 사용한 경우 </summary>
    
    ```sh
    # Dockerfile

    # 빌드 스테이지
    FROM amazoncorretto:17-alpine3.21 AS build
    RUN apk add --no-cache curl

    WORKDIR /app

    COPY mvnw .
    COPY .mvn .mvn
    COPY pom.xml .

    RUN chmod +x mvnw

    RUN ./mvnw dependency:go-offline -B

    COPY . .

    RUN chmod +x ./mvnw

    RUN ./mvnw package -DskipTests

    # Runtime 단계
    FROM amazoncorretto:17-alpine3.21 AS runtime

    WORKDIR /app

    RUN addgroup -g 1000 worker && \
        adduser -u 1000 -G worker -s /bin/sh -D worker

    COPY --from=build --chown=worker:worker /app/target/*.jar ./main.jar

    USER worker:worker

    ENV PROFILE=${PROFILE}

    EXPOSE 8080

    ENTRYPOINT ["java", "-Dspring.profiles.active=${PROFILE}", "-jar", "main.jar"]
    ```  
    </details>

### 3. 이미지 생성
[이미지 생성](#3-이미지-생성)
<br/>

### 4. 이미지 실행
[이미지 실행](#4-이미지-실행)
<br/>


## 성능을 높이려면?
Spring Boot는 `.jar`파일만 있으면 서버 실행가능하므로 .jar 파일만 남겨두고 다 삭제하는 방법을 고려해볼 수 있음.

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
여러 개의 컴퓨터 시스템, 애플리케이션 및/또는 서비스를 조율하고 관리하는 것.

## 등장배경
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
-  AWS에서 만든 orchestration 툴. [AWS ECS 자세히](#aws-ecs)
<br/>
<br/>

# AWS Elastic Beanstalk 배포

## 실행환경
- `Spring Boot 프로젝트 (Maven)`
- `Docker`: **코드 + 실행 환경을 패키징**하는 기술
  - 애플리케이션을 컨테이너로 감싸서 어디서든 동일한 환경에서 실행 가능하게 만듦.
  - Dockerfile을 이용해 이미지를 빌드하고, 이를 EB에서 실행할 수 있음.
- `GitHub Actions`: : **CI/CD 자동화**를 위해 파이프라인 구축
  - 코드 푸시할 때 자동으로 Docker 빌드 + EB에 배포
- `AWS ECR (Elastic Container Registry)`: **Docker 이미지를 저장**하는 서비스
  - EC2(서버), S3(스토리지), Cloud Watch(모니터링), 로드 밸런서(분산), 오토스케일링 등을 자동으로 설정해 줌.
  - 애플리케이션을 쉽게 배포할 수 있도록 지원함.
- `AWS EB (Elastic Beanstalk)`: AWS에서 제공하는 **서버 배포 및 관리 서비스**.

// TODO: 이거에 대해 서로 연결관계를 나중에 이미지로 추가할 수 있음 좋을듯.

<details>
<summary> CI/CD 파이프라인의 주요 단계 </summary>

- `CI` (Continuous Integration, 지속적 통합): 코드 빌드 & 테스트 자동화
- `CD` (Continuous Deployment/Delivery, 지속적 배포/전달): 애플리케이션 배포 자동화
<br/>

  |단계 |설명 |예시|
  |----|-----|----|
  |1. 코드 변경 (Commit & Push) |	개발자가 코드를 GitHub에 푸시	| git push origin main|
  |2. 빌드 (Build)	|Docker 이미지를 생성	|docker build -t my-app . |
  |3. 테스트 (Test)	|유닛 테스트, 통합 테스트 실행 |	mvn test, npm test|
  |4. Docker 이미지 푸시	|AWS ECR 또는 Docker Hub에 업로드 |	docker push <ECR_URL>/my-app:latest|
  |5. 배포 (Deploy)	| Elastic Beanstalk에 배포 |	eb deploy|
</details>

## 기본 배포 (셋팅 + 워크플로우 실행) 

### 순서
1. `계정`생성<br/>
  1-1. [필요한 계정 생성](#1-1-필요한-계정-생성)

2. `Dockerfile` 이용하여 패키징 <br/>
  2-1. [`Docker`를 이용하여 어플리케이션 패키징](#2-1-docker를-이용하여-어플리케이션-패키징) <br/>
  2-2. [`.dockerignore` 파일 추가](#2-2-dockerignore-파일-추가) <br/>
  2-3. [패키징 제대로 됐는지 확인](#2-3-패키징-제대로-됐는지-확인) <br/>

3. 도커 이미지 저장소인 Elastic Container Registry (`ECR`) 생성 <br/>
  3-1. [ECR 생성](#3-1-도커-이미지-저장소-ecr-생성) <br/>

4. 소스코드 배포 및 관리 저장소인 Elastic Beanstalk (`EB`) 생성 <br/>
  4-1. [EB 생성](#4-1-eb-생성) <br/>

5. GitHub Actions에서 AWS ECR, EB 연결하기 위해 `IAM User` 생성 및 권한부여 <br/>
  5-1. [IAM User 생성](#5-1-iam-user-생성) <br/>
  5-2. [Role에 권한 부여](#5-2-role에-권한-부여) <br/>

6. GitHub Actions Secrets에 `IAM 인증키 등록` 및 확인 <br/>
  6-1. [Secrets에 IAM 인증키 등록](#6-1-github-actions-secrets에-iam-인증키-등록) <br/>
  6-2. [GitHub Actions에서 AWS ECR, EB에 접근 가능한지 확인](#6-2-github-actions에-iam-인증-확인) <br/>

7. EB에 ECR이미지 가져와서 배포할 수 있도록하는 파일(`Dokerrun.aws.json`) 생성 <br/>
  7-1. [Dokerrun.aws.json 파일 생성](#7-1-dockerrunawsjson파일-생성) <br/>

8. 민감한 정보를 GitHub Secrets에 숨김 (Optional) <br/>
  8-1. [민감한 정보를 GitHub Secrets에 추가](#8-1-민감한-정보를-github-secrets에-추가-optional) <br/>

9. CD 실행을 위한 스크립트(`eb-deploy.yml`) 작성 <br/>
  9-1. [GitHub Action CD 스크립트 작성 (eb-deploy.yml)](#9-1-github-action-cd-스크립트-작성-eb-deployyml) <br/>   

10. 워크플로우 파일 실행 <br/>
  10-1. [워크플로우 파일(eb-deploy.yml) 실행](#10-1-eb-deployyml-실행) <br/>
<br/>

### 자세히 - 스텝 별로 자세히 설명
#### 1-1. 필요한 `계정` 생성
- GitHub
- Docker Hub
- Amazon Web Services (AWS)

#### 2-1. `Docker`를 이용하여 어플리케이션 패키징
- [Docker 이미지 빌드 및 배포하기 참고](#docker-이미지-빌드-및-배포하기)

#### 2-2. `.dockerignore` 파일 추가
Docker 이미지를 빌드는 과정에는 소스 코드를 복사하는 과정이 필요함. 이 때, 이미지의 용량을 불필요하게 키우거나 보안 측면에 문제를 야기할 수 있는 파일은 이미지 빌드 과정에서 제외필요. 

1. Root 디렉토리에 `.dockerignore` 파일 추가

2. `.dockerignore`에 아래내용 추가
  ```properties
  .gradle/
  build/

  target/

  .idea/
  *.iml
  *.iws
  *.ipr
  .vscode/

  *.log

  .DS_Store
  Thumbs.db

  src/test/

  README.md
  LICENSE
  *.md

  .git/
  .gitignore

  # Dockerfile
  # docker-compose.yml

  node_modules/

  *.class

  *.jar
  *.war
  *.nar
  *.ear
  *.zip
  *.tar.gz
  *.rar

  hs_err_pid*
  ```

#### 2-3. 패키징 제대로 됐는지 확인
1. [이미지 생성](#3-이미지-생성)
2. [이미지 실행](#4-이미지-실행)

#### 3-1. 도커 이미지 저장소 (ECR) 생성
1. [AWS ECR (Private repositories)Console](https://console.aws.amazon.com/ecr/) 이동
2. Create 선택
3. General settings:
    - **Repository name**: myapp-stg (원하는 이름 입력)
    - **Image tag mutability**: Mutable
    - **Encryption settings**: AES-256
4. Create

#### 4-1. EB 생성
1. [AWS EB Console](https://ap-southeast-2.console.aws.amazon.com/elasticbeanstalk/) 이동
2. Create environment 선택
3. 설정
  - `Step 1`: Configure environment
    - **Environment tier**: Web server environment
    - **Application information**
      - Application name: myapp-stg 
    - **Environment information**
      - Environment name: myapp-stg-env
    - **Platform**
      - Platform type: Managed platform
      - Platform: Docker
      - Platform branch: Docker running on 64bit Amazon Linux 2023
      - Platform version: 4.4.3 (Recommended)
    - **Application code**: Sample application
    - **Presets**: Single instance (free tier eligible)
  - `Step 2`: Configure service access
    - **Service access**
      - **Service role**: Use an exsiting service role
      - **Existing service roles**: aws-elasticbeanstalk-service-role
      - **EC2 instance profile**: aws-elasticbeanstalk-ec2-role
  - `Step 3`: Set up networking, database, and tags - optional -> skip! 
  - `Step 4`: Configure instance traffic and scaling - optional
    - **Instances**
      - **Instance metadata service (IMDS)**: Deactivated 선택
    - **Capacity**
      - **Environment type**: Single Instance
      - **Fleet composition**: On-Demand instance
      - **Architecture**: x86_64
      - **Instance types**: t3a.micro (싼거 아무거나 [인스턴스 가격표](https://aws.amazon.com/ec2/pricing/on-demand/))
  - `Step 5`: Configure updates, monitoring, and logging - optional -> skip!
4. 생생 된 Environment의 도메인 눌러서 실행해서 화면 제대로 뜨나 확인(정상적으로 설정됐다면 **Congratulations!** 뜸.)

#### 5-1. IAM User 생성
GitHub Actions를 통해 ECR, EB에 명령을 하고자 한다면 로그인 할 수 있는 사용자(User) 필요.

1. [AWS IAM > Users](https://console.aws.amazon.com/iamv2/home#/users)로 이동
2. **Create user** 선택
3. `Step 1`. Specify user details: 
    - **User name**: eb-github-actions-deploy
    - **Provide user access to the AWS Management Console - optional** 선택 > **I want to create an IAM user** 선택
4. `Step 2`. Set Permissions:
    - **Permissions options**: **Attach policies directly** 선택
    - **Permissions policies**: 아래 2개 권한 선택
      - AmazonEC2ContainerRegistryFullAccess (ECR 접근)
      - AdministratorAccess-AWSElasticBeanstalk 선택 (EB 배포)
      - AmazonEC2FullAccess (EC2 인스턴스에 태그 추가할 수 있는 권한)
5. `Step 3`. Review and create:
    - Tags: Name/eb-github-actions-deploy
6. `Step 4`. Retrieve password:
    - **.csv** 파일도 다운받아 두기
7. 생성된 user 선택
8. Summary에 **Create access key** 선택
9. third-party service 선택 
10. Access key, Secret access key 메모해두기

#### 5-2 Role에 권한 부여
Elastic Beanstalk (EB) 환경에서 Elastic Container Registry(ECR)에 저장된 Docker 이미지를 가져오기 위해 권한 부여

1. [AWS IAM > Users](https://console.aws.amazon.com/iamv2/home#/users)로 이동
2. Access Management > Roles
3. 리스트에서 `aws-elasticbeanstalk-service-role` 선택
4. Permissions > Add permissions > Attach Permissions > `AmazonEC2ContainerRegistryReadOnly` 선택 > Add permissions

#### 6-1. GitHub Actions Secrets에 IAM 인증키 등록
1. CI/CD 구축할 Github 리포지토리로 이동
2. **Settings** > **Secrets and variables** > **Actions** 로 이동
3. **New repository secret** 선택하고 아래 값 추가:
    - AWS_ACCESS_KEY_ID: IAM 생성 시 받은 Access Key ID
    - AWS_SECRET_ACCESS_KEY: IAM 생성 시 받은 Secret Key
    - AWS_ACCOUNT_ID: AWS 계정 ID (AWS 우측 상단의 로그인 된 내 아이디 화살표 누르면 확인 가능. '-' 없이 숫자만 입력)

#### 6-2. GitHub Actions에 IAM 인증 확인
1. CI/CD 구축할 Github 리포지토리로 이동
2. Actions 탭 선택 > `set up a workflow yourself` 선택
3. `aws-iam-test.yml`이름으로 파일 생성 
4. 아래 코드 붙여넣기
```yaml
name: AWS IAM Access Key Test

on:
  workflow_dispatch:

jobs:
  test-iam-access-key:
    runs-on: ubuntu-latest

    steps:
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ secrets.AWS_ACCESS_KEY_ID }}
          aws-secret-access-key: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
          aws-region: ap-southeast-2

      - name: Verify AWS Identity
        # 명령어를 실행하여 현재 인증된 IAM 사용자의 정보를 출력
        run: |
          aws sts get-caller-identity  
```
5. Commit changes
6. CI/CD 구축할 Github 리포지토리 > Actions 로 이동
7. 방금 전 만든 워크플로우 왼쪽 메뉴에서 선택 > Run Workflow
8. 마지막 명령 블럭인 `Verify AWS Identity`에서 UserId, Account 보인다면 성공

#### 7-1. Dockerrun.aws.json파일 생성
Elastic Beanstalk(EB)가 Elastic Container Registry(ECR)에 있는 Docker 컨테이너를 배포할 때 사용하는 구성파일

1. 루트 디렉토리에 `Dockerrun.aws.json` 파일 추가

2. `Dockerrun.aws.json`에 아래 내용 추가 <br/>
파일의 버전(AWSEBDockerrunVersion)은 ['Dockerrun.aws.json 버전'](https://docs.aws.amazon.com/elasticbeanstalk/latest/dg/single-container-docker-configuration.html) 참고하여 선택.
```json
{
  "AWSEBDockerrunVersion": "1",
  "Image": {
    "Name": "<AWS_ACCOUNT_ID>.dkr.ecr.ap-southeast-2.amazonaws.com/myapp-stg/latest",
    "Update": "true"
  },
  "Ports": [
    {
      "ContainerPort": 8080,
      "HostPort": 5000
    }
  ]
}
```
***- Dockerrun.aws.json 설명***
- `AWSEBDockerrunVersion`: Dockerrun 파일 형식의 버전
- `Image`: Docker 이미지 정보
  - `Name`: 이미지의 이름을 지정. `ECR URI:태그`의 값을 가짐.
  - `Update`: EB 배포 시마다 해당 이미지를 최신으로 업데이트하기 위해 `true` 작성
- `Ports`
  - `ContainerPort`: Docker 컨테이너 내부에서 애플리케이션이 실행되는 포트. 즉, Spring Boot 애플리케이션이 컨테이너 안에서 8080 포트로 요청을 수신 
  - `HostPort`: EB 인스턴스(호스트)에서 외부 트래픽을 수신하는 포트입니다. 일반적으로 Nginx와 같은 리버스 프록시가 외부 요청을 받아 내부의 컨테이너로 전달할 때 5000 포트를 사용. 
<br/>

#### 8-1. 민감한 정보를 GitHub Secrets에 추가 (optional)
GitHub Secrets을 사용해서 `.properties`에 있는 민감한 정보(ex. 비번)를 숨김. <br/>
예를 들어, application-secure.propertis 안에 있는 정보는 민감한 정보로 GitHub에 푸시하지 않음. 이 정보들은 서버 배포를 할 때 필요하므로 GitHub Repository에 올려놓는게 아니라 비밀저장소(GitHub Secrets)에 저장을 해야 함.

1. CI/CD 구축할 Github 리포지토리로 이동
2. **Settings** > **Secrets and variables** > **Actions** 로 이동
3. **New repository secret** 선택하고, (application-secure.propertis)데이터 추가. 나중에 GitHub Action 스크립트 작성할 때, 해당 데이터 불러와서 추가예정. <br/>
  예시:
    - MAIL_HOST: smtp.office365.com
    - MAIL_PORT: 587
    - MAIL_USERNAME: <User Name>
    - MAIL_PASSWORD: <Your Password>
<br/>

#### 9-1. GitHub Action CD 스크립트 작성 (eb-deploy.yml)
GitHub에 코드가 push되면 자동으로 빌드하고 AWS EB서버에 deploy 되도록 스크립트 작성.

1. 프로젝트에 `.github`, `.github\workflows` 폴더 생성하고, 그 아래에 `eb-deploy.yml`파일 생성 <br/>
    ```
    📂 your_project
    ├─ 📁 .github
    │  ├─ 📁 workflows 
    │  │  └─ 📄 eb-deploy.yml
    ```
2. 아래 코드 붙여 넣기
  ```yml
  name: Deploy to Elastic Beanstalk

  on:
    push:
      branches:
        - main

  env:
    AWS_ACCESS_KEY: ${{ secrets.AWS_ACCESS_KEY_ID }}
    AWS_ACCESS_SECRET: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    AWS_REGION: ap-southeast-2
    REPOSITORY: myapp-stg  
    REGISTRY: ''
    IMAGE_TAG: ''

  jobs:
    deploy:
      runs-on: ubuntu-latest

      steps:
        - name: Checkout Repository
          uses: actions/checkout@v3

        - name: Create application-secure.properties file
          run: |
            cd ./src/main/resources
            touch ./application-secure.properties
            echo "spring.mail.host=${{ secrets.MAIL_HOST }}" >> ./application-secure.properties
            echo "spring.mail.port=${{ secrets.MAIL_PORT }}" >> ./application-secure.properties
            echo "spring.mail.username=${{ secrets.MAIL_USERNAME }}" >> ./application-secure.properties
            echo "spring.mail.password=${{ secrets.MAIL_PASSWORD }}" >> ./application-secure.properties
          
        - name: Configure AWS credentials
          uses: aws-actions/configure-aws-credentials@v4
          with:
            aws-access-key-id: ${{ env.AWS_ACCESS_KEY }}
            aws-secret-access-key: ${{ env.AWS_ACCESS_SECRET }}
            aws-region: ${{ env.AWS_REGION }}
        
        - name: Login to AWS ECR
          id: login-ecr
          uses: aws-actions/amazon-ecr-login@v2
          with:
            mask-password: 'true'
      
        - name: Generate REGISTRY environment variable
          run: echo "REGISTRY=${{ steps.login-ecr.outputs.registry }}" >> $GITHUB_ENV

        - name: Set Timezone
          uses: MathRobin/timezone-action@v1.1
          with:
            timezoneLinux: 'Australia/Sydney'
            timezoneMacos: 'Australia/Sydney'
            timezoneWindows: 'AUS Eastern Standard Time'

        - name: Generate MAGE_TAG environment variable
          run: echo "IMAGE_TAG=$(date +%Y-%m-%d_%H-%M-%S)" >> $GITHUB_ENV

        - name: Build and Tag Docker Image
          run: |
            docker build -t ${{ env.REGISTRY }}/${{ env.REPOSITORY }}:${{ env.IMAGE_TAG }} -t ${{ env.REGISTRY }}/${{ env.REPOSITORY }}:latest .

        - name: Push Docker Image to ECR
          run: |
            docker push ${{ env.REGISTRY }}/${{ env.REPOSITORY }}:${{ env.IMAGE_TAG }}
            docker push ${{ env.REGISTRY }}/${{ env.REPOSITORY }}:latest

        - name: Package Dockerrun.aws.json
          run: |
            mkdir -p deploy
            cp Dockerrun.aws.json deploy/Dockerrun.aws.json
            cd deploy && zip -r myapp.zip .

        - name: Deploy to Elastic Beanstalk
          uses: einaregilsson/beanstalk-deploy@v22
          with:
            aws_access_key: ${{ env.AWS_ACCESS_KEY }}
            aws_secret_key: ${{ env.AWS_ACCESS_SECRET }}
            application_name: 'myapp-stg'
            environment_name: 'myapp-stg-env'
            version_label: ${{ github.run_number }}
            region: ${{ env.AWS_REGION }}
            deployment_package: deploy/myapp.zip
            use_existing_version_if_available: true 
  ```
***- eb-deploy.yml 설명*** <br/>
GitHub Actions를 이용해 Spring Boot 애플리케이션을 Docker 이미지로 빌드하고, AWS ECR에 푸시한 후, Elastic Beanstalk(EB)에 배포하는 전체 CI/CD 파이프라인을 구성

- `on`: GitHub Actions 워크플로우를 실행할 트리거(이벤트)를 지정.
  - `push`: GitHub에 push 될 때 워크플로우 실행. push 외에도 pull_request, schedule, relese 등 있음. 
    - `branches`: 어떤 브랜치에 이벤트가 일어날 때 실행 할 것인지를 지정. 여기서는 `main` 브랜치.
- `env`: 전역 환경 변수로 jobs 섹션에서 공통으로 쓰기 위해서 선언. 
  - `AWS_ACCESS_KEY: ${{ secrets.AWS_ACCESS_KEY_ID }}`: GitHub Secrets에서 AWS_ACCESS_KEY_ID를 가져와서 AWS_ACCESS_KEY에 저장
  - `AWS_ACCESS_SECRET: ${{ secrets.AWS_SECRET_ACCESS_KEY }}`: GitHub Secrets에서 AWS_SECRET_ACCESS_KEY를 가져와서 AWS_ACCESS_SECRET에 저장
  - `AWS_REGION: ap-southeast-2`: AWS_REGION 변수에 "ap-southeast-2" 라는 문자열 저장
  - `REPOSITORY: myapp-stg`: REPOSITORY 변수에 "myapp-stg"라는 문자열 저장
  - `REGISTRY: ''`: REGISTRY 변수는 빈문자열로 초기화
  - `IMAGE_TAG: ''`: REGISTRY 변수는 빈문자열로 초기화
- `jobs`: GitHub Actions 워크플로우 내에서 실행할 작업 목록을 정의하는 부분. 각각의 step은 순차적으로 수행
  - `deploy`: 어떤 플랫폼에서 배포할지 정의
    - `runs-on: ubuntu-latest`: GitHub에서 제공하는 최신 Ubuntu 호스트를 사용해서 job을 실행. [runner 버전 확인](https://docs.github.com/en/actions/using-github-hosted-runners/using-github-hosted-runners/about-github-hosted-runners#standard-github-hosted-runners-for-public-repositories)
    - `steps`: 순차적으로 실행될 작업 정의
      - `name`: 각 단계(step)에 대한 설명(라벨)을 지정
      - `id`: 단계에 고유한 식별자를 부여. 이를 통해 이후 단계에서 해당 단계의 출력(output) 값을 참조가능
      - `uses`: GitHub Actions에서 미리 만들어진 액션([Marketplace](https://github.com/marketplace)에서 제공하는 액션)을 호출할 때 사용
      - `with`: 액션(uses)에 전달할 입력 인자를 지정
      - `run`: 셸 명령어나 스크립트를 직접 실행할 때 사용 <br/>
      <br/>
      - 각각의 step 설명
        - `Step 1: Checkout Repository`: 해당 리포지토리의 소스 코드가 워크플로우를 실행하는 러너의 파일 시스템으로 복사. 
          - `actions/checkout@v3`: [GitHub Marketplace](https://github.com/marketplace/actions/checkout)
        - `Step 2: Create application-secure.properties file`: 민감한 정보를 GitHub secrets로 부터 가져와서 application-secure.properties 파일을 현재 실행중인 러너의 디렉토리에 생성
          - `|`: 셀 스크립트를 여러줄 실행하기 위해 작성
          - `cd ./src/main/resources`: src/main/resources 위치로 이동
          - `touch ./application-secure.properties`: 현재 위치에서 application-secure.properties 파일 생성
          - `echo "내용" >> 파일이름`: `파일이름`에 `내용`을 작성
            - `echo "spring.mail.host=${{ secrets.MAIL_HOST }}" >> ./application-secure.properties`: 로컬 실행 환경과 동일한 application-sercure.properties를 만들도록 키=값 형태로 작성하고, 현재 실행 중인 러너의 application-secure.properties에 저장
        - `Step 3: Configure AWS credentials`: AWS CLI 및 이후의 AWS 관련 액션들이 사용할 수 있도록 AWS 자격증명을 설정
          - `aws-actions/configure-aws-credentials@v4`: [GitHub Marketplace](https://github.com/marketplace/actions/configure-aws-credentials-action-for-github-actions)
        - `Step 4: Login to AWS ECR`: ECR에 로그인하여 Docker 이미지를 푸시할 수 있도록 인증을 진행
          - `id: login-ecr`: 출력 값엔 ECR의 레지스트리 URL 포함. 다른 단계에서 사용하기 위해 login-ecr변수에 값 저장.
          - `aws-actions/amazon-ecr-login@v2`: [GitHub Marketplace](https://github.com/marketplace/actions/amazon-ecr-login-action-for-github-actions)
        - `Step 5: Generate REGISTRY environment variable`: 이전 단계에서 로그인 후 얻은 registry 값을 $GITHUB_ENV 파일에 기록하여, 이후 단계에서 전역 환경 변수 REGISTRY로 사용할 수 있게 함.
        - `Step 6: Set Timezone`: 컨테이너 실행 환경의 타임존을 Australia/Sydney로 설정
          - `uses: MathRobin/timezone-action@v1.1`: [GitHub Marketplace](https://github.com/marketplace/actions/set-timezone)
        - `Step 7: Generate IMAGE_TAG environment variable`: 현재 날짜와 시간을 기반으로 고유한 Docker 이미지 태그의 버전 이름을 생성하고 $GITHUB_ENV 파일에 기록
        - `Step 8: Build and Tag Docker Image`: 현재 디렉터리를 빌드 컨텍스트로 하여 Docker 이미지를 빌드
          - `docker build`: 도커 이미지 빌드
          - `-t 이미지:이미지태그`: 위의 코드 처럼 여러 개의 태그를 한번에 부여 가능. 
            - 여기서는 2개의 이미지 태그를 부여. 하나는 고유태그(IMAGE_TAG)로 버전 관리에 사용하고, 다른 하나는 latest태그로 항상 최신 이미지를 나타냄.
        - `Step 9: Push Docker Image to ECR`: 빌드된 Docker 이미지를 AWS ECR에 푸시하여 저장소에 업로드
          - `docker push 이미지:이미지태그`: Dokcer 이미지를 ECR 저장소에 올리는 명령어
        - `Step 10: Package Dockerrun.aws.json`: EB 배포를 위한 ZIP 패키지를 생성
          - `mkdir -p deploy`:
            - mkdir: 디렉토리 생성 (make directory)
            - -p: 상위 디렉터리가 존재하지 않을 경우 함께 생성
            - deploy: deploy 라는 이름의 디렉토리 생성
          - `cp 현재경로 복사할경로`
            - cp: 복사 (copy)
            - Dockerrun.aws.json: 현재 경로에 있는 Dockerrun.aws.json를 찾음
            - deploy/Dockerrun.aws.json: 현재실행중인 러너의 deploy 디렉토리 경로
          - `cd deploy && zip -r myapp.zip .`
            - cd deploy: deploy 디렉토리로 이동(cd: change directory)
            - &&: 그리고
            - zip -r myapp.zip .: 해당 디렉터리 내의 모든 파일과 폴더를 재귀적으로(-r 옵션) myapp.zip이라는 이름의 ZIP 파일로 압축
        - `Step 11: Deploy to Elastic Beanstalk`: 최종적으로 EB에 배포 
          - `einaregilsson/beanstalk-deploy@v22`: [GitHub Marketplace](https://github.com/marketplace/actions/beanstalk-deploy)
          - with:
            - `version_label`: 배포 버전 라벨로 GitHub Actions의 실행 번호를 사용 (자동 증가)

#### 10-1. eb-deploy.yml 실행
1. CI/CD 구축할 Github 리포지토리 > Actions 로 이동
2. eb-deploy.yml 옆의 Run Workflow 또는 코드 변경하여 GitHub에 push

<details>
<summary>EB에서 코드 에러 났을 때 에러 확인</summary>

1. `배포 에러`: Elastic Beanstalk의 배포 및 실행 로그 <br/>
    EB 자체에서 배포와 관련된 이벤트를 기록. EB 컨테이너가 어떻게 실행됐는지 적용이 잘 됐는지, Docker Image 잘 가져왔는지 확인 <br/>

    1-1. SSH 접속: EC2 로 이동 > 해당 EC2 선택 > 상단 Connect 선택 > EC2 Instance Connect 탭 > Username: root > Connect 선택 <br/>
    1-2. 에러 확인
      ```sh
      cd /var/log
      vi eb-engine.log
      ```

2. `코드에러` <br/>
    2-1. EB > Environment > Logs<br/>

3. `Spring Boot 애플리케이션 로그`<br/>
    비지니스 로직 오류, HTTP 요청 오류, DB 연결 문제, 환경 변수 오류 등을 확인 <br/>

    3-1. SSH 접속: EC2 로 이동 > 해당 EC2 선택 > 상단 Connect 선택 > EC2 Instance Connect 탭 > Username: root > Connect 선택 <br/>
    3-2. Docker ID 확인
      ```sh
      docker ps # ps: Process Status
      ```
    3-3. 최신 100줄의 로그 확인
      ```sh
      docker logs <컨테이너_ID> -f --tail=100
      ```
<br/>

4. `Elastic Beanstalk logs` <br/>
```sh
# Elastic Beanstalk logs
sudo cat /var/log/nginx/access.log 

# Elastic Beanstalk logs: 4xx로 시작되는 에러 잡기
sudo cat /var/log/nginx/access.log | grep " 4[0-9][0-9] "
```
</details> 
<br/>

## GitHub 브랜치에 따라 워크플로우 실행
Git Flow strategy에 따라서, main, develop, features 브랜치를 운영한다고 할 때 `eb-deploy.yml` 파일
```yml
name: Deploy to Elastic Beanstalk
on:
  push:
    branches:
      - main
      - develop

env:
  AWS_ACCESS_KEY: ${{ secrets.AWS_ACCESS_KEY_ID }}
  AWS_ACCESS_SECRET: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
  AWS_REGION: ap-southeast-2
  REPOSITORY: myapp-stg  
  REGISTRY: ''
  IMAGE_TAG: ''
  LATEST_TAG: ''

jobs:
  deploy:
    runs-on: ubuntu-24.04

    steps:
      - name: Checkout Repository
        uses: actions/checkout@v3

      - name: Create application-secure.properties file
        run: |
          cd ./src/main/resources
          touch ./application-secure.properties
          echo "spring.mail.host=${{ secrets.MAIL_HOST }}" >> ./application-secure.properties
          echo "spring.mail.port=${{ secrets.MAIL_PORT }}" >> ./application-secure.properties
          echo "spring.mail.username=${{ secrets.MAIL_USERNAME }}" >> ./application-secure.properties
          echo "spring.mail.password=${{ secrets.MAIL_PASSWORD }}" >> ./application-secure.properties
        
      - name: Configure AWS credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          aws-access-key-id: ${{ env.AWS_ACCESS_KEY }}
          aws-secret-access-key: ${{ env.AWS_ACCESS_SECRET }}
          aws-region: ${{ env.AWS_REGION }}
      
      - name: Login to AWS ECR
        id: login-ecr
        uses: aws-actions/amazon-ecr-login@v2
        with:
          mask-password: 'true'
    
      - name: Generate REGISTRY environment variable
        run: echo "REGISTRY=${{ steps.login-ecr.outputs.registry }}" >> $GITHUB_ENV

      - name: Set Timezone
        uses: MathRobin/timezone-action@v1.1
        with:
          timezoneLinux: 'Australia/Sydney'
          timezoneMacos: 'Australia/Sydney'
          timezoneWindows: 'AUS Eastern Standard Time'

      - name: Generate IMAGE_TAG environment variable
        run: echo "IMAGE_TAG=$(date +%Y-%m-%d_%H-%M-%S)" >> $GITHUB_ENV

      - name: Generate IMAGE_TAG and LATEST_TAG environment variables
        run: |
          if [ "${GITHUB_REF}" = "refs/heads/main" ]; then
            echo "IMAGE_TAG=prod-$(date +%Y-%m-%d_%H-%M-%S)" >> $GITHUB_ENV
            echo "LATEST_TAG=latest" >> $GITHUB_ENV
          elif [ "${GITHUB_REF}" = "refs/heads/develop" ]; then
            echo "IMAGE_TAG=staging-$(date +%Y-%m-%d_%H-%M-%S)" >> $GITHUB_ENV
            echo "LATEST_TAG=staging" >> $GITHUB_ENV
          fi  

      - name: Push Docker Image to ECR
        if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/develop'
        run: |
          docker push ${{ env.REGISTRY }}/${{ env.REPOSITORY }}:${{ env.IMAGE_TAG }}
          docker push ${{ env.REGISTRY }}/${{ env.REPOSITORY }}:${{ env.LATEST_TAG }}

      - name: Package Dockerrun.aws.json
        run: |
          mkdir -p deploy
          cp Dockerrun.aws.json deploy/Dockerrun.aws.json
          cd deploy && zip -r myapp.zip .
      
      - name: Deploy to Elastic Beanstalk Staging
        if: github.ref == 'refs/heads/develop'
        uses: einaregilsson/beanstalk-deploy@v22
        with:
          aws_access_key: ${{ env.AWS_ACCESS_KEY }}
          aws_secret_key: ${{ env.AWS_ACCESS_SECRET }}
          application_name: 'myapp-stg'
          environment_name: 'myapp-stg-env'
          version_label: ${{ github.run_number }}
          region: ${{ env.AWS_REGION }}
          deployment_package: deploy/myapp.zip
          use_existing_version_if_available: true

      - name: Deploy to Elastic Beanstalk Production
        if: github.ref == 'refs/heads/main'
        uses: einaregilsson/beanstalk-deploy@v22
        with:
          aws_access_key: ${{ env.AWS_ACCESS_KEY }}
          aws_secret_key: ${{ env.AWS_ACCESS_SECRET }}
          application_name: 'myapp-prod'
          environment_name: 'myapp-prod-env'
          version_label: ${{ github.run_number }}
          region: ${{ env.AWS_REGION }}
          deployment_package: deploy/myapp.zip
          use_existing_version_if_available: true
```

***- eb-deploy.yml 설명*** <br/>

- `on`: GitHub Actions 워크플로우를 실행할 트리거(이벤트)를 지정.
  - `push`: GitHub에 push 될 때 워크플로우 실행. push 외에도 pull_request, schedule, relese 등 있음. 
    - `branches`: 어떤 브랜치에 이벤트가 일어날 때 실행 할 것인지를 지정. 여기서는 `main`, `develop` 브랜치 (필요하다면 `feature/**`도 추가).
- `jobs`: GitHub Actions 워크플로우 내에서 실행할 작업 목록을 정의하는 부분. 각각의 step은 순차적으로 수행
  - `deploy`: 어떤 플랫폼에서 배포할지 정의

      <br/>
  - 변경 된 step 설명 <br/>
    - `Build and Tag Docker Image`: 현재 디렉터리를 빌드 컨텍스트로 하여 Docker 이미지를 빌드<br/>
      - `if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/develop'`: `main`, `develop` 브랜치에서만 실행 <br/>
    - `Push Docker Image to ECR`: 빌드된 Docker 이미지를 AWS ECR에 푸시하여 저장소에 업로드 <br/>
      - `if: github.ref == 'refs/heads/main' || github.ref == 'refs/heads/develop'`: `main`, `develop` 브랜치에서만 실행 <br/>
    - `Deploy to Elastic Beanstalk Staging`: 최종적으로 EB에 배포 (`staging` server)  <br/>
      - `if: github.ref == 'refs/heads/develop'`: `develop` 브랜치일 경우 실행 <br/>
    - `Deploy to Elastic Beanstalk Production`: 최종적으로 EB에 배포 (`production` server) <br/>
      - `if: github.ref == 'refs/heads/main'`: `main` 브랜치일 경우 실행 <br/>
<br/>

# AWS ECS
AWS ECS (Elastic Container Service)는 AWS에서 만든 orchestration 툴

## 용어 정리
- `Cluster`: 하나의 프로젝트
- `Servie`: 하나의 마이크로 서비스
  - Service를 이용하여 task를 쉽게 복제
  - 고장난 task는 쉽게 replace.
- `Task`: 서로 붙어 있어야 할 컨테이너들을 묶는 단위. docker compse와 비슷. 
  - Task 안에서 통신가능. 볼륨장착, 컨테이너간 통신, 서비스간 통신 가능.
  - Task 안에서의 서로 `http://localhost:포트`로 통신.
- `Load balancer`: 유저가 서버에 들어왔을 때 task들에 균등하게 안내해주는 역할
<br/>
<br/>

### ECS 구조
<img src="https://github.com/Narae-H/study-docker/blob/main/assets/readme/aws_ecs.png?raw=true" width="500px" alt="aws ecs 구조"><br/>
<sup>이미지 출처: [코딩애플](https://codingapple.com/)</sup>
<br/>

### 치킨집 프랜차이즈로 비유
<img src="https://github.com/Narae-H/study-docker/blob/main/assets/readme/aws_ecs_chicken.png?raw=true" width="500px" alt="aws ecs 구조비유"><br/>
<sup>이미지 출처: [코딩애플](https://codingapple.com/)</sup>
<br/>

## 사용법
### 1. AWS ECS 접속
1.  [AWS ECS](https://ap-northeast-2.console.aws.amazon.com/ecs/v2/clusters?region=ap-northeast-2) 접속 > `region`은 `Seoul` 선택

### 2. Cluster 생성
클러스터는 프로젝트하나라고 생각하면 되는데, 정확히는 내가 컴퓨터를 몇대나 미리 점유할지 정하는 용도.
1. `Create cluster` 선택
<br/>
<br/>

2. 아래와 동일하게 입력
  - `Cluster name`: `testCluster`
  - `Infrastructure`: `AWS Fargate (Serverless)`
    - `AWS Fargate`: 서버리스 형태로 CPU, RAM을 필요할 때만 꺼내쓰는 방식. 알아서 서버 관리해줌. 대신 EC2보다 20% 정도 더 비쌈.
    - `Amazon EC2 Instances`: 직접 서버 고르고 관리. 대신 서버 비용은 조금 더 쌈.
<br/>

3. `Create`
<br/>

### 3. Task definition
Task를 어떻게 띄울지 정의하는 파일. 무슨 이미지들을 어떤 포트에 어떤 환경변수를 넣어서 띄울지 작성하는 파일. docker compose와 성격 동일. <br/>
Cluster에서 예약해놨던 컴퓨터를 task 1개가 얼마나 점유할것(CPU, Memory)인지 결정하는 것이므로 Task의 CPU, Memory의 총합이 이전에 선택한 값을 넘어가면 안됨. 

1. 왼쪽메뉴에서 `Task definitions` 선택 > `Create new task definition` 선택
2. 아래와 동일하게 입력
  - `Task definition configuration`
    - Task definition family: task1
  - `Infrastructure requirements`
    - Launch type: AWS Fargate
    - Operating system/Architecture: Linux/X86_64
    - Task size: CPU: 1 vCPU, Memory: 2 GB
    - Task execution role: Create new role
  - `Container-1`
    - Container details 
      - Name: webserver
      - Image URI: docker.io/내아이디/리포지토리명:태그명 ([docker hub](https://hub.docker.com/)에 올려놓은 이미지)
    - Container port: remove
    - Recource allocation limits
      - CPU: 0.5
      - Memory hard limit(최대): 1 GB
      - Memory soft limit(최소): 1 GB
    - HealthCheck(자동으로 n초마다 특정 페이지 들어가서 정상적으로 접속되는지 확인)
      - Command: timeout 10s bash -c ': > /dev/tcp/localhost/8080' || exit 1
  - `Container-2`
    - Container details 
      - Name: nginx
      - Image URI: docker.io/내아이디/리포지토리명:태그명 ([docker hub](https://hub.docker.com/)에 올려놓은 이미지)
    - Container port: 80
    - Recource allocation limits
      - CPU: 0.5
      - Memory hard limit(최대): 1 GB
      - Memory soft limit(최소): 1 GB
    - HealthCheck(자동으로 n초마다 특정 페이지 들어가서 정상적으로 접속되는지 확인)
      - Command: CMD-SHELL, curl -f http://localhost:80 || exit 1

3. Create
<br/>


### 4. Service 생성
1회성 작업같은걸 할때는 서비스 없어도 클러스터 사용 가능. 서비스는 어떤 컴퓨터에 task들을 띄울지 결정하는 부분.

1. 왼쪽메뉴에서 `Clusters` 선택 > 생성한 cluster (testCluster) 선택
2. `Services` 탭 선택> `Create` 선택
3. 아래와 동일하게 입력
  - Compute configuration: Launch type (Fargate)
    - Capacity provider strategy: EC2, Fargate에 task를 분산해서 띄우는 것 가능
    - Launch type: Fargate 쓴다고 해놨으므로 분산해서 띄울 필요 없음.
  - Deployment configuration
    - Application type(task만 실행할지, 아님 task랑 서비스 같이 실행할지): Service
    - Task definition:
      - Family: task1(아까 생성해둔 task)
      - Revision: 4(latest)
      - Service name: nginx-server-service
      - Service type: Replica
      - Desired tasks(task몇 개 띄울지): 1
    - Deployment options(tasks 수정사항 생겼을 때, 기존 tasks를 어떻게 끄고 새로운 tasks 띄울지 설정)
      - Deployment type: Rolling update
        - Rolling update: 수정된 tasks를 먼저띄우고(수정된 tasks와 이전 tasks가 공존하는 상태), 이전 tasks를 하나씩 순차적으로 종료. 간편한데 유저들이 이전 tasks와 수정된 tasks로 골고루 안내되는 상황이 발생할 수 있음.
        - Blue/green deployment: 수정된 tasks를 띄우고, 한번에 이전 tasks에서 수정된 tasks로 갈아탐.
  - Service Connect (서비스끼리 통신하기 위해서 서비스에 이름을 지어주는 옵션): User Service Connect 선택
    - Service Connect Configuration:
      - Client side only: 지금 만드는 서비스가 다른 서비스에 연결 필요
      - Client and Server: 다른 서비스도 내 서비스에 연결필요한 경우
  - Networking
    - VPC(가상 네트워크): default로 선택되어있는걸로 둠.
    - Subnets
    - Security group: Create a new security group
      - Security group name: ecs-service-sg
      - Inbound rules for security groups: Customised TCP / TCP / 80 / Anywhere
    - Public IP(task가 외부와 통신가능, 외부 사이트의 이미지도 받아올 수 있음): enable
  - Load balancing (유저가 서버에 들어왔을때 균등하게 task들로 나눠줌)
    - Load balancer type: Application Load Balancer
    - Container: nginx1 80:80
    - Application Load balancer: Create a new load balancer
      - Load balancer name: alb-test
      - Healthcheck period: 10
      - Listener: Create a new listener
        - Port: 80
        - Protocol: HTTP
      - Target group: Create a new target group
        - Target group name: ecs-testCl-testservice
        - Protocol: HTTP

4. Create

### 5. 사이트 접속
1. 왼쪽메뉴에서  `Clusters` 선택 > 생성된 cluster 선택 > Service 탭 이동
2. 서비스의 status가 `Active`(초록색)이면, task가 잘 돌아가는 중.
3. Load balancer status 부분에서 `로드밸런서 보기` 선택
4. Load blancer의 DNS주소를 가져와서 브라우저 주소창에 `http://로드밸런서DNS주소` 입력력

### 6. Task update
1. 코드짠걸 새로 빌드해서 이미지를 어딘가(ex) Docker Hub)에 올림.
2. 기존에 있던 Task definition을 수정해서 어떤 이미지를 사용할건지 기재
3. 생성된 Cluster 선택해서 `Service update` 버튼 누르기

### 7. Cluster 삭제
1. Servie 삭제
2. Cluster 삭제
3. Load balancer 삭제

<br/>
<br/>

# `ECS` vs `Elastic Beanstalk +Docker` 차이점 요약
| 기능               | AWS ECS (Fargate)                         | Elastic Beanstalk (Docker)       |
|--------------------|-----------------------------------------|---------------------------------|
| 이미지 저장소     | AWS ECR                                  | Docker Hub 또는 AWS ECR         |
| 로드 밸런서       | ALB, NLB (수동 설정 필요)               | 기본 제공 (ELB)                 |
| 오토 스케일링     | 수동/자동 컨테이너 개수 조정 가능       | 간단한 설정으로 자동 스케일링 지원 |
| 배포 방식         | AWS Fargate 또는 EC2 기반으로 컨테이너 배포 <br/>  Task 정의 → 서비스 → 클러스터 | 간단한 Dockerrun.aws.json 배포 |
| CI/CD        | CodePipeline, GitHub Actions             | GitHub Actions, Beanstalk CLI  |
| 유연성            | 고급 네트워크 및 보안 설정 가능         | 제한적 커스터마이징  |
| 운영방식 | 복잡한 마이크로서비스 아키텍처 (MSA)    | 단일 애플리케이션         |
| 적합한 사용 사례      | 여러개의 마이크로서비스 + 세밀한 설정 | 단일 컨테이너 + 빠르고 쉽게 배포  |
- MSA로 변경해야 할 경우 나중에 ECS로 쉽게 전환할 수 있음! 









