# thread Clone 專案待辦事項

這份待辦清單涵蓋 Thread Clone 社交媒體平台的所有開發階段，從基礎設置到管理員功能。每个任務包含具體工作項目，使用 `[ ]` 標記進度。

## 階段 1：專案設置與資料庫結構

目標：初始化前端和後端專案，設置 Supabase 資料庫並定義核心表結構。

### 迭代 1.1：專案初始化

- [x] 初始化前端 React 專案（使用 Vite）
  - 創建 `frontend/` 目錄，運行 `npm create vite@latest`。
  - 配置 Tailwind CSS（`tailwind.config.js`）。
  - 設置 ESLint 和 Prettier 確保程式碼一致性。
- [x] 初始化後端 Spring Boot 專案
  - 創建 `backend/` 目錄，生成 Maven 專案（`pom.xml`）。
  - 添加 Supabase Java 依賴（`io.github.jan:supabase-java`）。
  - 配置 `application.properties`（Supabase URL 和 Key）。
- [x] 設置 Git 儲存庫
  - 創建 `.gitignore`，忽略 `node_modules` 和構建產物。
  - 初始化 Git，提交初始專案結構。

### 迭代 1.2：資料庫結構

- [x] 設計並創建核心資料庫表
  - 創建 `database/database-schema.sql`：
    - `users` 表：儲存用戶資料（`id`, `email`, `username`, `bio`, `role` 等）。
    - `posts` 表：儲存貼文（`id`, `content`, `image_url`, `user_id`, `is_anonymous` 等）。
    - `replies` 表：儲存回覆（`id`, `content`, `post_id`, `user_id`, `is_anonymous` 等）。
    - `likes` 表：儲存點讚（`post_id`, `user_id`）。
    - `follows` 表：儲存追蹤關係（`follower_id`, `followed_id`）。
  - 啟用 RLS（Row Level Security）並定義政策：
    - `Users view own profile`
    - `Users update own profile`
    - `Users view public posts`
    - `Users manage own posts`
    - `Users manage own replies`
    - `Users manage own likes`
    - `Users manage own follows`
- [x] 設置觸發器
  - 創建 `handle_new_user` 函數，自動為新用戶設置預設 `username` 和 `role`。
- [x] 測試資料庫結構
  - 使用 Supabase 儀表板或 CLI 應用 `database-schema.sql`。
  - 插入測試資料，驗證 RLS 政策。

## 階段 2：用戶認證與貼文

目標：實現用戶認證（登錄/註冊）並支援貼文創建和時間軸顯示。

### 迭代 2.1：用戶認證

- [ ] 配置 Supabase 認證
  - 創建 `frontend/src/lib/supabase.js`，初始化 Supabase 客戶端。
  - 配置環境變數（`.env`）儲存 `SUPABASE_URL` 和 `SUPABASE_ANON_KEY`。
- [ ] 實現登錄和註冊頁面
  - 創建 `frontend/src/pages/Login.jsx` 和 `Login.test.jsx`：
    - 支援電子郵件和密碼登錄。
    - 處理錯誤（例如無效憑證）。
  - 創建 `frontend/src/pages/Signup.jsx` 和 `Signup.test.jsx`：
    - 支援電子郵件、密碼和用戶名註冊。
    - 調用 `handle_new_user` 觸發器。
- [ ] 測試認證流程
  - 單元測試：模擬 Supabase 登錄/註冊 API，驗證錯誤處理。
  - 手動測試：確保登錄後重定向到時間軸，註冊後自動登錄。

### 迭代 2.2：貼文 API

- [ ] 實現貼文創建和刪除 API
  - 創建 `backend/src/main/java/com/threadclone/backend/controller/PostController.java`：
    - `POST /api/posts`：創建貼文。
    - `DELETE /api/posts/:post_id`：軟刪除貼文（設置 `is_deleted = true`）。
  - 創建 `backend/src/main/java/com/threadclone/backend/service/PostService.java`：
    - 處理貼文邏輯，與 Supabase Postgrest 交互。
  - 創建 `backend/src/main/java/com/threadclone/backend/dto/PostRequest.java` 和 `PostResponse.java`。
