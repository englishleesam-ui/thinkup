# ThinkUp 설정 가이드

## 1단계: Firebase 프로젝트 생성

1. https://console.firebase.google.com 접속
2. [프로젝트 추가] 클릭
3. 프로젝트 이름: `thinkup-attendance` (원하는 이름)
4. Google Analytics: 사용 안 함 선택 후 완료

## 2단계: Firestore 데이터베이스 생성

1. 왼쪽 메뉴 → [빌드] → [Firestore Database]
2. [데이터베이스 만들기] 클릭
3. 테스트 모드 선택 (나중에 규칙 변경 필요)
4. 위치: asia-northeast3 (서울) 선택

## 3단계: Firebase 설정 코드 가져오기

1. 프로젝트 설정 (톱니바퀴 아이콘) → [프로젝트 설정]
2. 스크롤 내려서 [앱 추가] → 웹(</>)
3. 앱 닉네임: thinkup → [앱 등록]
4. `firebaseConfig` 객체 복사

## 4단계: firebase-config.js 수정

`firebase-config.js` 파일의 YOUR_... 부분을 복사한 값으로 교체:

```js
const firebaseConfig = {
  apiKey: "실제값",
  authDomain: "실제값",
  projectId: "실제값",
  storageBucket: "실제값",
  messagingSenderId: "실제값",
  appId: "실제값"
};
```

## 5단계: Firestore 보안 규칙 설정

Firebase Console → Firestore → [규칙] 탭에서 아래로 교체:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /schedules/{doc} {
      allow read: if true;
      allow write: if false;  // 앱에서만 쓰기 (admin 페이지에서)
    }
    match /students/{doc} {
      allow read: if true;
      allow write: if false;
    }
    match /attendance/{date}/students/{studentId} {
      allow read, write: if true;
    }
  }
}
```

> 주의: 위 규칙은 편의상 설정. 실제 운영 시 더 엄격하게 설정 권장.

## 6단계: GitHub Pages 배포

1. GitHub에서 새 저장소 생성 (예: `thinkup`)
2. thinkup 폴더 전체를 push
3. Settings → Pages → Source: main branch / root
4. 배포 주소: `https://[username].github.io/thinkup`

## 7단계: Gabia에서 thinkup.kr 연결

1. Gabia 도메인 관리 → DNS 설정
2. CNAME 레코드 추가:
   - 호스트: @ (또는 www)
   - 값: `[username].github.io`
3. GitHub 저장소 Settings → Pages → Custom domain: `thinkup.kr`

## 완료 후 테스트 순서

1. `thinkup.kr/admin` 접속 → chris / 0914 로그인
2. PDF 업로드해서 학생 등록
3. `thinkup.kr/student` 태블릿에서 열기 → 코드 입력 테스트
4. `thinkup.kr/teacher` 폰에서 열기 → 선생님 선택 후 실시간 확인
