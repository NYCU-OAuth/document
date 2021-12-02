陽明交通大學 OAuth 服務 - 開發者說明文件
====

這裡會介紹如何透過陽明交通大學 OAuth 服務獲取使用者資訊。本平台是依據 OAuth 2.0  (RFC6749) 的標準開發，目前僅開放 `Authorization Code` 方式來認證。


## 開放欄位

- Email
  - 內容：Email, 帳號名稱
  - 所需 scope： `profile`
  - 存取網址： `https://id.nycu.edu.tw/api/profile/`
  - 非機敏資料
- 姓名
  - 內容：使用者姓名, 帳號名稱
  - 所需 scope： `name`
  - 存取網址： `https://id.nycu.edu.tw/api/name/`
  - 機敏資料

- 在學/在職狀態
  - 內容：在學/在職狀態, 帳號名稱
  - 所需 scope： `status`
  - 存取網址： `https://id.nycu.edu.tw/api/status/`
  - 機敏資料

- 宿舍別
  - 內容：在學狀態, 帳號名稱
  - 所需 scope： `dorm_type`
  - 存取網址： `https://id.nycu.edu.tw/api/dorm_type/`
  - 機敏資料

- 宿舍房號
  - 內容：在學狀態, 帳號名稱
  - 所需 scope： `dorm_room_number`
  - 存取網址： `https://id.nycu.edu.tw/api/dorm_room_number/`
  - 機敏資料

## 申請機敏資料存取核可

若需要存取機敏資料，必須在系統內提出申請，通過學校的核可後才有存取權限。

#### 申請方法

在應用程式的管理頁面中，在「申請核可」這個欄位填寫所需的欄位及用途，通過校方的審查後即可使用。

## 認證方法

#### 申請應用程式

1. 連接到平台網頁 `https://id.nycu.edu.tw/`
2. 透過學號/工號登入
3. 在此頁面 `https://id.nycu.edu.tw/apply/new/` 申請一個新的應用程式
   * Client type: public
   * Authorization grant type: authorization-code
   * Redirect uris: 請填寫欲串接平台對應接收 OAuth 參數之網址，此以 `http://example.com/oauth/` 作為範例
4. 在此頁面找到 `https://id.nycu.edu.tw/apply/`，獲取剛剛所申請到的 Client Id, Client Secret, Redirect uris

#### 獲取使用者授權

1. 根據所需scope，讓使用者透過瀏覽器開啟
   - 存取 profile
     `https://id.nycu.edu.tw/o/authorize/?client_id=<Client id>&response_type=code&scope=profile`
   - 存取 name
     `https://id.nycu.edu.tw/o/authorize/?client_id=<Client id>&response_type=code&scope=name`
   - 存取 name + profile
     `https://id.nycu.edu.tw/o/authorize/?client_id=<Client id>&response_type=code&scope=profile%20name`

2. 按下授權
3. 成功授權後，瀏覽器會導向 `http://example.com/oauth/?code=<Code>`
4. 請保留此 `<Code>` 參數與伺服器換取 Token

#### 與 OAuth 伺服器換取 Token

1. 透過 POST 方式 `https://id.nycu.edu.tw/o/token/`， form-data 包含以下資料

   ```
   grant_type: authorization_code
   code: <Code>
   client_id:<Client id>
   client_secret: <Client Secret>
   redirect_uri: http://example.com/oauth/
   ```

   請注意 redirect_uri 必須包含在申請步驟的中所填寫的 Redirect uris 欄位中（可多個），否則會有錯誤。

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

#### 獲取使用者資訊名稱

1. 在 header 中附上 `Authorization: Bearer <Access Token>`
2. 透過 GET 方式存取所需欄位的網址，如
   - 存取 profile `https://id.nycu.edu.tw/api/profile/`
   - 存取 name `https://id.nycu.edu.tw/api/name/`
3. 伺服器回傳資料