- [ ] 實現時間軸 API
  - 在 `PostController.java` 中添加 `GET /api/timeline`：
    - 返回追蹤用戶的貼文（分頁）。
  - 更新 `PostService.java` 支援分頁查詢。
- [ ] 測試貼文 API
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/PostControllerTest.java`：
    - 單元測試：驗證創建、刪除和時間軸 API。
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/PostIntegrationTest.java`：
    - 整合測試：驗證資料庫操作和 RLS。

### 迭代 2.3：前端貼文與時間軸

- [ ] 實現時間軸頁面
  - 創建 `frontend/src/components/Timeline.jsx` 和 `Timeline.test.jsx`：
    - 顯示貼文列表（`GET /api/timeline`）。
    - 支援無限滾動（使用 IntersectionObserver）。
  - 創建貼文表單，調用 `POST /api/posts`。
- [ ] 測試時間軸
  - 單元測試：模擬 API 呼叫，驗證貼文渲染和分頁。
  - 手動測試：確保貼文卡片樣式（Tailwind CSS）一致，滾動加載順暢。

## 階段 3：回覆與點讚

目標：實現貼文回覆和點讚功能，增強用戶交互。

### 迭代 3.1：回覆 API

- [ ] 實現回覆 API
  - 更新 `PostController.java`：
    - `POST /api/posts/:post_id/replies`：創建回覆。
  - 更新 `PostService.java`：
    - 插入回覆到 `replies` 表，更新 `posts.replies_count`。
- [ ] 測試回覆 API
  - 更新 `PostControllerTest.java` 和 `PostIntegrationTest.java`：
    - 驗證回覆創建和計數更新。
    - 檢查 RLS（僅用戶可創建回覆）。

### 迭代 3.2：點讚 API

- [ ] 實現點讚 API
  - 更新 `PostController.java`：
    - `POST /api/posts/:post_id/like`：添加點讚。
    - `DELETE /api/posts/:post_id/like`：取消點讚。
  - 更新 `PostService.java`：
    - 管理 `likes` 表，更新 `posts.likes_count`。
- [ ] 測試點讚 API
  - 更新 `PostControllerTest.java` 和 `PostIntegrationTest.java`：
    - 驗證點讚和取消點讚。
    - 檢查 RLS（僅用戶可管理自己的點讚）。

### 迭代 3.3：前端回覆與點讚

- [ ] 更新時間軸組件
  - 更新 `frontend/src/components/Timeline.jsx`：
    - 添加回覆表單（`POST /api/posts/:post_id/replies`）。
    - 添加點讚按鈕（`POST/DELETE /api/posts/:post_id/like`）。
    - 顯示回覆列表和點讚數。
  - 更新 `frontend/src/components/Timeline.test.jsx`：
    - 單元測試：驗證回覆提交和點讚交互。
- [ ] 測試前端交互
  - 手動測試：驗證回覆列表渲染、點讚數更新和 Tailwind CSS 樣式。

## 階段 4：用戶追蹤與個人資料

目標：實現用戶追蹤功能和個人資料頁面。

### 迭代 4.1：追蹤 API

- [ ] 實現追蹤 API
  - 創建 `backend/src/main/java/com/threadclone/backend/controller/FollowController.java`：
    - `POST /api/users/:user_id/follow`：追蹤用戶。
    - `DELETE /api/users/:user_id/follow`：取消追蹤。
  - 創建 `backend/src/main/java/com/threadclone/backend/service/FollowService.java`。
  - 創建 `backend/src/main/java/com/threadclone/backend/entity/Follow.java`。
- [ ] 測試追蹤 API
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/FollowControllerTest.java`：
    - 單元測試：驗證追蹤和取消追蹤。
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/FollowIntegrationTest.java`：
    - 整合測試：驗證 `follows` 表記錄。

### 迭代 4.2：個人資料 API

