---
title: 구글스프레드 시트로 국제화(i18n) 관리하기
---

i18n 관리를 handsontable (자바스크립트 기반 스프레이드시트 라이브러리)를 사용해서 했으나, 구글 스프레드시트로 관리하도록 변경했다.
handsontable 장점 : uuid 생성이 즉시 반영됨, 시트의 구조를 개발자가 제한할 수 있음

# 구글 스프레드 시트
구글 스프레드 시트(google spreadSheet) 
장점 : 동시에 여러 사람이 수정 가능, 변경 사항에 대한 이력과 버전 관리를 따로 하지 않는 장점
변경 시 단점: 시트의 구조를 제한하는 부분이 까다로움, uuid 등 서버에서 생성하는 데이터를 밀어넣어줘야 함, api 호출 수 제한(100초당 500 리퀘스트)

구현 시 id 컬럼은 자동 생성되도록 하고 변경 불가능하게 Read only 처리 등을 할 수 있다.

# Google Sheets API
링크: https://developers.google.com/sheets/api/guides/concepts

googleapis 란 라이브러리를 제공해주긴 하지만 모든 구글 api를 포함하고 있다. 스프레드 시트만 필요하니 따로 호출해서 사용하는게 좋다.


## 사용방법
### 1. Google Access Token 얻기
Google Apis 를 사용하기  위해서 먼저 Google Access Token 을 얻어야 한다. ([참고](https://developers.google.com/sheets/api/guides/authorizing))

방법 1) OAuth 2.0 클라이언트 인증([Google Auth Library ](https://github.com/googleapis/google-auth-library-nodejs)의 OAuth2Client)

방법 2) 서비스 계정 키 사용하여 인증 (JWT, [참고](https://yuda.dev/267))

구글 로그인 진행 후, Sheets API를 호출할 테니, 로그인 시 전달받은 Access Token 을 사용할 수 있도록 방법1 로 진행.

호출을 위한 client_id, client_secret, redirect_uri 값은 '구글 콘솔 → API 및 서비스 → 사용자 인증 정보 → 클라이언트 ID' 에 생성한 뒤 사용해주면 된다.

```javascript
const client = new OAuth2Client(GOOGLE_CLIENT_ID, GOOGLE_CLIENT_SECRET, REDIRECT_URI);
```

### 2. scope 추가

구글 로그인 시 google auth 요청값에 사용하려는 api의 scope를 추가 해준다. ([scopes 는 여기서 확인](https://developers.google.com/identity/protocols/oauth2/scopes))

응답값은 code 로 받아 서버로 전달한 뒤, 서버에서 검증작업을 하자

```javascript
					 <GoogleLogin
                    clientId={GOOGLE_CLIENT_ID}
                    render={ // button code ...
                    buttonText="Sign In with Google"
                    scope="https://www.googleapis.com/auth/spreadsheets" // sheets api 사용을 위한 scope 추가
                    accessType="offline" // refresh token 을 전달받기 위해 offlie 추가
                    responseType="code" // 응답값으로 code만 전달받아 서버에 전달 후 서버에서 refresh token을 얻음
                    prompt="consent"
                    onSuccess={handleLoginSuccess}
                    onFailure={handleLoginFailure}
                    cookiePolicy="single_host_origin"
                />
```

### 3. 토큰을 얻고 검증 진행
code로 token 정보를 얻고, verify id token.
```javascript
const tokenInfo = await client.getToken(googleTokenCode);

      const ticket = await client.verifyIdToken({
        idToken: tokenInfo.tokens.id_token,
        audience: GOOGLE_CLIENT_ID,
      });
```

### 4. 토큰정보를 DB에 저장
refresh_token 을 포함하는 tokenInfo를 그대로 DB 에 저장해줌.

access_token 의 유효기간은 1시간, refresh_token을 이용해 token을 새로가져올 수 있다.

but, 구글의 refresh_token은 반영구적이다. 해당데이터는 필히 DB에 저장해야함.

고민 1) refresh_token 을 사용하지 않고 access_token 을 cookie에 저장하고, 유효기간이 지나면 auth api를 재호출하는 방법을 사용해보려 했으나, auth 재호출 시 현재유저의 정보를 포함시킬 방법이 없음. access token을 다시 얻으려면 권한수락 팝업창을 다시 띄워줘야함.

고민 2) 서비스 계정을 사용하여 config 에 저장해놓고 서비스 계정으로 api를 호출해도 되지만 그렇게 되면 스프레드 시트를 누가 업데이트했는지 알 수 없다. (서비스계정이 수정한 것으로 남음)

결론) refresh_token을 DB에 계정에 같이 넣어놓고, 해당 유저가 sheet api 호출할 때, refresh_token 으로 access_token 을 얻어서 사용하도록 진행함.


### 5. 토큰을 headers 에 넣어서 전달
Sheets API 호출 전, DB에서 얻어온 토큰정보로 토큰을 얻은 다음 headers 에 넣어서 전달해준다.
```javascript
const user = ... // DB에서 정보를 가져옴
      
    client.setCredentials(user.googleTokens);
  }

  // 구글 토큰 만료시간이 1분 미만으로 남았을 경우 새로 갱신
  if (client.credentials.expiry_date - 6000 <= new Date().valueOf()) {
    await client.getAccessToken();
  }
```
### 6. HTTP request 방법
HTTP request 방법은 [여기를 참고](https://developers.google.com/sheets/api/reference/rest/v4/spreadsheets.values)




실서버 저장 / 개발서버 저장 버튼으로 분리해서 저장버튼 클릭 시 반영되도록 구현했다.
현재 상황에선 필요없지만, 나중에는 빌드 시 자동으로 반영되도록 하는 부분도 고려해보면 좋을 듯 하다.



참고문서
- https://developers.google.com/sheets/api
- https://github.com/googleapis/google-auth-library-nodejs
- https://yuda.dev/267
