# 다세대 관리 앱

임대료 · 계약일 · 시설 이슈를 두 분의 폰에서 동시에 보고 수정할 수 있는 앱입니다.
정적 HTML(React, 별도 빌드 없음) + Firebase(Firestore·Authentication)로 만들어졌고,
실시간 동기화가 필요해 데이터는 로컬이 아니라 Firebase 서버에 저장됩니다.

## 1. Firebase 프로젝트 만들기

1. https://console.firebase.google.com 접속 → "프로젝트 추가" → 이름 입력(예: `family-rental`) 후 생성.
2. 왼쪽 메뉴 **Authentication** → "시작하기" → 로그인 방법에서 **이메일/비밀번호** 사용 설정.
3. **Authentication → Users(사용자)** 탭 → "사용자 추가"로 두 분의 이메일과 비밀번호를 직접 등록.
   (이 앱에는 회원가입 화면이 없습니다. 아무나 계정을 만들 수 없도록, 계정은 반드시 Firebase 콘솔에서 관리자가 직접 추가합니다.)
4. 왼쪽 메뉴 **Firestore Database** → "데이터베이스 만들기" → 프로덕션 모드로 시작 → 리전은 `asia-northeast3(서울)` 추천.
5. Firestore **규칙(Rules)** 탭에서 이 폴더의 `firestore.rules` 내용을 그대로 붙여넣고 게시(Publish).
   (로그인한 사용자만 읽고 쓸 수 있도록 제한하는 규칙입니다.)

## 2. 앱에 연결하기

1. Firebase 콘솔 → 프로젝트 설정(톱니바퀴) → 일반 탭 → "내 앱" → 웹 앱 추가(</> 아이콘).
2. 앱 닉네임 아무거나 입력 후 등록하면 `firebaseConfig` 객체가 나옵니다.
3. `multi-family/index.html` 파일을 열어 상단의 `FIREBASE_CONFIG` 객체 값을 방금 복사한 값으로 교체합니다.

```js
const FIREBASE_CONFIG={
  apiKey:"...",
  authDomain:"...",
  projectId:"...",
  storageBucket:"...",
  messagingSenderId:"...",
  appId:"..."
};
```

이 값들은 비밀키가 아니라 공개되어도 되는 클라이언트 식별 정보입니다. 실제 보안은 1-5의 Firestore 규칙과 로그인 여부로 이루어집니다.

## 3. 두 분이 접속하는 방법

가장 간단한 방법은 GitHub Pages로 배포하는 것입니다.

1. 이 저장소(GitHub) → Settings → Pages → Source를 `main` 브랜치, 폴더는 `/ (root)`로 설정.
2. 배포되면 `https://<사용자명>.github.io/routine-c/multi-family/` 주소로 접속 가능.
3. 두 분 폰에서 그 주소로 접속 후 로그인 → 홈 화면에 추가하면 앱처럼 사용할 수 있습니다(iOS: 공유 → 홈 화면에 추가, Android: 브라우저 메뉴 → 홈 화면에 추가).

## 4. 기능

- **호실 관리**: 호실명, 세입자, 계약 형태(월세/전세/공실), 보증금·월세, 계약 시작/종료일, 메모. 계약 종료일이 다가오면 D-day가 색으로 표시됩니다(30일 이내 빨강, 90일 이내 노랑).
- **시설 이슈**: 호실별(또는 공용) 이슈 등록, 상태(접수/진행중/완료) 관리. 각 호실 카드에 미해결 이슈 개수가 표시됩니다.
- 두 분 중 한쪽에서 데이터를 추가·수정하면 Firestore 실시간 리스너를 통해 다른 쪽 화면에도 즉시 반영됩니다.

## 5. 로컬에서 미리 보기

Firebase Auth는 `file://`로 열면 일부 브라우저에서 제한될 수 있으니, 로컬 서버로 열어보는 것을 권장합니다.

```bash
cd multi-family
python3 -m http.server 8080
# http://localhost:8080 접속
```