- [ ] 實現個人資料 API
  - 創建 `backend/src/main/java/com/threadclone/backend/controller/UserController.java`：
    - `GET /api/users/:user_id`：查看用戶資料。
    - `PUT /api/users/me`：更新用戶資料（`username`, `bio`）。
  - 創建 `backend/src/main/java/com/threadclone/backend/service/UserService.java`。
  - 創建 `backend/src/main/java/com/threadclone/backend/dto/UserRequest.java` 和 `UserResponse.java`。
- [ ] 測試個人資料 API
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/UserControllerTest.java`：
    - 單元測試：驗證資料檢索和更新。
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/UserIntegrationTest.java`：
    - 整合測試：驗證 RLS 和資料更新。

### 迭代 4.3：前端個人資料

- [ ] 實現個人資料頁面
  - 創建 `frontend/src/pages/Profile.jsx` 和 `Profile.test.jsx`：
    - 顯示用戶資料（`GET /api/users/:user_id`）。
    - 支援編輯資料（`PUT /api/users/me`）。
    - 支援追蹤/取消追蹤（`POST/DELETE /api/users/:user_id/follow`）。
- [ ] 測試個人資料頁面
  - 單元測試：驗證資料渲染、編輯提交和追蹤交互。
  - 手動測試：驗證頁面樣式（Tailwind CSS）和交互。

## 階段 5：搜索與通知

目標：實現貼文搜索和通知系統。

### 迭代 5.1：搜索 API

- [ ] 實現貼文搜索 API
  - 更新 `PostController.java`：
    - `GET /api/search`：全文搜索貼文（使用 `posts.content_tsv`）。
  - 更新 `PostService.java`：
    - 支援分頁和關鍵字查詢。
- [ ] 測試搜索 API
  - 更新 `PostControllerTest.java` 和 `PostIntegrationTest.java`：
    - 驗證搜索結果和分頁。

### 迭代 5.2：前端搜索

- [ ] 實現搜索頁面
  - 創建 `frontend/src/pages/Search.jsx` 和 `Search.test.jsx`：
    - 支援關鍵字輸入，顯示搜索結果（`GET /api/search`）。
    - 支援分頁。
- [ ] 測試搜索頁面
  - 單元測試：驗證搜索結果渲染。
  - 手動測試：驗證搜索輸入和貼文卡片樣式。

### 迭代 5.3：通知系統

- [ ] 創建通知表和觸發器
  - 創建 `database/notifications.sql`：
    - `notifications` 表：儲存點讚和回覆通知。
    - 觸發器：`handle_new_like` 和 `handle_new_reply`。
    - RLS 政策：`Users view own notifications`。
- [ ] 實現通知 API
  - 創建 `backend/src/main/java/com/threadclone/backend/controller/NotificationController.java`：
    - `GET /api/notifications`：返回用戶通知。
  - 創建 `backend/src/main/java/com/threadclone/backend/service/NotificationService.java`。
  - 創建 `backend/src/main/java/com/threadclone/backend/entity/Notification.java`。
- [ ] 測試通知 API
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/NotificationControllerTest.java`：
    - 單元測試：驗證通知查詢。
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/NotificationIntegrationTest.java`：
    - 整合測試：驗證通知生成和 RLS。

### 迭代 5.4：前端通知

- [ ] 實現通知頁面
  - 創建 `frontend/src/pages/Notifications.jsx` 和 `Notifications.test.jsx`：
    - 顯示通知列表（`GET /api/notifications`）。
    - 支援分頁。
- [ ] 測試通知頁面
  - 單元測試：驗證通知渲染。
  - 手動測試：驗證通知樣式和分頁。

## 階段 6：管理員功能

目標：實現管理員功能，支援查看和刪除所有貼文。

### 迭代 6.1：管理員 API

- [ ] 實現管理員 API
  - 創建 `backend/src/main/java/com/threadclone/backend/controller/AdminController.java`：
    - `GET /api/admin/posts`：查看所有貼文（可篩選已刪除/匿名）。
    - `DELETE /api/admin/posts/:post_id`：刪除貼文。
  - 創建 `backend/src/main/java/com/threadclone/backend/service/AdminService.java`。
