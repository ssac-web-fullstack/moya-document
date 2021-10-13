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
