# Trouble Shooting (LKN)

<br>

---

## ***npx create-react-app 명령어 404 Error 발생***
- `npx create-react-app 생성할_프로젝트_폴더명` 명령어 실행 시 404 Error 발생
  - Error message: npm ERR! 404 Not Found - GET https://registry.npmjs.org/creat-react-app - Not found 

<br>

- **해결 방법 1.**
  - `npm config set registry http://registry.npmjs.org` 를 실행

- **해결 방법 2.**
  - npm 으로 create-react-app 실행
    - `npm install -g create-react-app` 으로 cra 설치
    - `create-react-app 생성할_프로젝트_폴더명` 으로 생성할 프로젝트 폴더에서 cra 실행 

<br>

---

## ***npm install package.json 이슈***
- 팀원과 개발환경을 공유하기 위해 package.json을 pull 받아서 node_modules 생성
- `npm install package.json` 실행 이후 package.json과 package-lock.json 파일 변경됨
- 변경된 파일 commit 이전에는 다른 branch 넘어갈 수 없음

<br>

- package.json에 버전이 'xxx':^1.0.0 으로 기재된 경우 --> 1.x.x 에 해당하는 최신 버전 설치
- npm 버전이 상이하고 시점이 다를 경우 정확하게 동일한 개발환경이 구축되지 않을 수 있음
- dependencies 버전을 'xxx':1.0.0 으로 고정해도 xxx와 의존관계가 있는 yyy가 ^1.0.0 으로 작성 시 다른 버전 설치 가능성 존재
- `npm install` 사용 시 package.json, package-lock.json 덮어씀

<br>
- **해결 방법 0.**
  - `git stash`로 package 변경사항을 임시저장하여 이전 단계로 돌려놓기
  - `git stash drop`으로 저장된 변경사항 제거
  - 혹은 파일의 stage, commit, pull 여부에 따라 `git reset ~~` 사용
  - 이후 해결 방법 1 (2,3은 비현실적) 사용

- **해결 방법 1.**
  - `npm ci package-lock.json` 으로 node_modules 디렉토리 구축
    - `npm ci` 는 `npm install` 처럼 package-lock.json을 갱신하지 않음
    - 같은 node_modules 구성 가능
    - package.json, package-lock.json이 변경되지 않음

- **해결 방법 2.**
  - package.json 의 dependencies version을 ^ 없이 고정
  - 번거롭고 효율성 떨어짐

- **해결 방법 3.**
  - node_modules를 git으로 공유
  - 디렉토리가 너무 무겁기 때문에 효율성 떨어짐
