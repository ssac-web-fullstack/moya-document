# Trouble Shooting (KMS)

## **Issue**

### _Server Project_

- API 서버 배포 ⇒ github, netlify, heroku etc. 활용?

### _Customer Project_

### _Admin Project_

- 카테고리 생성/수정/삭제
- 게시글 차단/삭제/복구

### _Git & Github_

- 깃헙 remote에 있는 branch를 local PC의 remote branch 상태에 최신 업데이트 반영

  - `git branch -a` (local에서 remote branch state 확인)
  - `git fetch`

- 깃헙 remote에 업데이트 된 branch (다른 사람이 올려 놓은 브랜치)를 내 local로 가져오기

  - `git checkout -t origin/[브랜치명]`

- 깃헙 remote에 있는 브렌치와 local branch 모두 최신 상태로 연동 명령어? <br/>
  (한번에 모두 가져와서 local 최신 업데이트 반영)

- feature 브랜치를 develop 브랜치로 merge를 하고 나면 feature 브랜치가 사라짐 <br/>
  => develop 브랜치를 다시 merge 이전 상태로 reset하면 사라졌던 feature 브랜치가
  다시 살아나나?

### _Etc._

- NVM 으로 노드 버전 관리 (협업 위함)

- 리액트 query 라이브러리, Context API, useReducer 등 중요

- nodemailer (이메일 발송) // 테스트; mailtrap.io

- nodemon npm package?
