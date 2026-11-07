# 옛날 카카오톡 느낌의 실시간 채팅방

이 프로젝트는 GitHub Pages에 올릴 수 있는 정적 웹사이트입니다. 여러 사람이 실제로 메시지를 주고받으려면 Firebase 설정이 필요합니다.

## 1. Firebase 연결하기

1. https://console.firebase.google.com/ 에서 프로젝트를 만듭니다.
2. 프로젝트 설정에서 웹 앱을 추가하고 Firebase 설정값을 복사합니다.
3. Authentication > Sign-in method에서 **Anonymous(익명)** 로그인을 켭니다.
4. Firestore Database를 생성합니다.
5. `index.html`의 `firebaseConfig` 부분에 내 프로젝트 설정값을 붙여 넣습니다.

## 2. Firestore 규칙

Firestore > Rules에 아래 규칙을 넣고 게시합니다.

```text
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /messages/{messageId} {
      allow read: if request.auth != null;
      allow create: if request.auth != null
        && request.resource.data.keys().hasAll(['text', 'nickname', 'uid', 'createdAt'])
        && request.resource.data.text is string
        && request.resource.data.text.size() > 0
        && request.resource.data.text.size() <= 500
        && request.resource.data.nickname is string
        && request.resource.data.nickname.size() <= 16
        && request.resource.data.uid == request.auth.uid;
      allow update, delete: if false;
    }
  }
}
```

이 규칙은 로그인된 익명 사용자에게 공개 채팅방 읽기와 메시지 추가를 허용합니다. 공개 링크를 아는 사람은 누구나 들어올 수 있으므로 실명, 전화번호, 학교, 주소 등 개인정보를 쓰지 마세요. 이 기본 예제에는 관리자 삭제, 신고, 스팸 방지 기능이 없습니다.

## 3. GitHub에 올리고 배포하기

1. GitHub에서 새 Repository를 만듭니다.
2. `index.html` 파일을 저장소 최상위에 업로드합니다.
3. 저장소의 Settings > Pages로 이동합니다.
4. Build and deployment에서 `Deploy from a branch`를 선택합니다.
5. Branch는 `main`, 폴더는 `/ (root)`로 선택하고 Save를 누릅니다.
6. 잠시 기다리면 `https://사용자이름.github.io/저장소이름/` 주소로 접속할 수 있습니다.

## 파일 구성

- `index.html`: 채팅방 화면과 실시간 채팅 기능
- `README.md`: 설정 및 배포 안내
