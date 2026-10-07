# 가계쀼 링크 호스팅

`hosting/`은 빌드 단계 없는 `gagyebbu.newz.co.kr` 정적 사이트 루트다. GitHub
Pages에 이 폴더의 **내용물과 dotfile을 모두** 복사한다. 특히 `.nojekyll`,
`.well-known/`, `CNAME`을 빠뜨리면 association 파일이나 커스텀 도메인이 깨진다.
`404.html`은 유효한 `/i/123456` 요청만 `/i/?c=123456`으로 넘긴다.

## Android

`.well-known/assetlinks.json`에는 다음 SHA-256 인증서 지문을 모두 넣는다.

1. Play Console의 **앱 무결성 > 앱 서명 키 인증서** SHA-256
2. Play 밖에서 설치하는 QA 빌드용 **upload key 인증서** SHA-256

라이브 assetlinks에는 두 인증서가 이미 등록되어 있다. 저장소의
`REPLACE_WITH_PLAY_APP_SIGNING_SHA256`과 `REPLACE_WITH_UPLOAD_KEY_SHA256`을
각 실제 지문으로 교체한 뒤 배포한다.

응답 헤더를 확인한다. 결과는 `200`이고 Content-Type은 `application/json`이어야
한다.

```bash
curl -sI https://gagyebbu.newz.co.kr/.well-known/assetlinks.json
adb shell pm get-app-links com.gagyebbu.app
```

실기기에서 `https://gagyebbu.newz.co.kr/i/123456`과 초대 페이지의
`앱에서 열기` 버튼을 각각 확인한다.

## iOS (오너 작업)

1. Apple Developer의 App ID `com.gagyebbu.app`에서 Associated Domains를 먼저
   활성화한다.
2. Xcode의 Runner 타깃에서 **Signing & Capabilities > + Capability > Associated
   Domains**를 추가한다.
3. Xcode가 entitlement를 만든 뒤 아래 값이 정확한지 확인한다. capability를 먼저
   활성화하지 않은 채 저장소에 entitlement만 넣으면 archive가 실패할 수 있다.

```xml
<key>com.apple.developer.associated-domains</key>
<array>
    <string>applinks:gagyebbu.newz.co.kr</string>
</array>
```

AASA의 앱 ID는 `8LDXWG7HQF.com.gagyebbu.app`이다. 원본과 Apple CDN 캐시를 모두
확인한다.

- `https://gagyebbu.newz.co.kr/.well-known/apple-app-site-association`
- `https://app-site-association.cdn-apple.com/a/v1/gagyebbu.newz.co.kr`

두 association 파일은 리디렉션 없이 HTTPS 200으로 응답해야 한다.

## 앱 호스트 설정

Dart 기본 호스트는 `gagyebbu.newz.co.kr`이다. 다른 호스트를 시험할 때는
`--dart-define=INVITE_LINK_HOST=새호스트`를 사용하고 Android Manifest, 배포
도메인, association 파일을 같은 값으로 맞춘다.

## Google OAuth 브랜딩

Google Cloud Console의 **Google Auth Platform > Branding**에서 홈페이지를
`https://gagyebbu.newz.co.kr/`, 개인정보 처리방침을
`https://gagyebbu.newz.co.kr/privacy.html`, 이용약관을
`https://gagyebbu.newz.co.kr/terms.html`로 등록한다. 승인된 도메인은 서브도메인이
아닌 최상위 등록 가능 도메인 `newz.co.kr`을 사용하고 도메인 소유권 확인 후
브랜딩을 게시한다.
