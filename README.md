# 가계쀼 링크 호스팅

`hosting/`은 빌드 단계 없는 정적 사이트 루트다. GitHub Pages나 동일한 HTTPS
정적 호스팅에 이 폴더의 **내용물**을 루트로 배포한다. GitHub Pages에서는
`404.html`이 `/i/123456`을 `/i/?code=123456`으로 넘겨 설치되지 않은 기기도 초대
페이지를 볼 수 있게 한다.

## 배포 전 교체

1. `.well-known/assetlinks.json`의
   `REPLACE_WITH_PLAY_APP_SIGNING_SHA256`을 Play Console의 **앱 무결성 > 앱 서명 키
   인증서 > SHA-256 인증서 지문**으로 교체한다. 콜론이 포함된 전체 지문을 쓴다.
2. `.well-known/apple-app-site-association`의 `REPLACE_WITH_TEAM_ID`를 Apple
   Developer Team ID로 교체한다.
3. 도메인이 확정되면 아래 값을 모두 같은 호스트로 바꾼다.
   - `lib/core/links/invite_links.dart` 기본값 또는 빌드의
     `--dart-define=INVITE_LINK_HOST=새호스트`
   - `android/app/src/main/AndroidManifest.xml`의 App Link `android:host`
   - `ios/Runner/Runner.entitlements`의 `applinks:` 항목
   - 배포 대상 도메인과 Google OAuth 브랜드 링크

`.well-known` 두 파일은 리디렉션 없이 HTTPS 200으로 응답해야 하며 JSON MIME
타입을 권장한다. iOS 앱 배포 전 Runner 타깃에서 **Signing & Capabilities >
Associated Domains** capability도 직접 활성화한다.

## 확인

Android 설치 후 다음 명령으로 도메인 검증 상태를 확인한다.

```bash
adb shell pm get-app-links com.gagyebbu.app
```

브라우저에서 두 `.well-known` URL이 인증 없이 열리는지 확인하고, Apple의 AASA
Validator에서 `https://호스트/.well-known/apple-app-site-association`을 검사한다.
실기기에서는 `https://호스트/i/123456`을 눌러 앱 열림과 코드 보존을 확인한다.

## Google OAuth 브랜딩

Google Cloud Console의 **Google Auth Platform > Branding**에서 앱 이름과 지원
이메일을 확인하고 홈페이지를 `https://호스트/`, 개인정보 처리방침을
`https://호스트/privacy.html`, 이용약관을 `https://호스트/terms.html`로 등록한다.
같은 호스트를 승인된 도메인에 추가하고 도메인 소유권 확인을 마친 뒤 브랜딩을
게시한다.
