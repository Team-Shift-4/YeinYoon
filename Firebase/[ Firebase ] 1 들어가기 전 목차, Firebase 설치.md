# [ Firebase ] 1. 들어가기 전 목차, Firebase 설치 및 연동 방법

상태: 작성 완료
생성 일시: 2022년 11월 19일 오후 10:50
중요도: ★☆☆
최종 편집 일시: 2022년 12월 9일 오전 10:11
태그: 🔥 Firebase

참고한 강의 / Coding Apple - 당근마켓을 만들며 배워보는 FireBase

해당 문서는 대학 조별과제에서 필요에 따라 작성한 개인공부용 가이드입니다.
자세한 내용 및 세세한 설명이 필요할 경우 본가([https://codingapple.com/](https://codingapple.com/))에서 직접 결제하고 수강하시길 바랍니다.

# 개요

---

본 가이드는 **FireBase 8.6.5** 버전 기준으로 작성되었다.
Vue.js에 연동시키지 않고 HTML과 CSS만으로 만드는 당근마켓 프로젝트 소스코드를 예제로 삼는다.
FireBase 사이트에서 기본적인 프로젝트 생성을 마쳤다는 시점부터 시작하며 문법 위주의 설명으로진행될 예정. 프로젝트 생성 안했으면 구글 붙잡고 물어서 알아서 생성하고 오셈. 
어디까지나 기본적인 사용법에 대한 설명이기 때문에 활용은 개인의 역량에 맡긴다.

소스코드 상에 **JQuery** 문법이 자주 나올 예정이기에 JQuery의 기본적인 문법들을 알고 있는 상태라면 이해하기 수월하다.

---

# 가이드를 읽는 방법

---

# 챕터 제목

### 해당 챕터의 문서

### 해당 챕터의 도움말

---

# 목차 (미작성된 가이드 챕터 : 보라색)

---

1. FireBase 설치 및 연동 방법 ( HTML에서 사용 & Vue.js 또는 React.js에서 사용 )
2. FireStore DataBase에서 데이터 불러오기, 저장하기 (R/W)
3. FireStore Storage에 이미지 업로드하기 (서버 스토리지)
4. FireBase Authentication - 회원가입 만들기
5. FireBase Authentication - 로그인 만들기
6. 로그인한 유저의 정보 가져오기
7. 수정 기능 만들기
8. FireStore Rules 규칙 정리
9. 채팅기능 1 - 유저 collection & 채팅방 collection 만들기
10. 채팅기능 2 - Firebase DB Query
11. 채팅기능 3 - 메세지 DB 저장하기, DB 변동사항 실시간 반영
12. Admin 권한과 Data validation
13. FireBase Hosting
14. Functions로 서버기능 만들기 - Functions 설치
15. Functions로 만드는 실시간 알림기능

---

# 1. FireBase 설치 및 연동 방법

프로젝트 폴더 알아서 생성하고 VSCode로 연다.

**호스팅으로 프로젝트를 배포해야할 경우를 대비해 firebase-tools 9.23.1 설치 (글로벌 설치)**

```jsx
npm install -g firebase-tools@9.23.1
```

**firebase에 로그인**

```jsx
firebase login
```

**firebase 초기화 작업을 진행한다**

```jsx
firebase init

// 초기화 작업 질문과 답변
Are you ready to proceed? -> y
Which Firebase features do you want to set up for this directory?  -> 사용할 기능들 선택 // firestore, hosting(optional), stoarage
Please select an option -> Use an existing project
Select a default Firebase project for this directory -> firebase 사이트에서 만든 내 프로젝트 선택
이어지는 질문들 전부 엔터치되 Y/N 답변 형식은 N으로 대답하기
```

### HTML만 사용하는 경우 → HTML파일 생성할 때마다 하단 스크립트 기입

```jsx
<script src="https://www.gstatic.com/firebasejs/8.6.5/firebase-app.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.6.5/firebase-auth.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.6.5/firebase-firestore.js"></script>
<script src="https://www.gstatic.com/firebasejs/8.6.5/firebase-storage.js"></script>

<script>
var firebaseConfig = {
  apiKey: "AIzaSyD4Jbqd9RgZd_AHeLNX-n",
  authDomain: "test-78694.firebaseapp.com",
  projectId: "test-78694",
  등 파이어베이스 콘솔에 있던 SDK 설정내용 ~~
};
firebase.initializeApp(firebaseConfig);
</script>
```

### Vue.js 또는 React.js에 연동하는 경우 → NPM을 이용한 설치와 코드 기입

 1. 해당 프로젝트 경로에서 터미널을 열고 Firebase 8.6.5 설치

```jsx
npm install firebase@8.6.5
```

1. index.js 또는 main.js 파일에 연동 코드 기입

```jsx
import firebase from "firebase/app";
import "firebase/firestore";

var firebaseConfig = {
  apiKey: "AIzaSyD4Jbqd9RgZd_AHeLNX-n",
  authDomain: "test-78694.firebaseapp.com",
  projectId: "test-78694",
  등 파이어베이스 콘솔에 있던 SDK 설정내용 ~~
};

firebase.initializeApp(firebaseConfig);
export const db = firebase.firestore();
```

1. 이후 원하는 .js파일에서 DB관련 코드를 작성하고 싶을 경우

```jsx
import {db} from './index.js'// index.js파일에서 파이어베이스 모듈을 import
import "firebase/firestore"; // firestore database를 사용하기 위한 모듈을 import

// DB관련 함수 사용 가능
```

---