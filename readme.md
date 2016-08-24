交通大學 OAuth 服務 - 開發者說明文件
====================

這裡會介紹如何透過交通大學 OAuth 服務獲取使用者資訊。本平台是依據 OAuth 2.0  (RFC6749) 的標準開發，目前僅開放 `Authorization Code` 方式來認證。

----------

認證方法
=========

（一）申請應用程式
--
1. 連接到平台網頁 `https://id.nctu.edu.tw/`
2. 透過學號/工號登入
3. 在此頁面 `https://id.nctu.edu.tw/apply/new/` 申請一個新的應用程式
   * Client type: public
   * Authorization grant type: authorization-code
   * Redirect uris: 請填寫欲串接平台對應接收 OAuth 參數之網址，此以 `http://example.com/oauth/` 作為範例
4. 在此頁面找到 `https://id.nctu.edu.tw/apply/`，獲取剛剛所申請到的 Client Id, Client Secret, Redirect uris

（二）獲取使用者授權
--

1. 透過瀏覽器開啟
`https://id.nctu.edu.tw/o/authorize/?client_id=<Client id>&scope=profile&response_type=code`
2. 按下授權
3. 成功授權後，瀏覽器會導向 `http://example.com/oauth/?code=<Code>`
4. 請保留此 `<Code>` 參數與伺服器換取 Token

三: 與 OAuth 伺服器換取 Token
--

1. 透過 POST 方式 `https://id.nctu.edu.tw/o/token/`， form-data 包含以下資料
   ```
   grant_type: authorization_code
   code: <Code>
   client_id:<Client id>
   client_secret: <Client Secret>
   redirect_uri: <URL> 
   ```

2. 伺服器應回傳以下資料
   ```javascript
   {
      "token_type": "Bearer",
      "expires_in": 36000,
      "access_token": <Access Token>,
      "scope": "profile",
      "refresh_token": <Refresh Token>
   }
   ```

四: 獲取使用者資訊名稱
--
1. 透過 GET 方式 `https://id.nctu.edu.tw/api/profile/`
並在 header 中附上 `Authorization: Bearer <Access Token>`
2. 伺服器回傳使用者資訊 (目前提供學號/工號 + D2 EMail)