- [ ] 測試管理員 API
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/AdminControllerTest.java`：
    - 單元測試：驗證僅管理員可訪問。
  - 創建 `backend/src/test/java/com/threadclone/backend/controller/AdminIntegrationTest.java`：
    - 整合測試：驗證貼文檢索和刪除。

### 迭代 6.2：前端管理員面板

- [ ] 實現管理員面板
  - 創建 `frontend/src/pages/AdminPanel.jsx` 和 `AdminPanel.test.jsx`：
    - 顯示貼文列表（`GET /api/admin/posts`）。
    - 支援篩選已刪除/匿名貼文。
    - 支援刪除貼文（`DELETE /api/admin/posts/:post_id`）。
- [ ] 測試管理員面板
  - 單元測試：驗證角色檢查、貼文渲染和刪除操作。
  - 手動測試：驗證頁面樣式和篩選交互。

## 其他任務

- [ ] 配置路由
  - 更新 `frontend/src/App.jsx`：
    - 定義路由：`/login`, `/signup`, `/timeline`, `/profile/:user_id`, `/search`, `/notifications`, `/admin`。
- [ ] 部署專案
  - 前端：部署到 Vercel 或 Netlify。
  - 後端：部署到 Railway 或 Heroku。
  - 配置 Supabase 環境變數。
- [ ] 撰寫文件
  - 更新 `README.md`：
    - 包含專案概述、設置指南和 API 文件。
  - 提供 API 文件（例如使用 Swagger）。
- [ ] 執行完整測試
  - 運行所有單元測試和整合測試。
  - 手動測試所有功能（認證、貼文、回覆、點讚、追蹤、個人資料、搜索、通知、管理員）。

---

## 說明與使用指南

### 文件結構

- **階段與迭代**：每個階段按功能劃分（例如認證、貼文、管理員），迭代聚焦具體實現（例如 API、前端）。
- **工作項目**：詳細列出每個任務，涵蓋程式碼實現、測試和手動驗證。
- **進度追蹤**：使用 `[ ]`（未完成）或 `[x]`（已完成）標記進度，您可直接編輯 Markdown 文件。
- **檔案參考**：對應前述資料夾結構（例如 `frontend/src/components/Timeline.jsx`），確保與專案一致。

### 如何使用

1. **複製文件**：將上述內容保存為 `todo.markdown`，放置於專案根目錄（`thread-clone/`）。
2. **追蹤進度**：在開發過程中，完成任務後將 `[ ]` 改為 `[x]`，例如：
   ```markdown
   - [x] 初始化前端 React 專案（使用 Vite）
   ```
3. **優先級排序**：按階段順序執行，確保依賴關係（例如資料庫結構先於 API）。
4. **檢查點**：
   - 每個迭代完成後，運行相關測試（單元測試和整合測試）。
   - 階段完成後，進行手動測試，驗證 UI 和功能。

### 與專案整合

- **資料庫**：依賴 `database-schema.sql` 和 `notifications.sql`，確保 RLS 和觸發器正確設置。
- **前端**：所有頁面（`Timeline.jsx`, `Profile.jsx` 等）使用 Tailwind CSS，與 `tailwind.config.js` 一致。
- **後端**：API 實現（`PostController.java` 等）與 Supabase Postgrest 交互，依賴 `application.properties` 配置。
- **測試**：每個功能包含單元測試（例如 `Timeline.test.jsx`）和整合測試（例如 `PostIntegrationTest.java`）。

## 下一步建議

- **擴展功能**：
  - [ ] 添加私訊功能（新表 `messages`，API `POST /api/messages`）。
  - [ ] 實現貼文標籤（新表 `tags`，支援 `GET /api/tags/:tag`）。
- **效能優化**：
  - [ ] 在 `GET /api/timeline` 中引入快取（Redis）。
  - [ ] 優化前端無限滾動，減少 API 呼叫。
- **部署與監控**：
  - [ ] 配置 CI/CD（GitHub Actions），自動運行測試和部署。
  - [ ] 添加日誌（例如使用 Logback）監控後端錯誤。
