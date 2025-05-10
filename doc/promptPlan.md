以下是一個詳細的藍圖，用於以 **Traditional Chinese (Taiwan)** 打造 **Threads Clone** 社交媒體平台。我們將從專案規格出發，制定一個全面的開發計劃，將其分解為迭代的階段和細小的步驟，確保每個步驟可安全實施、易於測試，並逐步推進專案進展。最終，我們將提供一系列針對代碼生成 LLM 的提示（prompts），以測試驅動開發（TDD）方式實現每個步驟，遵循最佳實踐，確保增量進展，並將所有代碼整合為一個完整的應用程式。

---

## 專案藍圖：Threads Clone 開發計劃

### 目標

打造一個類似 Threads 的社交媒體平台，支援貼文、回覆、點讚、追蹤和搜尋功能，使用 Supabase（PostgreSQL）作為資料庫，後端使用 Spring Boot，前端使用 React，並採用 TDD 確保高品質代碼。

### 非功能需求

- **安全性**：使用 Supabase Auth 的 JWT 認證，啟用 RLS（Row-Level Security），確保資料安全。
- **可擴展性**：支援無限滾動、游標分頁和全文搜尋。
- **可維護性**：結構化代碼、OpenAPI 文件、詳細測試覆蓋（單元測試 80%，整合測試 50%）。
- **用戶體驗**：響應式設計，支援深色/淺色模式，簡潔的錯誤訊息。

### 技術棧

- **後端**：Spring Boot（RESTful API）、Supabase Java 客戶端、OpenAPI（Swagger）。
- **前端**：React（Vite）、Tailwind CSS、@supabase/supabase-js。
- **資料庫**：Supabase（PostgreSQL），啟用 RLS 和全文搜尋（tsvector）。
- **測試**：JUnit 5、Mockito、Spring Boot Test、Supabase CLI。

---

## 步驟一：整體開發計劃

### 階段分解

根據規格和開發順序，專案分為五個主要階段，每個階段聚焦於特定功能，並通過迭代逐步完成：

1. **階段 1：資料庫設計與認證基礎**（1-2 週）

   - 目標：建立資料庫結構，配置 Supabase Auth，實現註冊和登入功能。
   - 功能：資料庫表、索引、RLS、用戶註冊/登入 API、前端認證表單。
   - 交付：可運行的資料庫、基本的認證 API 和前端登入頁面。

2. **階段 2：貼文與時間軸**（2-3 週）

   - 目標：實現貼文創建、刪除和時間軸顯示。
   - 功能：貼文 API（創建、刪除）、時間軸 API（公開）、前端貼文表單和時間軸組件。
   - 交付：用戶可創建貼文並查看時間軸。

3. **階段 3：回覆與點讚**（2-3 週）

   - 目標：添加回覆和點讚功能。
   - 功能：回覆 API（創建、刪除）、點讚 API（點讚、取消）、前端回覆表單和點讚按鈕。
   - 交付：用戶可回覆貼文並點讚。

4. **階段 4：追蹤與個人檔案**（2-3 週）

   - 目標：實現追蹤系統和個人檔案頁面。
   - 功能：追蹤 API（追蹤、取消）、個人檔案 API、前端個人檔案頁面和追蹤按鈕。
   - 交付：用戶可追蹤他人並查看個人檔案。

5. **階段 5：搜尋與管理員功能**（2-3 週）

   - 目標：添加全文搜尋和管理員功能。
   - 功能：搜尋 API、管理員 API（查看匿名貼文）、前端搜尋欄和管理員介面。
   - 交付：完整的 Threads Clone 應用程式，包含搜尋和管理功能。

6. **階段 6：整合與優化**（1-2 週）
   - 目標：整合所有功能，優化性能，完成最終測試。
   - 功能：前端後端整合、性能優化（快取、索引）、最終測試。
   - 交付：可部署的應用程式，包含 CI/CD 設置和文件。

### 設計原則

- **增量開發**：每個階段交付可運行的功能，逐步增加複雜度。
- **TDD**：每個功能先寫測試，確保代碼品質。
- **模組化**：後端分層（控制器、服務、儲存庫），前端組件化（React 組件）。
- **整合性**：每個步驟的代碼與前一步驟整合，避免孤立代碼。
- **安全性**：所有 API 端點使用 Supabase JWT 驗證，資料庫使用 RLS。

---

## 步驟二：迭代分解

將每個階段分解為更小的迭代（chunks），每個迭代專注於一個具體功能，包含明確的輸入、輸出和測試需求。以下是初步分解：

### 階段 1：資料庫設計與認證基礎

- **迭代 1.1：資料庫結構與 RLS**
  - 創建表（`users`, `posts`, `replies`, `likes`, `follows`）、索引和觸發器。
  - 配置 RLS 政策，確保公開存取和認證存取。
  - 測試：Supabase CLI 驗證 RLS。
- **迭代 1.2：Supabase Auth 與觸發器**
  - 配置 Supabase Auth（啟用郵件驗證）。
  - 創建觸發器，同步 `auth.users` 到 `users` 表。
  - 測試：驗證用戶註冊和 `users` 表同步。
- **迭代 1.3：後端認證 API**
  - 實現 `POST /api/register` 和 `POST /api/login`。
  - 配置 Supabase Java 客戶端，處理 JWT。
  - 測試：單元測試（控制器、服務）、整合測試（API 與 Supabase）。
- **迭代 1.4：前端認證表單**
  - 創建 React 專案，設置 `@supabase/supabase-js`。
  - 實現註冊和登入表單，處理錯誤訊息。
  - 測試：手動測試表單，驗證 JWT 儲存。

### 階段 2：貼文與時間軸

- **迭代 2.1：貼文 API**
  - 實現 `POST /api/posts`（創建貼文）、`DELETE /api/posts/:post_id`。
  - 配置 RLS，確保只有驗證用戶可創建。
  - 測試：單元測試（貼文邏輯）、整合測試（RLS）。
- **迭代 2.2：時間軸 API**
  - 實現 `GET /api/timeline`（公開，無限滾動）。
  - 配置游標分頁（20 條/頁）。
  - 測試：單元測試（分頁邏輯）、整合測試（公開存取）。
- **迭代 2.3：前端貼文與時間軸**
  - 實現貼文表單和時間軸組件（無限滾動）。
  - 使用 Tailwind CSS 設計卡片式介面。
  - 測試：手動測試表單和滾動行為。

### 階段 3：回覆與點讚

- **迭代 3.1：回覆 API**
  - 實現 `POST /api/posts/:post_id/replies`、`DELETE /api/replies/:reply_id`。
  - 配置 RLS，確保驗證用戶可創建回覆。
  - 測試：單元測試（回覆邏輯）、整合測試（RLS）。
- **迭代 3.2：點讚 API**
  - 實現 `POST /api/posts/:post_id/like`、`DELETE /api/posts/:post_id/like`。
  - 更新 `posts.likes_count`。
  - 測試：單元測試（點讚邏輯）、整合測試（計數）。
- **迭代 3.3：前端回覆與點讚**
  - 實現回覆表單和點讚按鈕。
  - 顯示回覆列表和點讚數。
  - 測試：手動測試回覆和點讚功能。

### 階段 4：追蹤與個人檔案

- **迭代 4.1：追蹤 API**
  - 實現 `POST /api/follow/:user_id`、`DELETE /api/follow/:user_id`。
  - 配置 RLS，防止自我追蹤。
  - 測試：單元測試（追蹤邏輯）、整合測試（RLS）。
- **迭代 4.2：個人檔案 API**
  - 實現 `GET /api/users/:user_id`、`GET /api/users/:user_id/posts`。
  - 計算追蹤者數和追蹤中數。
  - 測試：單元測試（檔案邏輯）、整合測試（公開存取）。
- **迭代 4.3：前端個人檔案**
  - 實現個人檔案頁面和追蹤按鈕。
  - 顯示用戶貼文（無限滾動）。
  - 測試：手動測試檔案和追蹤功能。

### 階段 5：搜尋與管理員功能

- **迭代 5.1：搜尋 API**
  - 實現 `GET /api/search?q=關鍵字`，使用 `tsvector` 排序。
  - 配置公開存取（`is_deleted = FALSE`）。
  - 測試：單元測試（搜尋邏輯）、整合測試（相關性）。
- **迭代 5.2：管理員 API**
  - 實現 `GET /api/admin/posts?anonymous=true`。
  - 配置管理員 RLS。
  - 測試：單元測試（管理員邏輯）、整合測試（權限）。
- **迭代 5.3：前端搜尋與管理員**
  - 實現搜尋欄和管理員介面。
  - 顯示搜尋結果和匿名貼文。
  - 測試：手動測試搜尋和管理功能。

### 階段 6：整合與優化

- **迭代 6.1：前端後端整合**
  - 整合所有 API 端點和前端組件。
  - 確保 JWT 驗證和錯誤處理。
  - 測試：端到端測試（手動）。
- **迭代 6.2：性能優化**
  - 添加快取（Redis 或 Supabase Edge Functions）。
  - 優化索引（搜尋、分頁）。
  - 測試：性能測試（響應時間）。
- **迭代 6.3：最終測試與文件**
  - 執行全套測試（單元、整合、端到端）。
  - 生成 OpenAPI 文件和 README。
  - 測試：驗證覆蓋率（80% 單元，50% 整合）。

---

## 步驟三：細化步驟

每個迭代進一步分解為更小的步驟，確保每個步驟：

- **可實施**：單次開發不超過 1-2 小時。
- **可測試**：包含明確的測試案例。
- **有意義**：推進專案進展，與前一步驟整合。
- **安全**：避免大規模變更，減少錯誤風險。

以下是階段 1 和 2 的細化步驟（其他階段可依相同邏輯分解）：

### 階段 1：資料庫設計與認證基礎

#### 迭代 1.1：資料庫結構與 RLS

- **步驟 1.1.1：創建資料庫表**
  - 創建 `users`, `posts`, `replies`, `likes`, `follows` 表。
  - 配置主鍵、外鍵和約束。
  - 測試：驗證表結構（Supabase CLI）。
- **步驟 1.1.2：添加索引**
  - 創建全文搜尋索引（`content_tsv`）和其他索引。
  - 測試：查詢性能（EXPLAIN）。
- **步驟 1.1.3：實現觸發器**
  - 創建 `detect_english` 和 `update_content_tsv` 函數。
  - 添加觸發器到 `posts` 和 `replies`。
  - 測試：插入英文/非英文內容，驗證 `content_tsv`。
- **步驟 1.1.4：配置 RLS**
  - 啟用 RLS，添加政策（`users`, `posts`, `replies`, `likes`, `follows`）。
  - 測試：模擬 `anon` 和 `authenticated` 角色，驗證存取。

#### 迭代 1.2：Supabase Auth 與觸發器

- **步驟 1.2.1：配置 Supabase Auth**
  - 在 Supabase 儀表板啟用郵件驗證。
  - 設置環境變數（`SUPABASE_URL`, `SUPABASE_KEY`）。
  - 測試：手動註冊用戶，驗證郵件發送。
- **步驟 1.2.2：創建同步觸發器**
  - 實現 `handle_new_user` 函數，同步 `auth.users` 到 `users`。
  - 添加觸發器到 `auth.users`。
  - 測試：註冊用戶，驗證 `users` 表記錄。

#### 迭代 1.3：後端認證 API

- **步驟 1.3.1：設置 Spring Boot 專案**
  - 創建 Spring Boot 專案，添加依賴（`spring-web`, `spring-data-jpa`, `supabase-java`）。
  - 配置應用程式屬性（`application.yml`）。
  - 測試：啟動應用程式，驗證無錯誤。
- **步驟 1.3.2：實現註冊 API**
  - 創建 `POST /api/register` 控制器和服務。
  - 使用 Supabase Java 客戶端註冊用戶。
  - 測試：單元測試（控制器、服務）、整合測試（Supabase）。
- **步驟 1.3.3：實現登入 API**
  - 創建 `POST /api/login` 控制器和服務。
  - 處理 Supabase JWT 返回。
  - 測試：單元測試（控制器、服務）、整合測試（JWT）。

#### 迭代 1.4：前端認證表單

- **步驟 1.4.1：設置 React 專案**
  - 創建 Vite 專案，添加 Tailwind CSS 和 `@supabase/supabase-js`。
  - 配置環境變數（`.env`）。
  - 測試：啟動應用程式，驗證頁面渲染。
- **步驟 1.4.2：實現註冊表單**
  - 創建註冊組件，調用 `supabase.auth.signUp`。
  - 顯示錯誤訊息。
  - 測試：手動測試表單，驗證註冊流程。
- **步驟 1.4.3：實現登入表單**
  - 創建登入組件，調用 `supabase.auth.signInWithPassword`。
  - 儲存 JWT 到 `localStorage`。
  - 測試：手動測試登入，驗證 JWT。

### 階段 2：貼文與時間軸

#### 迭代 2.1：貼文 API

- **步驟 2.1.1：實現貼文創建 API**
  - 創建 `POST /api/posts` 控制器和服務。
  - 驗證內容長度（≤ 500 字）和圖片 URL。
  - 測試：單元測試（驗證邏輯）、整合測試（RLS）。
- **步驟 2.1.2：實現貼文刪除 API**
  - 創建 `DELETE /api/posts/:post_id` 控制器和服務。
  - 實現軟刪除（`is_deleted = TRUE`）。
  - 測試：單元測試（刪除邏輯）、整合測試（權限）。

#### 迭代 2.2：時間軸 API

- **步驟 2.2.1：實現時間軸查詢**
  - 創建 `GET /api/timeline` 控制器和服務。
  - 查詢用戶及其追蹤者的貼文（`is_deleted = FALSE`）。
  - 測試：單元測試（查詢邏輯）、整合測試（公開存取）。
- **步驟 2.2.2：實現游標分頁**
  - 添加 `cursor` 參數，限制每次 20 條。
  - 返回 `next_cursor`。
  - 測試：單元測試（分頁邏輯）、整合測試（連續滾動）。

#### 迭代 2.3：前端貼文與時間軸

- **步驟 2.3.1：實現貼文表單**
  - 創建貼文組件，調用 `POST /api/posts`。
  - 支援匿名選項和圖片 URL。
  - 測試：手動測試表單，驗證創建。
- **步驟 2.3.2：實現時間軸組件**
  - 創建時間軸組件，調用 `GET /api/timeline`。
  - 實現無限滾動（Intersection Observer）。
  - 測試：手動測試滾動和資料加載。

---

## 步驟四：審查與調整

### 審查標準

- **步驟粒度**：每個步驟應在 1-2 小時內完成，避免過於複雜。
- **測試性**：每個步驟包含明確的測試案例（單元、整合或手動）。
- **連續性**：每個步驟與前一步驟整合，無孤立代碼。
- **進展性**：每個步驟推進功能實現，逐步接近完整應用。

### 審查結果

- **階段 1.1（資料庫）**：步驟粒度適中，表、索引、觸發器和 RLS 分開實現，易於測試（Supabase CLI）。
- **階段 1.2（Auth）**：觸發器步驟可能稍複雜，可細分為「創建函數」和「綁定觸發器」。
- **階段 1.3（後端 API）**：註冊和登入 API 分開，粒度合理，測試覆蓋充分。
- **階段 1.4（前端）**：表單實現簡單，但需添加路由（React Router），可作為額外步驟。
- **階段 2（貼文與時間軸）**：步驟清晰，API 和前端分離，無限滾動可能需細分為「基礎列表」和「滾動邏輯」。

### 調整後的細化步驟（階段 1 示例）

#### 迭代 1.2：Supabase Auth 與觸發器

- **步驟 1.2.1：配置 Supabase Auth**
  - 配置郵件驗證，設置環境變數。
  - 測試：手動註冊，驗證郵件。
- **步驟 1.2.2：創建同步函數**
  - 實現 `handle_new_user` 函數。
  - 測試：模擬插入 `auth.users`，驗證函數邏輯。
- **步驟 1.2.3：綁定觸發器**
  - 添加觸發器到 `auth.users`。
  - 測試：註冊用戶，驗證 `users` 表記錄。

#### 迭代 1.4：前端認證表單

- **步驟 1.4.1：設置 React 專案**
  - 創建 Vite 專案，添加依賴。
  - 測試：啟動應用程式。
- **步驟 1.4.2：配置路由**
  - 添加 `react-router-dom`，設置 `/register` 和 `/login` 路由。
  - 測試：驗證路由導航。
- **步驟 1.4.3：實現註冊表單**
  - 創建註冊組件，調用 `supabase.auth.signUp`。
  - 測試：手動測試表單。
- **步驟 1.4.4：實現登入表單**
  - 創建登入組件，儲存 JWT。
  - 測試：手動測試登入。

---

## 步驟五：代碼生成提示（Prompts）

以下是針對階段 1 的代碼生成提示，採用 TDD 方式，確保每個步驟可獨立實現並與前一步驟整合。每個提示包含：

- **背景**：描述步驟目標和上下文。
- **任務**：明確的代碼要求。
- **測試要求**：單元測試、整合測試或手動測試。
- **整合方式**：如何與前一步驟連接。
- **格式**：使用 Markdown 和 `<xaiArtifact>` 標籤。

### Prompt 1：創建資料庫表

#### 背景

這是 Threads Clone 專案的第一步，目標是創建資料庫表（`users`, `posts`, `replies`, `likes`, `follows`），為後續功能奠定基礎。使用 Supabase（PostgreSQL），確保表結構符合規格。

#### 任務

創建 `database-schema.sql`，包含：

- `users` 表（無 `password_hash`，`id` 與 `auth.users.id` 同步）。
- `posts`, `replies`, `likes`, `follows` 表，包含主鍵、外鍵和約束。
- 啟用 UUID 擴展。

#### 測試要求

- 使用 Supabase CLI 執行腳本，驗證表結構。
- 插入測試資料，檢查約束（例如 `username` 長度、`image_url` 格式）。

#### 整合方式

- 這是第一步，無需整合。
- 後續步驟（索引、觸發器）將基於此腳本。

---

### Prompt 2：添加索引

#### 背景

在創建資料庫表後，需添加索引以優化查詢性能，特別是全文搜尋（`content_tsv`）和常見查詢（`user_id`, `post_id`）。

#### 任務

更新 `database-schema.sql`，添加以下索引：

- 全文搜尋索引（`idx_posts_content_tsv`, `idx_replies_content_tsv`）。
- 外鍵索引（`idx_posts_user_id`, `idx_replies_post_id` 等）。
- 刪除篩選索引（`idx_posts_is_deleted`, `idx_replies_is_deleted`）。

#### 測試要求

- 使用 `EXPLAIN` 驗證索引使用。
- 插入測試資料，比較有無索引的查詢性能。

#### 整合方式

- 追加到 `database-schema.sql`，基於 Prompt 1 的表結構。

---

### Prompt 3：實現觸發器

#### 背景

為支援全文搜尋，需為 `posts` 和 `replies` 表添加觸發器，自動生成 `content_tsv`（僅限英文內容）。

#### 任務

更新 `database-schema.sql`，添加：

- `detect_english` 函數，檢查是否為 ASCII 內容。
- `update_content_tsv` 函數，生成 `content_tsv`。
- 觸發器（`posts_tsv_trigger`, `replies_tsv_trigger`）。

#### 測試要求

- 插入英文內容，驗證 `content_tsv` 生成。
- 插入非英文內容（例如中文），驗證 `content_tsv` 為 NULL。

#### 整合方式

- 追加到 `database-schema.sql`，基於 Prompt 2。

---

### Prompt 4：配置 RLS

#### 背景

為確保資料安全，需為所有表啟用 RLS，並添加政策，支援公開存取（`anon`）、認證存取（`authenticated`）和管理員權限。

#### 任務

更新 `database-schema.sql`，添加：

- 啟用 RLS（`ALTER TABLE ... ENABLE ROW LEVEL SECURITY`）。
- RLS 政策（`users`, `posts`, `replies`, `likes`, `follows`），如規格所述。

#### 測試要求

- 使用 Supabase CLI 模擬角色：
  - `anon`：驗證僅能查看未刪除貼文/回覆。
  - `authenticated`：驗證可創建貼文（需 `is_verified`）。
  - `admin`：驗證可刪除任何貼文。

#### 整合方式

- 追加到 `database-schema.sql`，基於 Prompt 3。

```sql
-- Threads Clone 資料庫結構
-- 為 Supabase (PostgreSQL) 設計，包含表、索引、觸發器和 RLS
-- 使用 UUID，與 Supabase Auth 同步

-- 啟用 UUID 擴展
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- 語言偵測函數：檢查是否為英文（僅 ASCII 字符）
CREATE OR REPLACE FUNCTION detect_english(content TEXT) RETURNS BOOLEAN AS $$
BEGIN
  RETURN content ~ '^[[:ascii:]]*$';
END;
$$ LANGUAGE plpgsql;

-- 更新 content_tsv 的觸發器函數
CREATE OR REPLACE FUNCTION update_content_tsv() RETURNS TRIGGER AS $$
BEGIN
  IF detect_english(NEW.content) THEN
    NEW.content_tsv := to_tsvector('simple', NEW.content);
  ELSE
    NEW.content_tsv := NULL;
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- 創建 users 表
CREATE TABLE users (
  id UUID PRIMARY KEY,
  email TEXT UNIQUE NOT NULL,
  username TEXT UNIQUE NOT NULL CHECK (LENGTH(username) >= 3 AND LENGTH(username) <= 50),
  bio TEXT CHECK (LENGTH(bio) <= 160),
  is_verified BOOLEAN DEFAULT FALSE,
  role TEXT DEFAULT 'user' CHECK (role IN ('user', 'admin')),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- 創建 posts 表
CREATE TABLE posts (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  content TEXT NOT NULL CHECK (LENGTH(content) <= 500),
  content_tsv TSVECTOR,
  image_url TEXT CHECK (LENGTH(image_url) <= 255 AND image_url ~* '\.(jpg|png|gif)$'),
  user_id UUID REFERENCES users(id) ON DELETE SET NULL,
  is_anonymous BOOLEAN DEFAULT FALSE,
  is_deleted BOOLEAN DEFAULT FALSE,
  likes_count INT DEFAULT 0 CHECK (likes_count >= 0),
  replies_count INT DEFAULT 0 CHECK (replies_count >= 0),
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- 創建 posts 表觸發器
CREATE TRIGGER posts_tsv_trigger
  BEFORE INSERT OR UPDATE OF content
  ON posts
  FOR EACH ROW
  EXECUTE FUNCTION update_content_tsv();

-- 創建 replies 表
CREATE TABLE replies (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  post_id UUID REFERENCES posts(id) ON DELETE CASCADE,
  content TEXT NOT NULL CHECK (LENGTH(content) <= 200),
  content_tsv TSVECTOR,
  image_url TEXT CHECK (LENGTH(image_url) <= 255 AND image_url ~* '\.(jpg|png|gif)$'),
  user_id UUID REFERENCES users(id) ON DELETE SET NULL,
  is_anonymous BOOLEAN DEFAULT FALSE,
  is_deleted BOOLEAN DEFAULT FALSE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- 創建 replies 表觸發器
CREATE TRIGGER replies_tsv_trigger
  BEFORE INSERT OR UPDATE OF content
  ON replies
  FOR EACH ROW
  EXECUTE FUNCTION update_content_tsv();

-- 創建 likes 表
CREATE TABLE likes (
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  post_id UUID REFERENCES posts(id) ON DELETE CASCADE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  PRIMARY KEY (user_id, post_id)
);

-- 創建 follows 表
CREATE TABLE follows (
  follower_id UUID REFERENCES users(id) ON DELETE CASCADE,
  followed_id UUID REFERENCES users(id) ON DELETE CASCADE,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW(),
  PRIMARY KEY (follower_id, followed_id)
);

-- 創建索引
CREATE INDEX idx_posts_content_tsv ON posts USING GIN(content_tsv) WHERE content_tsv IS NOT NULL;
CREATE INDEX idx_replies_content_tsv ON replies USING GIN(content_tsv) WHERE content_tsv IS NOT NULL;
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_posts_is_deleted ON posts(is_deleted);
CREATE INDEX idx_replies_post_id ON replies(post_id, created_at);
CREATE INDEX idx_replies_is_deleted ON replies(is_deleted);
CREATE INDEX idx_likes_post_id ON likes(post_id);
CREATE INDEX idx_follows_follower_id ON follows(follower_id);
CREATE INDEX idx_follows_followed_id ON follows(followed_id);

-- 啟用 RLS
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE posts ENABLE ROW LEVEL SECURITY;
ALTER TABLE replies ENABLE ROW LEVEL SECURITY;
ALTER TABLE likes ENABLE ROW LEVEL SECURITY;
ALTER TABLE follows ENABLE ROW LEVEL SECURITY;

-- users 表 RLS 政策
CREATE POLICY "Users view own profile" ON users
FOR SELECT TO authenticated
USING (id = auth.uid());

CREATE POLICY "Users update own profile" ON users
FOR UPDATE TO authenticated
USING (id = auth.uid())
WITH CHECK (
  LENGTH(username) >= 3 AND LENGTH(username) <= 50 AND
  LENGTH(bio) <= 160
);

CREATE POLICY "Admins view all users" ON users
FOR SELECT TO authenticated
USING ((SELECT role FROM users WHERE id = auth.uid()) = 'admin');

-- posts 表 RLS 政策
CREATE POLICY "Public view non-deleted posts" ON posts
FOR SELECT TO public
USING (is_deleted = FALSE);

CREATE POLICY "Users create posts" ON posts
FOR INSERT TO authenticated
WITH CHECK (
  auth.uid() IS NOT NULL AND
  (SELECT is_verified FROM users WHERE id = auth.uid()) = TRUE
);

CREATE POLICY "Users delete own posts" ON posts
FOR UPDATE TO authenticated
USING (user_id = auth.uid())
WITH CHECK (is_deleted IS NOT NULL);

CREATE POLICY "Admins delete all posts" ON posts
FOR UPDATE TO authenticated
USING ((SELECT role FROM users WHERE id = auth.uid()) = 'admin')
WITH CHECK (is_deleted IS NOT NULL);

CREATE POLICY "Admins view all posts" ON posts
FOR SELECT TO authenticated
USING ((SELECT role FROM users WHERE id = auth.uid()) = 'admin');

-- replies 表 RLS 政策
CREATE POLICY "Public view non-deleted replies" ON replies
FOR SELECT TO public
USING (is_deleted = FALSE);

CREATE POLICY "Users create replies" ON replies
FOR INSERT TO authenticated
WITH CHECK (
  auth.uid() IS NOT NULL AND
  (SELECT is_verified FROM users WHERE id = auth.uid()) = TRUE
);

CREATE POLICY "Users delete own replies" ON replies
FOR UPDATE TO authenticated
USING (user_id = auth.uid())
WITH CHECK (is_deleted IS NOT NULL);

CREATE POLICY "Admins delete all replies" ON replies
FOR UPDATE TO authenticated
USING ((SELECT role FROM users WHERE id = auth.uid()) = 'admin')
WITH CHECK (is_deleted IS NOT NULL);

CREATE POLICY "Admins view all replies" ON replies
FOR SELECT TO authenticated
USING ((SELECT role FROM users WHERE id = auth.uid()) = 'admin');

-- likes 表 RLS 政策
CREATE POLICY "Users manage own likes" ON likes
FOR ALL TO authenticated
USING (user_id = auth.uid());

-- follows 表 RLS 政策
CREATE POLICY "Users manage own follows" ON follows
FOR ALL TO authenticated
USING (follower_id = auth.uid())
WITH CHECK (follower_id != followed_id);
```

---

### Prompt 5：配置 Supabase Auth

#### 背景

在資料庫結構完成後，需配置 Supabase Auth 以支援用戶註冊和郵件驗證。

#### 任務

創建 `supabase-config.md`，記錄 Supabase Auth 配置：

- 啟用郵件驗證。
- 設置環境變數（`SUPABASE_URL`, `SUPABASE_KEY`）。
- 提供測試腳本（SQL），模擬註冊。

#### 測試要求

- 手動註冊用戶，驗證郵件發送。
- 執行測試腳本，檢查 `auth.users` 記錄。

#### 整合方式

- 獨立文件，供後續步驟參考。
- 環境變數用於後端和前端。

````
# Supabase Auth 配置

## 目標
配置 Supabase Auth，啟用郵件驗證，支援 Threads Clone 的用戶註冊和登入。

## 步驟
1. **啟用郵件驗證**：
   - 登入 Supabase 儀表板，進入「Authentication」設置。
   - 啟用「Email」提供者，設置驗證郵件模板（預設模板即可）。
   - 設置郵件有效期為 24 小時。
2. **設置環境變數**：
   - 在 Supabase 儀表板獲取專案 URL 和 Anon Key。
   - 創建 `.env` 文件：
     ```env
     SUPABASE_URL=https://<project>.supabase.co
     SUPABASE_KEY=<anon_key>
     ```
3. **測試腳本**：
   - 使用 Supabase CLI 或 SQL 客戶端執行以下腳本，模擬註冊：
     ```sql
     -- 模擬註冊
     INSERT INTO auth.users (email, password_hash, email_verified)
     VALUES ('test@example.com', 'hashed_password', FALSE);
     -- 檢查記錄
     SELECT * FROM auth.users WHERE email = 'test@example.com';
     ```

## 測試
- 手動註冊用戶（透過 Supabase API 或後續前端）。
- 驗證收到郵件，點擊連結後 `auth.users.email_verified` 為 TRUE。

````

---

### Prompt 6：創建同步觸發器

#### 背景

為同步 `auth.users` 和 `users` 表，需創建觸發器，當新用戶註冊時自動插入 `users` 表。

#### 任務

更新 `database-schema.sql`，添加：

- `handle_new_user` 函數，插入 `users` 表。
- 觸發器，綁定到 `auth.users` 的插入事件。

#### 測試要求

- 模擬插入 `auth.users`，驗證 `users` 表記錄。
- 檢查 `username`（預設值）、`is_verified` 等欄位。

#### 整合方式

- 追加到 `database-schema.sql`，基於 Prompt 4。

```sql

-- 使用 UUID，與 Supabase Auth 同步

-- 同步 auth.users 到 users 表
CREATE OR REPLACE FUNCTION public.handle_new_user()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO public.users (id, email, username, is_verified, role, created_at)
  VALUES (
    NEW.id,
    NEW.email,
    'user_' || NEW.id::text,
    NEW.email_verified,
    'user',
    NOW()
  );
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_auth_user_created
  AFTER INSERT ON auth.users
  FOR EACH ROW EXECUTE FUNCTION public.handle_new_user();
```

---

### Prompt 7：設置 Spring Boot 專案

#### 背景

開始後端開發，設置 Spring Boot 專案，準備實現認證 API。

#### 任務

創建 Spring Boot 專案，包含：

- `pom.xml`：添加依賴（`spring-web`, `spring-data-jpa`, `supabase-java`）。
- `application.yml`：配置 Supabase 連線。
- `Application.java`：主應用程式類。

#### 測試要求

- 執行 `mvn spring-boot:run`，驗證應用程式啟動。
- 檢查日誌，確保無錯誤。

#### 整合方式

- 獨立專案，為後續 API 提供基礎。
- 使用 Prompt 5 的環境變數。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.2.0</version>
        <relativePath/>
    </parent>
    <groupId>com.threadsclone</groupId>
    <artifactId>backend</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>Threads Clone Backend</name>
    <description>Backend for Threads Clone social media platform</description>

    <properties>
        <java.version>17</java.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-data-jpa</artifactId>
        </dependency>
        <dependency>
            <groupId>io.github.jan-supabase</groupId>
            <artifactId>supabase-java</artifactId>
            <version>0.2.0</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.junit.jupiter</groupId>
            <artifactId>junit-jupiter</artifactId>
            <scope>test</scope>
        </dependency>
        <dependency>
            <groupId>org.mockito</groupId>
            <artifactId>mockito-core</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>

```

```yaml
spring:
  application:
    name: threads-clone-backend
  datasource:
    url: ${SUPABASE_URL}/postgres
    username: postgres
    password: ${SUPABASE_KEY}
supabase:
  url: ${SUPABASE_URL}
  key: ${SUPABASE_KEY}
server:
  port: 8080
```

```java
package com.threadclone.backend;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

---

### Prompt 8：實現註冊 API

#### 背景

在 Spring Boot 專案中實現 `POST /api/register`，使用 Supabase Auth 註冊用戶，並同步到 `users` 表。

#### 任務

創建以下文件：

- `AuthController.java`：處理 `POST /api/register`。
- `AuthService.java`：實現註冊邏輯。
- `AuthControllerTest.java`：單元測試。
- `AuthIntegrationTest.java`：整合測試。

#### 測試要求

- 單元測試：模擬 Supabase 客戶端，驗證註冊邏輯。
- 整合測試：連線 Supabase，驗證 API 和 `users` 表同步。

#### 整合方式

- 基於 Prompt 7 的 Spring Boot 專案。
- 使用 Prompt 5 的環境變數和 Prompt 6 的觸發器。

```java
package com.threadclone.backend.controller;

package com.threadclone.backend;

import com.threadclone.backend.dto.RegisterRequest;
import com.threadclone.backend.dto.RegisterResponse;
import com.threadclone.backend.service.AuthService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class AuthController {
    private final AuthService authService;

    public AuthController(AuthService authService) {
        this.authService = authService;
    }

    @PostMapping("/register")
    public ResponseEntity<RegisterResponse> register(@RequestBody RegisterRequest request) {
        authService.register(request);
        return ResponseEntity.ok(new RegisterResponse("註冊成功，請驗證郵件"));
    }
}
```

```java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.RegisterRequest;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.gotrue.Gotrue;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class AuthService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public AuthService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public void register(RegisterRequest request) {
        GoTrue auth = supabaseClient.getGoTrue();
        auth.signUp(request.getEmail(), request.getPassword());
        // 觸發器將自動同步到 users 表
    }
}
```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.RegisterRequest;
import com.threadclone.backend.dto.RegisterResponse;
import com.threadclone.backend.service.AuthService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.doNothing;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(AuthController.class)
public class AuthControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private AuthService authService;

    @Test
    void shouldRegisterSuccessfully() throws Exception {
        RegisterRequest request = new RegisterRequest("test@example.com", "password123", "testuser");
        doNothing().when(authService).register(request);

        mockMvc.perform(post("/api/register")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"email\":\"test@example.com\",\"password\":\"password123\",\"username\":\"testuser\"}"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.message").value("註冊成功，請驗證郵件"));
    }
}
```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.RegisterRequest;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class AuthIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldRegisterSuccessfully() {
        RegisterRequest request = new RegisterRequest("test@example.com", "password123", "testuser");
        ResponseEntity<String> response = restTemplate.postForEntity(
                "/api/register",
                request,
                String.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
        assertEquals("{\"message\":\"註冊成功，請驗證郵件\"}", response.getBody());
    }
}
```

### 階段 2：貼文與時間軸

目標：實現貼文創建、刪除和時間軸顯示功能，包含後端 API 和前端組件，支援無限滾動。

#### 迭代 2.1：貼文 API

##### Prompt 9：實現貼文創建 API

###### 背景

在階段 1 完成資料庫和認證基礎後，需實現 `POST /api/posts` API，允許驗證用戶創建貼文，內容儲存到 `posts` 表，遵守 RLS（僅 `is_verified = TRUE` 的用戶可創建）。

###### 任務

創建以下文件：

- `PostController.java`：處理 `POST /api/posts`。
- `PostService.java`：實現貼文創建邏輯，使用 Supabase Java 客戶端。
- `Post.java`：貼文實體類（JPA）。
- `PostControllerTest.java`：單元測試。
- `PostIntegrationTest.java`：整合測試。

###### 測試要求

- **單元測試**：模擬 Supabase 客戶端，驗證內容長度（≤ 500 字）、圖片 URL 格式（jpg/png/gif）。
- **整合測試**：連線 Supabase，驗證貼文創建、RLS（未驗證用戶被阻止）、匿名選項。

###### 整合方式

- 基於 Prompt 7 的 Spring Boot 專案，添加 `PostController` 和 `PostService`。
- 使用 `database-schema.sql` 的 `posts` 表和 RLS。

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.PostService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/api")
public class PostController {
    private final PostService postService;

    public PostController(PostService postService) {
        this.postService = postService;
    }

    @PostMapping("/posts")
    public ResponseEntity<PostResponse> createPost(@RequestBody PostRequest request) {
        PostResponse response = postService.createPost(request);
        return ResponseEntity.ok(response);
    }
}
```

```java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class PostService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public PostService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public PostResponse createPost(PostRequest request) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var result = postgrest.from("posts")
                .insert(new PostRequestWrapper(
                        request.getContent(),
                        request.getImageUrl(),
                        request.isAnonymous()
                ))
                .select()
                .single()
                .execute();
        return new PostResponse(
                result.getString("id"),
                result.getString("content"),
                result.getString("image_url"),
                result.getString("user_id"),
                result.getBoolean("is_anonymous"),
                result.getInt("likes_count"),
                result.getInt("replies_count"),
                result.getString("created_at")
        );
    }

    private static class PostRequestWrapper {
        private String content;
        private String image_url;
        private boolean is_anonymous;
        private String user_id;

        public PostRequestWrapper(String content, String imageUrl, boolean isAnonymous) {
            this.content = content;
            this.image_url = imageUrl;
            this.is_anonymous = isAnonymous;
            this.user_id = SupabaseClient.getAuth().getCurrentUser().getId();
        }
    }
}

```

```java
package com.threadclone.backend.entity;

import jakarta.persistence.*;
import java.time.OffsetDateTime;
import java.util.UUID;

@Entity
@Table(name = "posts")
public class Post {
    @Id
    @GeneratedValue
    private UUID id;

    @Column(nullable = false, length = 500)
    private String content;

    @Column(name = "image_url", length = 255)
    private String imageUrl;

    @Column(name = "user_id")
    private UUID userId;

    @Column(name = "is_anonymous")
    private boolean isAnonymous;

    @Column(name = "is_deleted")
    private boolean isDeleted;

    @Column(name = "likes_count")
    private int likesCount;

    @Column(name = "replies_count")
    private int repliesCount;

    @Column(name = "created_at")
    private OffsetDateTime createdAt;

    // Getters and setters
}
```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.PostService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(PostController.class)
public class PostControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private PostService postService;

    @Test
    void shouldCreatePostSuccessfully() throws Exception {
        PostRequest request = new PostRequest("Hello world", null, false);
        PostResponse response = new PostResponse("uuid", "Hello world", null, "user_id", false, 0, 0, "2025-05-10T12:00:00Z");
        when(postService.createPost(request)).thenReturn(response);

        mockMvc.perform(post("/api/posts")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"content\":\"Hello world\",\"imageUrl\":null,\"isAnonymous\":false}"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.content").value("Hello world"));
    }
}
```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class PostIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldCreatePostSuccessfully() {
        PostRequest request = new PostRequest("Hello world", null, false);
        ResponseEntity<PostResponse> response = restTemplate.postForEntity(
                "/api/posts",
                request,
                PostResponse.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
        assertEquals("Hello world", response.getBody().getContent());
    }
}
```

##### Prompt 10：實現貼文刪除 API

###### 背景

實現 `DELETE /api/posts/:post_id` API，允許用戶軟刪除自己的貼文（設置 `is_deleted = TRUE`），遵守 RLS。

###### 任務

更新以下文件：

- `PostController.java`：添加 `DELETE /api/posts/:post_id`。
- `PostService.java`：實現刪除邏輯。
- `PostControllerTest.java`：添加單元測試。
- `PostIntegrationTest.java`：添加整合測試。

###### 測試要求

- **單元測試**：驗證僅貼文擁有者可刪除，模擬 RLS。
- **整合測試**：驗證軟刪除（`is_deleted = TRUE`），非擁有者和未驗證用戶被阻止。

###### 整合方式

- 擴展 Prompt 9 的 `PostController` 和 `PostService`。
- 依賴 `posts` 表的 RLS（`Users delete own posts`）。

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.PostService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
public class PostController {
    private final PostService postService;

    public PostController(PostService postService) {
        this.postService = postService;
    }

    @PostMapping("/posts")
    public ResponseEntity<PostResponse> createPost(@RequestBody PostRequest request) {
        PostResponse response = postService.createPost(request);
        return ResponseEntity.ok(response);
    }

    @DeleteMapping("/posts/{postId}")
    public ResponseEntity<Void> deletePost(@PathVariable String postId) {
        postService.deletePost(postId);
        return ResponseEntity.noContent().build();
    }
}
```

```java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class PostService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public PostService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public PostResponse createPost(PostRequest request) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var result = postgrest.from("posts")
                .insert(new PostRequestWrapper(
                        request.getContent(),
                        request.getImageUrl(),
                        request.isAnonymous()
                ))
                .select()
                .single()
                .execute();
        return new PostResponse(
                result.getString("id"),
                result.getString("content"),
                result.getString("image_url"),
                result.getString("user_id"),
                result.getBoolean("is_anonymous"),
                result.getInt("likes_count"),
                result.getInt("replies_count"),
                result.getString("created_at")
        );
    }

    public void deletePost(String postId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        postgrest.from("posts")
                .update(new DeletePostWrapper())
                .eq("id", postId)
                .execute();
    }

    private static class PostRequestWrapper {
        private String content;
        private String image_url;
        private boolean is_anonymous;
        private String user_id;

        public PostRequestWrapper(String content, String imageUrl, boolean isAnonymous) {
            this.content = content;
            this.image_url = imageUrl;
            this.is_anonymous = isAnonymous;
            this.user_id = SupabaseClient.getAuth().getCurrentUser().getId();
        }
    }

    private static class DeletePostWrapper {
        private boolean is_deleted = true;
    }
}

```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.PostService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.doNothing;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(PostController.class)
public class PostControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private PostService postService;

    @Test
    void shouldCreatePostSuccessfully() throws Exception {
        PostRequest request = new PostRequest("Hello world", null, false);
        PostResponse response = new PostResponse("uuid", "Hello world", null, "user_id", false, 0, 0, "2025-05-10T12:00:00Z");
        when(postService.createPost(request)).thenReturn(response);

        mockMvc.perform(post("/api/posts")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"content\":\"Hello world\",\"imageUrl\":null,\"isAnonymous\":false}"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.content").value("Hello world"));
    }

    @Test
    void shouldDeletePostSuccessfully() throws Exception {
        String postId = "uuid";
        doNothing().when(postService).deletePost(postId);

        mockMvc.perform(delete("/api/posts/" + postId))
                .andExpect(status().isNoContent());
    }
}
```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class PostIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldCreatePostSuccessfully() {
        PostRequest request = new PostRequest("Hello world", null, false);
        ResponseEntity<PostResponse> response = restTemplate.postForEntity(
                "/api/posts",
                request,
                PostResponse.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
        assertEquals("Hello world", response.getBody().getContent());
    }

    @Test
    void shouldDeletePostSuccessfully() {
        String postId = "uuid"; // 假設已創建的貼文 ID
        ResponseEntity<Void> response = restTemplate.exchange(
                "/api/posts/" + postId,
                HttpMethod.DELETE,
                null,
                Void.class
        );
        assertEquals(HttpStatus.NO_CONTENT, response.getStatusCode());
    }
}
```

#### 迭代 2.2：時間軸 API

##### Prompt 11：實現時間軸查詢 API

###### 背景

實現 `GET /api/timeline` API，返回用戶及其追蹤者的貼文（`is_deleted = FALSE`），支援游標分頁（每次 20 條）。

###### 任務

更新以下文件：

- `PostController.java`：添加 `GET /api/timeline`。
- `PostService.java`：實現時間軸查詢邏輯。
- `PostControllerTest.java`：添加單元測試。
- `PostIntegrationTest.java`：添加整合測試。

###### 測試要求

- **單元測試**：驗證分頁邏輯（20 條/頁），模擬追蹤者貼文。
- **整合測試**：驗證返回未刪除貼文，檢查游標分頁。

###### 整合方式

- 擴展 Prompt 10 的 `PostController` 和 `PostService`。
- 依賴 `posts` 和 `follows` 表的 RLS。

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.PostService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api")
public class PostController {
    private final PostService postService;

    public PostController(PostService postService) {
        this.postService = postService;
    }

    @PostMapping("/posts")
    public ResponseEntity<PostResponse> createPost(@RequestBody PostRequest request) {
        PostResponse response = postService.createPost(request);
        return ResponseEntity.ok(response);
    }

    @DeleteMapping("/posts/{postId}")
    public ResponseEntity<Void> deletePost(@PathVariable String postId) {
        postService.deletePost(postId);
        return ResponseEntity.noContent().build();
    }

    @GetMapping("/timeline")
    public ResponseEntity<List<PostResponse>> getTimeline(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(required = false) String cursor
    ) {
        List<PostResponse> posts = postService.getTimeline(page, size, cursor);
        return ResponseEntity.ok(posts);
    }
}

```

```java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

@Service
public class PostService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public PostService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public PostResponse createPost(PostRequest request) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var result = postgrest.from("posts")
                .insert(new PostRequestWrapper(
                        request.getContent(),
                        request.getImageUrl(),
                        request.isAnonymous()
                ))
                .select()
                .single()
                .execute();
        return new PostResponse(
                result.getString("id"),
                result.getString("content"),
                result.getString("image_url"),
                result.getString("user_id"),
                result.getBoolean("is_anonymous"),
                result.getInt("likes_count"),
                result.getInt("replies_count"),
                result.getString("created_at")
        );
    }

    public void deletePost(String postId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        postgrest.from("posts")
                .update(new DeletePostWrapper())
                .eq("id", postId)
                .execute();
    }

    public List<PostResponse> getTimeline(int page, int size, String cursor) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var query = postgrest.from("posts")
                .select("*")
                .eq("is_deleted", false)
                .in("user_id", getFollowedUserIds())
                .order("created_at", "desc")
                .limit(size);
        if (cursor != null) {
            query.gt("created_at", cursor);
        }
        var results = query.execute();
        return results.getData().stream()
                .map(result -> new PostResponse(
                        result.getString("id"),
                        result.getString("content"),
                        result.getString("image_url"),
                        result.getString("user_id"),
                        result.getBoolean("is_anonymous"),
                        result.getInt("likes_count"),
                        result.getInt("replies_count"),
                        result.getString("created_at")
                ))
                .collect(Collectors.toList());
    }

    private List<String> getFollowedUserIds() {
        Postgrest postgrest = supabaseClient.getPostgrest();
        String currentUserId = SupabaseClient.getAuth().getCurrentUser().getId();
        var results = postgrest.from("follows")
                .select("followed_id")
                .eq("follower_id", currentUserId)
                .execute();
        return results.getData().stream()
                .map(result -> result.getString("followed_id"))
                .collect(Collectors.toList());
    }

    private static class PostRequestWrapper {
        private String content;
        private String image_url;
        private boolean is_anonymous;
        private String user_id;

        public PostRequestWrapper(String content, String imageUrl, boolean isAnonymous) {
            this.content = content;
            this.image_url = imageUrl;
            this.is_anonymous = isAnonymous;
            this.user_id = SupabaseClient.getAuth().getCurrentUser().getId();
        }
    }

    private static class DeletePostWrapper {
        private boolean is_deleted = true;
    }
}

```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.PostService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import java.util.List;

import static org.mockito.Mockito.doNothing;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(PostController.class)
public class PostControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private PostService postService;

    @Test
    void shouldCreatePostSuccessfully() throws Exception {
        PostRequest request = new PostRequest("Hello world", null, false);
        PostResponse response = new PostResponse("uuid", "Hello world", null, "user_id", false, 0, 0, "2025-05-10T12:00:00Z");
        when(postService.createPost(request)).thenReturn(response);

        mockMvc.perform(post("/api/posts")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"content\":\"Hello world\",\"imageUrl\":null,\"isAnonymous\":false}"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.content").value("Hello world"));
    }

    @Test
    void shouldDeletePostSuccessfully() throws Exception {
        String postId = "uuid";
        doNothing().when(postService).deletePost(postId);

        mockMvc.perform(delete("/api/posts/" + postId))
                .andExpect(status().isNoContent());
    }

    @Test
    void shouldGetTimelineSuccessfully() throws Exception {
        List<PostResponse> posts = List.of(
                new PostResponse("uuid1", "Post 1", null, "user_id", false, 0, 0, "2025-05-10T12:00:00Z")
        );
        when(postService.getTimeline(0, 20, null)).thenReturn(posts);

        mockMvc.perform(get("/api/timeline?page=0&size=20"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$[0].content").value("Post 1"));
    }
}
```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class PostIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldCreatePostSuccessfully() {
        PostRequest request = new PostRequest("Hello world", null, false);
        ResponseEntity<PostResponse> response = restTemplate.postForEntity(
                "/api/posts",
                request,
                PostResponse.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
        assertEquals("Hello world", response.getBody().getContent());
    }

    @Test
    void shouldDeletePostSuccessfully() {
        String postId = "uuid"; // 假設已創建的貼文 ID
        ResponseEntity<Void> response = restTemplate.exchange(
                "/api/posts/" + postId,
                HttpMethod.DELETE,
                null,
                Void.class
        );
        assertEquals(HttpStatus.NO_CONTENT, response.getStatusCode());
    }

    @Test
    void shouldGetTimelineSuccessfully() {
        ResponseEntity<PostResponse[]> response = restTemplate.getForEntity(
                "/api/timeline?page=0&size=20",
                PostResponse[].class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
    }
}

```

#### 迭代 2.3：前端貼文與時間軸

##### Prompt 12：實現貼文表單

###### 背景

在前端實現貼文表單，調用 `POST /api/posts`，支援內容輸入、圖片 URL 和匿名選項，使用 Tailwind CSS 設計。

###### 任務

創建以下文件：

- `src/components/PostForm.jsx`：貼文表單組件。
- `src/components/PostForm.test.jsx`：單元測試。

###### 測試要求

- **單元測試**：驗證表單提交，模擬 API 呼叫，檢查錯誤處理（例如內容過長）。
- **手動測試**：驗證表單樣式和提交功能。

###### 整合方式

- 基於階段 1 的 React 專案（Prompt 8），添加新組件。
- 調用 `POST /api/posts`（Prompt 9）。

```jsx
import { useState } from "react";
import { supabase } from "../lib/supabase";

export default function PostForm() {
  const [content, setContent] = useState("");
  const [imageUrl, setImageUrl] = useState("");
  const [isAnonymous, setIsAnonymous] = useState(false);
  const [error, setError] = useState(null);

  const handleSubmit = async (e) => {
    e.preventDefault();
    setError(null);
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch("/api/posts", {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${data.session.access_token}`,
        },
        body: JSON.stringify({ content, imageUrl, isAnonymous }),
      });
      if (!response.ok) throw new Error("創建貼文失敗");
      setContent("");
      setImageUrl("");
      setIsAnonymous(false);
    } catch (err) {
      setError(err.message);
    }
  };

  return (
    <form
      onSubmit={handleSubmit}
      className="p-4 bg-white dark:bg-gray-800 rounded-lg shadow-md"
    >
      <textarea
        value={content}
        onChange={(e) => setContent(e.target.value)}
        placeholder="分享你的想法..."
        className="w-full p-2 mb-2 border rounded-md dark:bg-gray-700 dark:text-white"
        maxLength={500}
      />
      <input
        type="text"
        value={imageUrl}
        onChange={(e) => setImageUrl(e.target.value)}
        placeholder="圖片 URL（可選）"
        className="w-full p-2 mb-2 border rounded-md dark:bg-gray-700 dark:text-white"
      />
      <label className="flex items-center mb-2">
        <input
          type="checkbox"
          checked={isAnonymous}
          onChange={(e) => setIsAnonymous(e.target.checked)}
          className="mr-2"
        />
        匿名發布
      </label>
      {error && <p className="text-red-500">{error}</p>}
      <button
        type="submit"
        className="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600"
      >
        發布
      </button>
    </form>
  );
}
```

```jsx
import { render, screen, fireEvent } from "@testing-library/react";
import PostForm from "./PostForm";
import { supabase } from "../lib/supabase";

jest.mock("../lib/supabase", () => ({
  supabase: {
    auth: {
      getSession: jest.fn(),
    },
  },
}));

describe("PostForm", () => {
  beforeEach(() => {
    supabase.auth.getSession.mockResolvedValue({
      data: { session: { access_token: "mock_token" } },
      error: null,
    });
    global.fetch = jest.fn(() =>
      Promise.resolve({
        ok: true,
        json: () => Promise.resolve({}),
      })
    );
  });

  test("should submit post successfully", async () => {
    render(<PostForm />);
    fireEvent.change(screen.getByPlaceholderText("分享你的想法..."), {
      target: { value: "Test post" },
    });
    fireEvent.click(screen.getByText("發布"));
    expect(global.fetch).toHaveBeenCalledWith(
      "/api/posts",
      expect.objectContaining({
        method: "POST",
        headers: expect.any(Object),
        body: JSON.stringify({
          content: "Test post",
          imageUrl: "",
          isAnonymous: false,
        }),
      })
    );
  });
});
```

##### Prompt 13：實現時間軸組件

###### 背景

實現時間軸組件，顯示 `GET /api/timeline` 返回的貼文，支援無限滾動，使用 Intersection Observer。

###### 任務

創建以下文件：

- `src/components/Timeline.jsx`：時間軸組件。
- `src/components/Timeline.test.jsx`：單元測試。

###### 測試要求

- **單元測試**：驗證貼文渲染，模擬無限滾動。
- **手動測試**：驗證滾動加載和卡片樣式。

###### 整合方式

- 基於 Prompt 12 的 React 專案，添加新組件。
- 調用 `GET /api/timeline`（Prompt 11）。

```jsx
import { useState, useEffect, useRef } from "react";
import { supabase } from "../lib/supabase";

export default function Timeline() {
  const [posts, setPosts] = useState([]);
  const [page, setPage] = useState(0);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);
  const observerRef = useRef();

  const loadPosts = async () => {
    if (loading || !hasMore) return;
    setLoading(true);
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/timeline?page=${page}&size=20`, {
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (!response.ok) throw new Error("加載貼文失敗");
      const newPosts = await response.json();
      setPosts((prev) => [...prev, ...newPosts]);
      setPage(page + 1);
      setHasMore(newPosts.length === 20);
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    loadPosts();
  }, []);

  useEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting && hasMore && !loading) {
          loadPosts();
        }
      },
      { threshold: 1.0 }
    );
    if (observerRef.current) observer.observe(observerRef.current);
    return () => observer.unobserve(observerRef.current);
  }, [hasMore, loading]);

  return (
    <div className="max-w-2xl mx-auto p-4">
      {posts.map((post) => (
        <div
          key={post.id}
          className="p-4 mb-4 bg-white dark:bg-gray-800 rounded-lg shadow-md"
        >
          <p className="text-gray-800 dark:text-gray-200">{post.content}</p>
          {post.image_url && (
            <img
              src={post.image_url}
              alt="Post"
              className="mt-2 rounded-md max-w-full"
            />
          )}
          <p className="text-sm text-gray-500 dark:text-gray-400">
            {post.is_anonymous ? "匿名" : post.user_id} ·{" "}
            {new Date(post.created_at).toLocaleString()}
          </p>
        </div>
      ))}
      <div ref={observerRef} className="h-10" />
      {loading && <p className="text-center">加載中...</p>}
    </div>
  );
}
```

```jsx
import { render, screen } from "@testing-library/react";
import Timeline from "./Timeline";
import { supabase } from "../lib/supabase";

jest.mock("../lib/supabase", () => ({
  supabase: {
    auth: {
      getSession: jest.fn(),
    },
  },
}));

describe("Timeline", () => {
  beforeEach(() => {
    supabase.auth.getSession.mockResolvedValue({
      data: { session: { access_token: "mock_token" } },
      error: null,
    });
    global.fetch = jest.fn(() =>
      Promise.resolve({
        ok: true,
        json: () =>
          Promise.resolve([
            {
              id: "1",
              content: "Test post",
              user_id: "user1",
              created_at: "2025-05-10T12:00:00Z",
            },
          ]),
      })
    );
  });

  test("should render posts", async () => {
    render(<Timeline />);
    expect(await screen.findByText("Test post")).toBeInTheDocument();
  });
});
```

### 階段 3：回覆與點讚

目標：實現回覆和點讚功能，包含 API 和前端交互。

#### 迭代 3.1：回覆 API

##### Prompt 14：實現回覆創建 API

###### 背景

實現 `POST /api/posts/:post_id/replies`，允許驗證用戶創建回覆，儲存到 `replies` 表，遵守 RLS。

###### 任務

創建以下文件：

- `ReplyController.java`：處理 `POST /api/posts/:post_id/replies`。
- `ReplyService.java`：實現回覆創建邏輯。
- `Reply.java`：回覆實體類。
- `ReplyControllerTest.java`：單元測試。
- `ReplyIntegrationTest.java`：整合測試。

###### 測試要求

- **單元測試**：驗證內容長度（≤ 200 字）、圖片 URL 格式。
- **整合測試**：驗證回覆創建、RLS（未驗證用戶被阻止）、`replies_count` 更新。

###### 整合方式

- 擴展 Prompt 11 的 Spring Boot 專案，添加新控制器和服務。
- 依賴 `replies` 表和 RLS。

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.ReplyRequest;
import com.threadclone.backend.dto.ReplyResponse;
import com.threadclone.backend.service.ReplyService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/posts")
public class ReplyController {
    private final ReplyService replyService;

    public ReplyController(ReplyService replyService) {
        this.replyService = replyService;
    }

    @PostMapping("/{postId}/replies")
    public ResponseEntity<ReplyResponse> createReply(
            @PathVariable String postId,
            @RequestBody ReplyRequest request
    ) {
        ReplyResponse response = replyService.createReply(postId, request);
        return ResponseEntity.ok(response);
    }
}
```

```java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.ReplyRequest;
import com.threadclone.backend.dto.ReplyResponse;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class ReplyService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public ReplyService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public ReplyResponse createReply(String postId, ReplyRequest request) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var result = postgrest.from("replies")
                .insert(new ReplyRequestWrapper(
                        postId,
                        request.getContent(),
                        request.getImageUrl(),
                        request.isAnonymous()
                ))
                .select()
                .single()
                .execute();
        // 更新 posts.replies_count
        postgrest.from("posts")
                .update(new UpdateRepliesCountWrapper())
                .eq("id", postId)
                .execute();
        return new ReplyResponse(
                result.getString("id"),
                result.getString("post_id"),
                result.getString("content"),
                result.getString("image_url"),
                result.getString("user_id"),
                result.getBoolean("is_anonymous"),
                result.getString("created_at")
        );
    }

    private static class ReplyRequestWrapper {
        private String post_id;
        private String content;
        private String image_url;
        private boolean is_anonymous;
        private String user_id;

        public ReplyRequestWrapper(String postId, String content, String imageUrl, boolean isAnonymous) {
            this.post_id = postId;
            this.content = content;
            this.image_url = imageUrl;
            this.is_anonymous = isAnonymous;
            this.user_id = SupabaseClient.getAuth().getCurrentUser().getId();
        }
    }

    private static class UpdateRepliesCountWrapper {
        private int replies_count;

        public UpdateRepliesCountWrapper() {
            this.replies_count = postgrest.from("replies")
                    .select("count")
                    .eq("post_id", post_id)
                    .execute()
                    .getData()
                    .getInt("count");
        }
    }
}
```

```java
package com.threadclone.backend.entity;

import jakarta.persistence.*;
import java.time.OffsetDateTime;
import java.util.UUID;

@Entity
@Table(name = "replies")
public class Reply {
    @Id
    @GeneratedValue
    private UUID id;

    @Column(name = "post_id")
    private UUID postId;

    @Column(nullable = false, length = 200)
    private String content;

    @Column(name = "image_url", length = 255)
    private String imageUrl;

    @Column(name = "user_id")
    private UUID userId;

    @Column(name = "is_anonymous")
    private boolean isAnonymous;

    @Column(name = "is_deleted")
    private boolean isDeleted;

    @Column(name = "created_at")
    private OffsetDateTime createdAt;

    // Getters and setters
}
```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.ReplyRequest;
import com.threadclone.backend.dto.ReplyResponse;
import com.threadclone.backend.service.ReplyService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(ReplyController.class)
public class ReplyControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private ReplyService replyService;

    @Test
    void shouldCreateReplySuccessfully() throws Exception {
        String postId = "post_uuid";
        ReplyRequest request = new ReplyRequest("Nice post!", null, false);
        ReplyResponse response = new ReplyResponse("uuid", postId, "Nice post!", null, "user_id", false, "2025-05-10T12:00:00Z");
        when(replyService.createReply(postId, request)).thenReturn(response);

        mockMvc.perform(post("/api/posts/" + postId + "/replies")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"content\":\"Nice post!\",\"imageUrl\":null,\"isAnonymous\":false}"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.content").value("Nice post!"));
    }
}

```

```java

package com.threadclone.backend.controller;

import com.threadclone.backend.dto.ReplyRequest;
import com.threadclone.backend.dto.ReplyResponse;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class ReplyIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldCreateReplySuccessfully() {
        String postId = "post_uuid"; // 假設已創建的貼文 ID
        ReplyRequest request = new ReplyRequest("Nice post!", null, false);
        ResponseEntity<ReplyResponse> response = restTemplate.postForEntity(
                "/api/posts/" + postId + "/replies",
                request,
                ReplyResponse.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
        assertEquals("Nice post!", response.getBody().getContent());
    }
}

```

##### Prompt 15：實現回覆刪除 API

###### 背景

實現 `DELETE /api/replies/:reply_id`，允許用戶軟刪除自己的回覆，更新 `posts.replies_count`。

###### 任務

更新以下文件：

- `ReplyController.java`：添加 `DELETE /api/replies/:reply_id`。
- `ReplyService.java`：實現刪除邏輯。
- `ReplyControllerTest.java`：添加單元測試。
- `ReplyIntegrationTest.java`：添加整合測試。

###### 測試要求

- **單元測試**：驗證僅回覆擁有者可刪除，模擬 RLS。
- **整合測試**：驗證軟刪除（`is_deleted = TRUE`），檢查 `replies_count`。

###### 整合方式

- 擴展 Prompt 14 的 `ReplyController` 和 `ReplyService`。
- 依賴 `replies` 和 `posts` 表的 RLS。

```java

package com.threadclone.backend.controller;

import com.threadclone.backend.dto.ReplyRequest;
import com.threadclone.backend.dto.ReplyResponse;
import com.threadclone.backend.service.ReplyService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api")
public class ReplyController {
    private final ReplyService replyService;

    public ReplyController(ReplyService replyService) {
        this.replyService = replyService;
    }

    @PostMapping("/posts/{postId}/replies")
    public ResponseEntity<ReplyResponse> createReply(
            @PathVariable String postId,
            @RequestBody ReplyRequest request
    ) {
        ReplyResponse response = replyService.createReply(postId, request);
        return ResponseEntity.ok(response);
    }

    @DeleteMapping("/replies/{replyId}")
    public ResponseEntity<Void> deleteReply(@PathVariable String replyId) {
        replyService.deleteReply(replyId);
        return ResponseEntity.noContent().build();
    }
}
```

```java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.ReplyRequest;
import com.threadclone.backend.dto.ReplyResponse;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class ReplyService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public ReplyService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public ReplyResponse createReply(String postId, ReplyRequest request) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var result = postgrest.from("replies")
                .insert(new ReplyRequestWrapper(
                        postId,
                        request.getContent(),
                        request.getImageUrl(),
                        request.isAnonymous()
                ))
                .select()
                .single()
                .execute();
        // 更新 posts.replies_count
        postgrest.from("posts")
                .update(new UpdateRepliesCountWrapper(postId))
                .eq("id", postId)
                .execute();
        return new ReplyResponse(
                result.getString("id"),
                result.getString("post_id"),
                result.getString("content"),
                result.getString("image_url"),
                result.getString("user_id"),
                result.getBoolean("is_anonymous"),
                result.getString("created_at")
        );
    }

    public void deleteReply(String replyId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var reply = postgrest.from("replies")
                .select("post_id")
                .eq("id", replyId)
                .single()
                .execute();
        String postId = reply.getString("post_id");
        postgrest.from("replies")
                .update(new DeleteReplyWrapper())
                .eq("id", replyId)
                .execute();
        // 更新 posts.replies_count
        postgrest.from("posts")
                .update(new UpdateRepliesCountWrapper(postId))
                .eq("id", postId)
                .execute();
    }

    private static class ReplyRequestWrapper {
        private String post_id;
        private String content;
        private String image_url;
        private boolean is_anonymous;
        private String user_id;

        public ReplyRequestWrapper(String postId, String content, String imageUrl, boolean isAnonymous) {
            this.post_id = postId;
            this.content = content;
            this.image_url = imageUrl;
            this.is_anonymous = isAnonymous;
            this.user_id = SupabaseClient.getAuth().getCurrentUser().getId();
        }
    }

    private static class DeleteReplyWrapper {
        private boolean is_deleted = true;
    }

    private static class UpdateRepliesCountWrapper {
        private int replies_count;

        public UpdateRepliesCountWrapper(String postId) {
            this.replies_count = postgrest.from("replies")
                    .select("count")
                    .eq("post_id", postId)
                    .eq("is_deleted", false)
                    .execute()
                    .getData()
                    .getInt("count");
        }
    }
}
```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.ReplyRequest;
import com.threadclone.backend.dto.ReplyResponse;
import com.threadclone.backend.service.ReplyService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.doNothing;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(ReplyController.class)
public class ReplyControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private ReplyService replyService;

    @Test
    void shouldCreateReplySuccessfully() throws Exception {
        String postId = "post_uuid";
        ReplyRequest request = new ReplyRequest("Nice post!", null, false);
        ReplyResponse response = new ReplyResponse("uuid", postId, "Nice post!", null, "user_id", false, "2025-05-10T12:00:00Z");
        when(replyService.createReply(postId, request)).thenReturn(response);

        mockMvc.perform(post("/api/posts/" + postId + "/replies")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"content\":\"Nice post!\",\"imageUrl\":null,\"isAnonymous\":false}"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.content").value("Nice post!"));
    }

    @Test
    void shouldDeleteReplySuccessfully() throws Exception {
        String replyId = "uuid";
        doNothing().when(replyService).deleteReply(replyId);

        mockMvc.perform(delete("/api/replies/" + replyId))
                .andExpect(status().isNoContent());
    }
}
```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.ReplyRequest;
import com.threadclone.backend.dto.ReplyResponse;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class ReplyIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldCreateReplySuccessfully() {
        String postId = "post_uuid"; // 假設已創建的貼文 ID
        ReplyRequest request = new ReplyRequest("Nice post!", null, false);
        ResponseEntity<ReplyResponse> response = restTemplate.postForEntity(
                "/api/posts/" + postId + "/replies",
                request,
                ReplyResponse.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
        assertEquals("Nice post!", response.getBody().getContent());
    }

    @Test
    void shouldDeleteReplySuccessfully() {
        String replyId = "uuid"; // 假設已創建的回覆 ID
        ResponseEntity<Void> response = restTemplate.exchange(
                "/api/replies/" + replyId,
                HttpMethod.DELETE,
                null,
                Void.class
        );
        assertEquals(HttpStatus.NO_CONTENT, response.getStatusCode());
    }
}
```

#### 迭代 3.2：點讚 API

##### Prompt 16：實現點讚 API

###### 背景

實現 `POST /api/posts/:post_id/like` 和 `DELETE /api/posts/:post_id/like`，管理用戶點讚，更新 `posts.likes_count`。

###### 任務

創建以下文件：

- `LikeController.java`：處理點讚 API。
- `LikeService.java`：實現點讚邏輯。
- `Like.java`：點讚實體類。
- `LikeControllerTest.java`：單元測試。
- `LikeIntegrationTest.java`：整合測試。

###### 測試要求

- **單元測試**：驗證點讚/取消邏輯，模擬 RLS。
- **整合測試**：驗證 `likes` 表記錄和 `posts.likes_count` 更新。

###### 整合方式

- 擴展 Prompt 15 的 Spring Boot 專案，添加新控制器和服務。
- 依賴 `likes` 和 `posts` 表的 RLS。

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.service.LikeService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/posts")
public class LikeController {
    private final LikeService likeService;

    public LikeController(LikeService likeService) {
        this.likeService = likeService;
    }

    @PostMapping("/{postId}/like")
    public ResponseEntity<Void> likePost(@PathVariable String postId) {
        likeService.likePost(postId);
        return ResponseEntity.ok().build();
    }

    @DeleteMapping("/{postId}/like")
    public ResponseEntity<Void> unlikePost(@PathVariable String postId) {
        likeService.unlikePost(postId);
        return ResponseEntity.ok().build();
    }
}

```

```java
package com.threadclone.backend.service;

import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class LikeService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public LikeService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public void likePost(String postId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        postgrest.from("likes")
                .insert(new LikeRequestWrapper(postId))
                .execute();
        // 更新 posts.likes_count
        postgrest.from("posts")
                .update(new UpdateLikesCountWrapper(postId))
                .eq("id", postId)
                .execute();
    }

    public void unlikePost(String postId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        String userId = SupabaseClient.getAuth().getCurrentUser().getId();
        postgrest.from("likes")
                .delete()
                .eq("post_id", postId)
                .eq("user_id", userId)
                .execute();
        // 更新 posts.likes_count
        postgrest.from("posts")
                .update(new UpdateLikesCountWrapper(postId))
                .eq("id", postId)
                .execute();
    }

    private static class LikeRequestWrapper {
        private String post_id;
        private String user_id;

        public LikeRequestWrapper(String postId) {
            this.post_id = postId;
            this.user_id = SupabaseClient.getAuth().getCurrentUser().getId();
        }
    }

    private static class UpdateLikesCountWrapper {
        private int likes_count;

        public UpdateLikesCountWrapper(String postId) {
            this.likes_count = postgrest.from("likes")
                    .select("count")
                    .eq("post_id", postId)
                    .execute()
                    .getData()
                    .getInt("count");
        }
    }
}
```

```java
package com.threadclone.backend.entity;

import jakarta.persistence.*;
import java.time.OffsetDateTime;
import java.util.UUID;

@Entity
@Table(name = "likes")
public class Like {
    @Id
    @Column(name = "user_id")
    private UUID userId;

    @Id
    @Column(name = "post_id")
    private UUID postId;

    @Column(name = "created_at")
    private OffsetDateTime createdAt;

    // Getters and setters
}

```

```java
package com.threadclone.backend.controller;

import com.threadclone.backend.service.LikeService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.doNothing;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(LikeController.class)
public class LikeControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private LikeService likeService;

    @Test
    void shouldLikePostSuccessfully() throws Exception {
        String postId = "uuid";
        doNothing().when(likeService).likePost(postId);

        mockMvc.perform(post("/api/posts/" + postId + "/like"))
                .andExpect(status().isOk());
    }

    @Test
    void shouldUnlikePostSuccessfully() throws Exception {
        String postId = "uuid";
        doNothing().when(likeService).unlikePost(postId);

        mockMvc.perform(delete("/api/posts/" + postId + "/like"))
                .andExpect(status().isOk());
    }
}
```

````java
```java
package com.threadclone.backend.controller;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class LikeIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldLikePostSuccessfully() {
        String postId = "uuid"; // 假設已創建的貼文 ID
        ResponseEntity<Void> response = restTemplate.postForEntity(
                "/api/posts/" + postId + "/like",
                null,
                Void.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
    }

    @Test
    void shouldUnlikePostSuccessfully() {
        String postId = "uuid"; // 假設已點讚的貼文 ID
        ResponseEntity<Void> response = restTemplate.exchange(
                "/api/posts/" + postId + "/like",
                HttpMethod.DELETE,
                null,
                Void.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
    }
}
````

#### 迭代 3.3：前端回覆與點讚

感謝您的要求！以下將從 **Prompt 17：實現回覆表單和點讚按鈕** 開始，繼續提供 Threads Clone 社交媒體平台的藍圖，涵蓋剩餘部分的代碼生成提示（Prompt 17 及後續），直到完成所有階段（階段 3 至階段 6）。所有代碼將使用標準 Markdown 格式（```）包裝，不使用 `<xaiArtifact>`標籤。回應將使用 **繁體中文（台灣）**，遵循您指定的結構化、詳細且非冗餘的風格，並基於已更新的`database-schema.sql`（包含 `handle_new_user` 函數和修正後的 RLS 政策）。每個提示將包含背景、任務、測試要求和整合方式，確保與前述步驟整合，並遵循 **TDD（測試驅動開發）** 方法。

---

## 步驟五：代碼生成提示（續）

### 階段 3：回覆與點讚（續）

#### 迭代 3.3：前端回覆與點讚

##### Prompt 17：實現回覆表單和點讚按鈕

###### 背景

在前端實現回覆表單（調用 `POST /api/posts/:post_id/replies`）和點讚按鈕（調用 `POST/DELETE /api/posts/:post_id/like`），顯示回覆列表和點讚數。該功能增強時間軸組件，提供用戶與貼文的交互。

###### 任務

更新以下文件：

- `src/components/Timeline.jsx`：添加回覆表單、回覆列表和點讚按鈕。
- `src/components/Timeline.test.jsx`：添加單元測試。

###### 測試要求

- **單元測試**：驗證回覆提交和點讚交互，模擬 API 呼叫，檢查錯誤處理（例如內容過長、未登入）。
- **手動測試**：驗證回覆列表渲染、點讚數更新和樣式（使用 Tailwind CSS）。

###### 整合方式

- 基於階段 2 的 `Timeline.jsx`（Prompt 13），擴展功能。
- 調用 `POST /api/posts/:post_id/replies`（Prompt 14）、`POST/DELETE /api/posts/:post_id/like`（Prompt 16）。
- 依賴 `posts`、`replies` 和 `likes` 表的 RLS。

```jsx
// src/components/Timeline.jsx
import { useState, useEffect, useRef } from "react";
import { supabase } from "../lib/supabase";

export default function Timeline() {
  const [posts, setPosts] = useState([]);
  const [page, setPage] = useState(0);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);
  const observerRef = useRef();

  const loadPosts = async () => {
    if (loading || !hasMore) return;
    setLoading(true);
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/timeline?page=${page}&size=20`, {
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (!response.ok) throw new Error("加載貼文失敗");
      const newPosts = await response.json();
      setPosts((prev) => [...prev, ...newPosts]);
      setPage(page + 1);
      setHasMore(newPosts.length === 20);
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  const handleReply = async (postId, content, isAnonymous) => {
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/posts/${postId}/replies`, {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${data.session.access_token}`,
        },
        body: JSON.stringify({ content, imageUrl: null, isAnonymous }),
      });
      if (!response.ok) throw new Error("回覆失敗");
      loadPosts(); // 重新加載貼文
    } catch (err) {
      console.error(err);
    }
  };

  const handleLike = async (postId, isLiked) => {
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/posts/${postId}/like`, {
        method: isLiked ? "DELETE" : "POST",
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (!response.ok) throw new Error("點讚失敗");
      loadPosts(); // 重新加載貼文
    } catch (err) {
      console.error(err);
    }
  };

  useEffect(() => {
    loadPosts();
  }, []);

  useEffect(() => {
    const observer = new IntersectionObserver(
      (entries) => {
        if (entries[0].isIntersecting && hasMore && !loading) {
          loadPosts();
        }
      },
      { threshold: 1.0 }
    );
    if (observerRef.current) observer.observe(observerRef.current);
    return () => observer.unobserve(observerRef.current);
  }, [hasMore, loading]);

  return (
    <div className="max-w-2xl mx-auto p-4">
      {posts.map((post) => (
        <div
          key={post.id}
          className="p-4 mb-4 bg-white dark:bg-gray-800 rounded-lg shadow-md"
        >
          <p className="text-gray-800 dark:text-gray-200">{post.content}</p>
          {post.image_url && (
            <img
              src={post.image_url}
              alt="Post"
              className="mt-2 rounded-md max-w-full"
            />
          )}
          <p className="text-sm text-gray-500 dark:text-gray-400">
            {post.is_anonymous ? "匿名" : post.user_id} ·{" "}
            {new Date(post.created_at).toLocaleString()}
          </p>
          <div className="flex items-center mt-2">
            <button
              onClick={() => handleLike(post.id, post.is_liked)}
              className={`mr-4 ${
                post.is_liked ? "text-red-500" : "text-gray-500"
              }`}
            >
              {post.is_liked ? "取消讚" : "讚"} ({post.likes_count})
            </button>
            <span>{post.replies_count} 回覆</span>
          </div>
          <div className="mt-2">
            {post.replies?.map((reply) => (
              <div
                key={reply.id}
                className="pl-4 border-l-2 border-gray-300 dark:border-gray-600"
              >
                <p className="text-gray-800 dark:text-gray-200">
                  {reply.content}
                </p>
                <p className="text-sm text-gray-500 dark:text-gray-400">
                  {reply.is_anonymous ? "匿名" : reply.user_id} ·{" "}
                  {new Date(reply.created_at).toLocaleString()}
                </p>
              </div>
            ))}
            <form
              onSubmit={(e) => {
                e.preventDefault();
                const content = e.target.content.value;
                const isAnonymous = e.target.isAnonymous.checked;
                handleReply(post.id, content, isAnonymous);
                e.target.reset();
              }}
              className="mt-2"
            >
              <textarea
                name="content"
                placeholder="回覆..."
                className="w-full p-2 border rounded-md dark:bg-gray-700 dark:text-white"
                maxLength={200}
              />
              <label className="flex items-center mb-2">
                <input type="checkbox" name="isAnonymous" className="mr-2" />
                匿名回覆
              </label>
              <button
                type="submit"
                className="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600"
              >
                回覆
              </button>
            </form>
          </div>
        </div>
      ))}
      <div ref={observerRef} className="h-10" />
      {loading && <p className="text-center">加載中...</p>}
    </div>
  );
}
```

```jsx
// src/components/Timeline.test.jsx
import { render, screen, fireEvent } from "@testing-library/react";
import Timeline from "./Timeline";
import { supabase } from "../lib/supabase";

jest.mock("../lib/supabase", () => ({
  supabase: {
    auth: {
      getSession: jest.fn(),
    },
  },
}));

describe("Timeline", () => {
  beforeEach(() => {
    supabase.auth.getSession.mockResolvedValue({
      data: { session: { access_token: "mock_token" } },
      error: null,
    });
    global.fetch = jest.fn();
  });

  test("should render posts", async () => {
    global.fetch.mockResolvedValueOnce({
      ok: true,
      json: () =>
        Promise.resolve([
          {
            id: "1",
            content: "Test post",
            user_id: "user1",
            created_at: "2025-05-10T12:00:00Z",
            likes_count: 0,
            replies_count: 0,
            is_liked: false,
          },
        ]),
    });
    render(<Timeline />);
    expect(await screen.findByText("Test post")).toBeInTheDocument();
  });

  test("should submit reply", async () => {
    global.fetch
      .mockResolvedValueOnce({
        ok: true,
        json: () =>
          Promise.resolve([
            {
              id: "1",
              content: "Test post",
              user_id: "user1",
              created_at: "2025-05-10T12:00:00Z",
              likes_count: 0,
              replies_count: 0,
              is_liked: false,
            },
          ]),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve({}),
      });
    render(<Timeline />);
    const textarea = await screen.findByPlaceholderText("回覆...");
    fireEvent.change(textarea, { target: { value: "Test reply" } });
    fireEvent.click(screen.getByText("回覆"));
    expect(global.fetch).toHaveBeenCalledWith(
      "/api/posts/1/replies",
      expect.objectContaining({
        method: "POST",
        headers: expect.any(Object),
        body: JSON.stringify({
          content: "Test reply",
          imageUrl: null,
          isAnonymous: false,
        }),
      })
    );
  });

  test("should toggle like", async () => {
    global.fetch
      .mockResolvedValueOnce({
        ok: true,
        json: () =>
          Promise.resolve([
            {
              id: "1",
              content: "Test post",
              user_id: "user1",
              created_at: "2025-05-10T12:00:00Z",
              likes_count: 0,
              replies_count: 0,
              is_liked: false,
            },
          ]),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve({}),
      });
    render(<Timeline />);
    const likeButton = await screen.findByText("讚 (0)");
    fireEvent.click(likeButton);
    expect(global.fetch).toHaveBeenCalledWith(
      "/api/posts/1/like",
      expect.objectContaining({
        method: "POST",
        headers: expect.any(Object),
      })
    );
  });
});
```

### 階段 4：用戶追蹤與個人資料

目標：實現用戶追蹤功能和個人資料頁面，包含 API 和前端組件。

#### 迭代 4.1：追蹤 API

##### Prompt 18：實現追蹤 API

###### 背景

實現 `POST /api/users/:user_id/follow` 和 `DELETE /api/users/:user_id/follow`，管理用戶追蹤關係，儲存到 `follows` 表，遵守 RLS。

###### 任務

創建以下文件：

- `FollowController.java`：處理追蹤 API。
- `FollowService.java`：實現追蹤邏輯。
- `Follow.java`：追蹤實體類。
- `FollowControllerTest.java`：單元測試。
- `FollowIntegrationTest.java`：整合測試。

###### 測試要求

- **單元測試**：驗證追蹤/取消邏輯，模擬 RLS。
- **整合測試**：驗證 `follows` 表記錄，檢查重複追蹤錯誤。

###### 整合方式

- 擴展階段 3 的 Spring Boot 專案，添加新控制器和服務。
- 依賴 `follows` 表的 RLS（`Users manage own follows`）。

```java
// src/main/java/com/threadsclone/backend/controller/FollowController.java
package com.threadclone.backend.controller;

import com.threadclone.backend.service.FollowService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
public class FollowController {
    private final FollowService followService;

    public FollowController(FollowService followService) {
        this.followService = followService;
    }

    @PostMapping("/{userId}/follow")
    public ResponseEntity<Void> followUser(@PathVariable String userId) {
        followService.followUser(userId);
        return ResponseEntity.ok().build();
    }

    @DeleteMapping("/{userId}/follow")
    public ResponseEntity<Void> unfollowUser(@PathVariable String userId) {
        followService.unfollowUser(userId);
        return ResponseEntity.ok().build();
    }
}
```

```java
// src/main/java/com/threadsclone/backend/service/FollowService.java
package com.threadclone.backend.service;

import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class FollowService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public FollowService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public void followUser(String userId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        postgrest.from("follows")
                .insert(new FollowRequestWrapper(userId))
                .execute();
    }

    public void unfollowUser(String userId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        String currentUserId = SupabaseClient.getAuth().getCurrentUser().getId();
        postgrest.from("follows")
                .delete()
                .eq("follower_id", currentUserId)
                .eq("followed_id", userId)
                .execute();
    }

    private static class FollowRequestWrapper {
        private String follower_id;
        private String followed_id;

        public FollowRequestWrapper(String followedId) {
            this.follower_id = SupabaseClient.getAuth().getCurrentUser().getId();
            this.followed_id = followedId;
        }
    }
}
```

```java
// src/main/java/com/threadsclone/backend/entity/Follow.java
package com.threadclone.backend.entity;

import jakarta.persistence.*;
import java.time.OffsetDateTime;
import java.util.UUID;

@Entity
@Table(name = "follows")
public class Follow {
    @Id
    @Column(name = "follower_id")
    private UUID followerId;

    @Id
    @Column(name = "followed_id")
    private UUID followedId;

    @Column(name = "created_at")
    private OffsetDateTime createdAt;

    // Getters and setters
}
```

```java
// src/test/java/com/threadsclone/backend/controller/FollowControllerTest.java
package com.threadclone.backend.controller;

import com.threadclone.backend.service.FollowService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.doNothing;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(FollowController.class)
public class FollowControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private FollowService followService;

    @Test
    void shouldFollowUserSuccessfully() throws Exception {
        String userId = "uuid";
        doNothing().when(followService).followUser(userId);

        mockMvc.perform(post("/api/users/" + userId + "/follow"))
                .andExpect(status().isOk());
    }

    @Test
    void shouldUnfollowUserSuccessfully() throws Exception {
        String userId = "uuid";
        doNothing().when(followService).unfollowUser(userId);

        mockMvc.perform(delete("/api/users/" + userId + "/follow"))
                .andExpect(status().isOk());
    }
}
```

```java
// src/test/java/com/threadsclone/backend/controller/FollowIntegrationTest.java
package com.threadclone.backend.controller;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class FollowIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldFollowUserSuccessfully() {
        String userId = "uuid"; // 假設存在的用戶 ID
        ResponseEntity<Void> response = restTemplate.postForEntity(
                "/api/users/" + userId + "/follow",
                null,
                Void.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
    }

    @Test
    void shouldUnfollowUserSuccessfully() {
        String userId = "uuid"; // 假設已追蹤的用戶 ID
        ResponseEntity<Void> response = restTemplate.exchange(
                "/api/users/" + userId + "/follow",
                HttpMethod.DELETE,
                null,
                Void.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
    }
}
```

#### 迭代 4.2：個人資料 API

##### Prompt 19：實現個人資料 API

###### 背景

實現 `GET /api/users/:user_id` 和 `PUT /api/users/me`，分別用於查看用戶資料和更新個人資料（`username` 和 `bio`），遵守 RLS。

###### 任務

創建以下文件：

- `UserController.java`：處理用戶資料 API。
- `UserService.java`：實現用戶資料邏輯。
- `UserControllerTest.java`：單元測試。
- `UserIntegrationTest.java`：整合測試。

###### 測試要求

- **單元測試**：驗證資料檢索和更新，檢查 `username`（3-50 字）、`bio`（≤ 160 字）約束。
- **整合測試**：驗證 RLS（僅能查看自己或管理員查看所有），檢查更新後資料。

###### 整合方式

- 擴展 Prompt 18 的 Spring Boot 專案，添加新控制器和服務。
- 依賴 `users` 表的 RLS（`Users view own profile`, `Users update own profile`）。

```java
// src/main/java/com/threadsclone/backend/controller/UserController.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.UserRequest;
import com.threadclone.backend.dto.UserResponse;
import com.threadclone.backend.service.UserService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
@RequestMapping("/api/users")
public class UserController {
    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    @GetMapping("/{userId}")
    public ResponseEntity<UserResponse> getUser(@PathVariable String userId) {
        UserResponse response = userService.getUser(userId);
        return ResponseEntity.ok(response);
    }

    @PutMapping("/me")
    public ResponseEntity<UserResponse> updateUser(@RequestBody UserRequest request) {
        UserResponse response = userService.updateUser(request);
        return ResponseEntity.ok(response);
    }
}
```

```java
// src/main/java/com/threadsclone/backend/service/UserService.java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.UserRequest;
import com.threadclone.backend.dto.UserResponse;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

@Service
public class UserService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public UserService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public UserResponse getUser(String userId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var result = postgrest.from("users")
                .select("*")
                .eq("id", userId)
                .single()
                .execute();
        return new UserResponse(
                result.getString("id"),
                result.getString("email"),
                result.getString("username"),
                result.getString("bio"),
                result.getBoolean("is_verified"),
                result.getString("role"),
                result.getString("created_at")
        );
    }

    public UserResponse updateUser(UserRequest request) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        String userId = SupabaseClient.getAuth().getCurrentUser().getId();
        var result = postgrest.from("users")
                .update(new UserRequestWrapper(request.getUsername(), request.getBio()))
                .eq("id", userId)
                .select()
                .single()
                .execute();
        return new UserResponse(
                result.getString("id"),
                result.getString("email"),
                result.getString("username"),
                result.getString("bio"),
                result.getBoolean("is_verified"),
                result.getString("role"),
                result.getString("created_at")
        );
    }

    private static class UserRequestWrapper {
        private String username;
        private String bio;

        public UserRequestWrapper(String username, String bio) {
            this.username = username;
            this.bio = bio;
        }
    }
}
```

```java
// src/test/java/com/threadsclone/backend/controller/UserControllerTest.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.UserRequest;
import com.threadclone.backend.dto.UserResponse;
import com.threadclone.backend.service.UserService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.put;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(UserController.class)
public class UserControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private UserService userService;

    @Test
    void shouldGetUserSuccessfully() throws Exception {
        String userId = "uuid";
        UserResponse response = new UserResponse(userId, "test@example.com", "testuser", "Bio", true, "user", "2025-05-10T12:00:00Z");
        when(userService.getUser(userId)).thenReturn(response);

        mockMvc.perform(get("/api/users/" + userId))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.username").value("testuser"));
    }

    @Test
    void shouldUpdateUserSuccessfully() throws Exception {
        UserRequest request = new UserRequest("newuser", "New bio");
        UserResponse response = new UserResponse("uuid", "test@example.com", "newuser", "New bio", true, "user", "2025-05-10T12:00:00Z");
        when(userService.updateUser(request)).thenReturn(response);

        mockMvc.perform(put("/api/users/me")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"username\":\"newuser\",\"bio\":\"New bio\"}"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.username").value("newuser"));
    }
}
```

```java
// src/test/java/com/threadsclone/backend/controller/UserIntegrationTest.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.UserRequest;
import com.threadclone.backend.dto.UserResponse;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class UserIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldGetUserSuccessfully() {
        String userId = "uuid"; // 假設存在的用戶 ID
        ResponseEntity<UserResponse> response = restTemplate.getForEntity(
                "/api/users/" + userId,
                UserResponse.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
    }

    @Test
    void shouldUpdateUserSuccessfully() {
        UserRequest request = new UserRequest("newuser", "New bio");
        ResponseEntity<UserResponse> response = restTemplate.exchange(
                "/api/users/me",
                HttpMethod.PUT,
                new HttpEntity<>(request),
                UserResponse.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
        assertEquals("newuser", response.getBody().getUsername());
    }
}
```

#### 迭代 4.3：前端個人資料

##### Prompt 20：實現個人資料頁面

###### 背景

實現個人資料頁面，顯示用戶資料（`GET /api/users/:user_id`）、貼文和追蹤狀態，允許編輯個人資料（`PUT /api/users/me`）和追蹤操作（`POST/DELETE /api/users/:user_id/follow`）。

###### 任務

創建以下文件：

- `src/pages/Profile.jsx`：個人資料頁面組件。
- `src/pages/Profile.test.jsx`：單元測試。

###### 測試要求

- **單元測試**：驗證資料渲染、編輯表單提交、追蹤按鈕交互，模擬 API 呼叫。
- **手動測試**：驗證頁面樣式和交互（使用 Tailwind CSS）。

###### 整合方式

- 基於階段 3 的 React 專案，添加新頁面。
- 調用 `GET /api/users/:user_id`、`PUT /api/users/me`（Prompt 19）、`POST/DELETE /api/users/:user_id/follow`（Prompt 18）。

```jsx
// src/pages/Profile.jsx
import { useState, useEffect } from "react";
import { useParams } from "react-router-dom";
import { supabase } from "../lib/supabase";

export default function Profile() {
  const { userId } = useParams();
  const [user, setUser] = useState(null);
  const [posts, setPosts] = useState([]);
  const [isFollowing, setIsFollowing] = useState(false);
  const [editing, setEditing] = useState(false);
  const [username, setUsername] = useState("");
  const [bio, setBio] = useState("");
  const [error, setError] = useState(null);

  const loadUser = async () => {
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/users/${userId}`, {
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (!response.ok) throw new Error("加載用戶失敗");
      const userData = await response.json();
      setUser(userData);
      setUsername(userData.username);
      setBio(userData.bio || "");
    } catch (err) {
      setError(err.message);
    }
  };

  const loadPosts = async () => {
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/users/${userId}/posts`, {
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (!response.ok) throw new Error("加載貼文失敗");
      setPosts(await response.json());
    } catch (err) {
      console.error(err);
    }
  };

  const checkFollowing = async () => {
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) return;
      const response = await fetch(`/api/users/${userId}/follow-status`, {
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (response.ok) setIsFollowing(await response.json().isFollowing);
    } catch (err) {
      console.error(err);
    }
  };

  const handleFollow = async () => {
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/users/${userId}/follow`, {
        method: isFollowing ? "DELETE" : "POST",
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (!response.ok) throw new Error("追蹤操作失敗");
      setIsFollowing(!isFollowing);
    } catch (err) {
      setError(err.message);
    }
  };

  const handleUpdate = async (e) => {
    e.preventDefault();
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch("/api/users/me", {
        method: "PUT",
        headers: {
          "Content-Type": "application/json",
          Authorization: `Bearer ${data.session.access_token}`,
        },
        body: JSON.stringify({ username, bio }),
      });
      if (!response.ok) throw new Error("更新失敗");
      setUser((prev) => ({ ...prev, username, bio }));
      setEditing(false);
    } catch (err) {
      setError(err.message);
    }
  };

  useEffect(() => {
    loadUser();
    loadPosts();
    checkFollowing();
  }, [userId]);

  if (!user) return <p>加載中...</p>;

  return (
    <div className="max-w-2xl mx-auto p-4">
      <div className="bg-white dark:bg-gray-800 p-4 rounded-lg shadow-md">
        <h2 className="text-xl font-bold">{user.username}</h2>
        <p className="text-gray-600 dark:text-gray-400">
          {user.bio || "無簡介"}
        </p>
        <p className="text-sm text-gray-500 dark:text-gray-400">
          加入時間: {new Date(user.created_at).toLocaleDateString()}
        </p>
        {user.id === supabase.auth.getCurrentUser()?.id ? (
          <button
            onClick={() => setEditing(!editing)}
            className="mt-2 px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600"
          >
            {editing ? "取消" : "編輯資料"}
          </button>
        ) : (
          <button
            onClick={handleFollow}
            className={`mt-2 px-4 py-2 ${
              isFollowing ? "bg-gray-500" : "bg-blue-500"
            } text-white rounded-md hover:bg-opacity-80`}
          >
            {isFollowing ? "取消追蹤" : "追蹤"}
          </button>
        )}
        {editing && (
          <form onSubmit={handleUpdate} className="mt-4">
            <input
              type="text"
              value={username}
              onChange={(e) => setUsername(e.target.value)}
              placeholder="用戶名"
              className="w-full p-2 mb-2 border rounded-md dark:bg-gray-700 dark:text-white"
              maxLength={50}
            />
            <textarea
              value={bio}
              onChange={(e) => setBio(e.target.value)}
              placeholder="簡介"
              className="w-full p-2 mb-2 border rounded-md dark:bg-gray-700 dark:text-white"
              maxLength={160}
            />
            {error && <p className="text-red-500">{error}</p>}
            <button
              type="submit"
              className="px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600"
            >
              保存
            </button>
          </form>
        )}
      </div>
      <div className="mt-4">
        {posts.map((post) => (
          <div
            key={post.id}
            className="p-4 mb-4 bg-white dark:bg-gray-800 rounded-lg shadow-md"
          >
            <p className="text-gray-800 dark:text-gray-200">{post.content}</p>
            <p className="text-sm text-gray-500 dark:text-gray-400">
              {new Date(post.created_at).toLocaleString()}
            </p>
          </div>
        ))}
      </div>
    </div>
  );
}
```

```jsx
// src/pages/Profile.test.jsx
import { render, screen, fireEvent } from "@testing-library/react";
import { MemoryRouter, Route } from "react-router-dom";
import Profile from "./Profile";
import { supabase } from "../lib/supabase";

jest.mock("../lib/supabase", () => ({
  supabase: {
    auth: {
      getSession: jest.fn(),
      getCurrentUser: jest.fn(),
    },
  },
}));

describe("Profile", () => {
  beforeEach(() => {
    supabase.auth.getSession.mockResolvedValue({
      data: { session: { access_token: "mock_token" } },
      error: null,
    });
    supabase.auth.getCurrentUser.mockReturnValue({ id: "uuid" });
    global.fetch = jest.fn();
  });

  test("should render user profile", async () => {
    global.fetch
      .mockResolvedValueOnce({
        ok: true,
        json: () =>
          Promise.resolve({
            id: "uuid",
            username: "testuser",
            bio: "Test bio",
            created_at: "2025-05-10T12:00:00Z",
          }),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve([]),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve({ isFollowing: false }),
      });
    render(
      <MemoryRouter initialEntries={["/profile/uuid"]}>
        <Route path="/profile/:userId" component={Profile} />
      </MemoryRouter>
    );
    expect(await screen.findByText("testuser")).toBeInTheDocument();
    expect(screen.getByText("Test bio")).toBeInTheDocument();
  });

  test("should update profile", async () => {
    global.fetch
      .mockResolvedValueOnce({
        ok: true,
        json: () =>
          Promise.resolve({
            id: "uuid",
            username: "testuser",
            bio: "Test bio",
            created_at: "2025-05-10T12:00:00Z",
          }),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve([]),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve({ isFollowing: false }),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve({ username: "newuser", bio: "New bio" }),
      });
    render(
      <MemoryRouter initialEntries={["/profile/uuid"]}>
        <Route path="/profile/:userId" component={Profile} />
      </MemoryRouter>
    );
    fireEvent.click(await screen.findByText("編輯資料"));
    fireEvent.change(screen.getByPlaceholderText("用戶名"), {
      target: { value: "newuser" },
    });
    fireEvent.change(screen.getByPlaceholderText("簡介"), {
      target: { value: "New bio" },
    });
    fireEvent.click(screen.getByText("保存"));
    expect(global.fetch).toHaveBeenCalledWith(
      "/api/users/me",
      expect.objectContaining({
        method: "PUT",
        headers: expect.any(Object),
        body: JSON.stringify({ username: "newuser", bio: "New bio" }),
      })
    );
  });
});
```

### 階段 5：搜索與通知

目標：實現貼文搜索功能和簡單通知系統。

#### 迭代 5.1：搜索 API

##### Prompt 21：實現貼文搜索 API

###### 背景

實現 `GET /api/search`，使用 `posts.content_tsv` 進行全文搜索（僅英文），返回未刪除的貼文，支援分頁。

###### 任務

更新以下文件：

- `PostController.java`：添加 `GET /api/search`。
- `PostService.java`：實現搜索邏輯。
- `PostControllerTest.java`：添加單元測試。
- `PostIntegrationTest.java`：添加整合測試。

###### 測試要求

- **單元測試**：驗證搜索關鍵字匹配，模擬 `to_tsvector` 查詢。
- **整合測試**：驗證僅返回未刪除貼文，檢查分頁。

###### 整合方式

- 擴展 Prompt 16 的 `PostController` 和 `PostService`。
- 依賴 `posts` 表的 `content_tsv` 欄位和 RLS。

```java
// src/main/java/com/threadsclone/backend/controller/PostController.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.PostService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api")
public class PostController {
    private final PostService postService;

    public PostController(PostService postService) {
        this.postService = postService;
    }

    @PostMapping("/posts")
    public ResponseEntity<PostResponse> createPost(@RequestBody PostRequest request) {
        PostResponse response = postService.createPost(request);
        return ResponseEntity.ok(response);
    }

    @DeleteMapping("/posts/{postId}")
    public ResponseEntity<Void> deletePost(@PathVariable String postId) {
        postService.deletePost(postId);
        return ResponseEntity.noContent().build();
    }

    @GetMapping("/timeline")
    public ResponseEntity<List<PostResponse>> getTimeline(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(required = false) String cursor
    ) {
        List<PostResponse> posts = postService.getTimeline(page, size, cursor);
        return ResponseEntity.ok(posts);
    }

    @GetMapping("/search")
    public ResponseEntity<List<PostResponse>> searchPosts(
            @RequestParam String query,
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size
    ) {
        List<PostResponse> posts = postService.searchPosts(query, page, size);
        return ResponseEntity.ok(posts);
    }
}
```

```java
// src/main/java/com/threadsclone/backend/service/PostService.java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

@Service
public class PostService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public PostService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public PostResponse createPost(PostRequest request) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var result = postgrest.from("posts")
                .insert(new PostRequestWrapper(
                        request.getContent(),
                        request.getImageUrl(),
                        request.isAnonymous()
                ))
                .select()
                .single()
                .execute();
        return new PostResponse(
                result.getString("id"),
                result.getString("content"),
                result.getString("image_url"),
                result.getString("user_id"),
                result.getBoolean("is_anonymous"),
                result.getInt("likes_count"),
                result.getInt("replies_count"),
                result.getString("created_at")
        );
    }

    public void deletePost(String postId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        postgrest.from("posts")
                .update(new DeletePostWrapper())
                .eq("id", postId)
                .execute();
    }

    public List<PostResponse> getTimeline(int page, int size, String cursor) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var query = postgrest.from("posts")
                .select("*")
                .eq("is_deleted", false)
                .in("user_id", getFollowedUserIds())
                .order("created_at", "desc")
                .limit(size);
        if (cursor != null) {
            query.gt("created_at", cursor);
        }
        var results = query.execute();
        return results.getData().stream()
                .map(result -> new PostResponse(
                        result.getString("id"),
                        result.getString("content"),
                        result.getString("image_url"),
                        result.getString("user_id"),
                        result.getBoolean("is_anonymous"),
                        result.getInt("likes_count"),
                        result.getInt("replies_count"),
                        result.getString("created_at")
                ))
                .collect(Collectors.toList());
    }

    public List<PostResponse> searchPosts(String query, int page, int size) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var results = postgrest.from("posts")
                .select("*")
                .eq("is_deleted", false)
                .textSearch("content_tsv", query, "simple")
                .order("created_at", "desc")
                .limit(size)
                .offset(page * size)
                .execute();
        return results.getData().stream()
                .map(result -> new PostResponse(
                        result.getString("id"),
                        result.getString("content"),
                        result.getString("image_url"),
                        result.getString("user_id"),
                        result.getBoolean("is_anonymous"),
                        result.getInt("likes_count"),
                        result.getInt("replies_count"),
                        result.getString("created_at")
                ))
                .collect(Collectors.toList());
    }

    private List<String> getFollowedUserIds() {
        Postgrest postgrest = supabaseClient.getPostgrest();
        String currentUserId = SupabaseClient.getAuth().getCurrentUser().getId();
        var results = postgrest.from("follows")
                .select("followed_id")
                .eq("follower_id", currentUserId)
                .execute();
        return results.getData().stream()
                .map(result -> result.getString("followed_id"))
                .collect(Collectors.toList());
    }

    private static class PostRequestWrapper {
        private String content;
        private String image_url;
        private boolean is_anonymous;
        private String user_id;

        public PostRequestWrapper(String content, String imageUrl, boolean isAnonymous) {
            this.content = content;
            this.image_url = imageUrl;
            this.is_anonymous = isAnonymous;
            this.user_id = SupabaseClient.getAuth().getCurrentUser().getId();
        }
    }

    private static class DeletePostWrapper {
        private boolean is_deleted = true;
    }
}
```

```java
// src/test/java/com/threadsclone/backend/controller/PostControllerTest.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.PostService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;

import java.util.List;

import static org.mockito.Mockito.doNothing;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.post;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(PostController.class)
public class PostControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private PostService postService;

    @Test
    void shouldCreatePostSuccessfully() throws Exception {
        PostRequest request = new PostRequest("Hello world", null, false);
        PostResponse response = new PostResponse("uuid", "Hello world", null, "user_id", false, 0, 0, "2025-05-10T12:00:00Z");
        when(postService.createPost(request)).thenReturn(response);

        mockMvc.perform(post("/api/posts")
                .contentType(MediaType.APPLICATION_JSON)
                .content("{\"content\":\"Hello world\",\"imageUrl\":null,\"isAnonymous\":false}"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$.content").value("Hello world"));
    }

    @Test
    void shouldDeletePostSuccessfully() throws Exception {
        String postId = "uuid";
        doNothing().when(postService).deletePost(postId);

        mockMvc.perform(delete("/api/posts/" + postId))
                .andExpect(status().isNoContent());
    }

    @Test
    void shouldGetTimelineSuccessfully() throws Exception {
        List<PostResponse> posts = List.of(
                new PostResponse("uuid1", "Post 1", null, "user_id", false, 0, 0, "2025-05-10T12:00:00Z")
        );
        when(postService.getTimeline(0, 20, null)).thenReturn(posts);

        mockMvc.perform(get("/api/timeline?page=0&size=20"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$[0].content").value("Post 1"));
    }

    @Test
    void shouldSearchPostsSuccessfully() throws Exception {
        List<PostResponse> posts = List.of(
                new PostResponse("uuid1", "Hello world", null, "user_id", false, 0, 0, "2025-05-10T12:00:00Z")
        );
        when(postService.searchPosts("hello", 0, 20)).thenReturn(posts);

        mockMvc.perform(get("/api/search?query=hello&page=0&size=20"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$[0].content").value("Hello world"));
    }
}
```

```java
// src/test/java/com/threadsclone/backend/controller/PostIntegrationTest.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostRequest;
import com.threadclone.backend.dto.PostResponse;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class PostIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldCreatePostSuccessfully() {
        PostRequest request = new PostRequest("Hello world", null, false);
        ResponseEntity<PostResponse> response = restTemplate.postForEntity(
                "/api/posts",
                request,
                PostResponse.class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
        assertEquals("Hello world", response.getBody().getContent());
    }

    @Test
    void shouldDeletePostSuccessfully() {
        String postId = "uuid"; // 假設已創建的貼文 ID
        ResponseEntity<Void> response = restTemplate.exchange(
                "/api/posts/" + postId,
                HttpMethod.DELETE,
                null,
                Void.class
        );
        assertEquals(HttpStatus.NO_CONTENT, response.getStatusCode());
    }

    @Test
    void shouldGetTimelineSuccessfully() {
        ResponseEntity<PostResponse[]> response = restTemplate.getForEntity(
                "/api/timeline?page=0&size=20",
                PostResponse[].class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
    }

    @Test
    void shouldSearchPostsSuccessfully() {
        ResponseEntity<PostResponse[]> response = restTemplate.getForEntity(
                "/api/search?query=hello&page=0&size=20",
                PostResponse[].class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
    }
}
```

#### 迭代 5.2：前端搜索

##### Prompt 22：實現搜索頁面

###### 背景

實現搜索頁面，調用 `GET /api/search`，顯示匹配的貼文，支援關鍵字輸入和分頁。

###### 任務

創建以下文件：

- `src/pages/Search.jsx`：搜索頁面組件。
- `src/pages/Search.test.jsx`：單元測試。

###### 測試要求

- **單元測試**：驗證搜索結果渲染，模擬 API 呼叫。
- **手動測試**：驗證搜索輸入和貼文卡片樣式。

###### 整合方式

- 基於階段 4 的 React 專案，添加新頁面。
- 調用 `GET /api/search`（Prompt 21）。

```jsx
// src/pages/Search.jsx
import { useState, useEffect } from "react";
import { supabase } from "../lib/supabase";

export default function Search() {
  const [query, setQuery] = useState("");
  const [posts, setPosts] = useState([]);
  const [page, setPage] = useState(0);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);

  const searchPosts = async () => {
    if (loading || !hasMore || !query) return;
    setLoading(true);
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(
        `/api/search?query=${encodeURIComponent(query)}&page=${page}&size=20`,
        {
          headers: {
            Authorization: `Bearer ${data.session.access_token}`,
          },
        }
      );
      if (!response.ok) throw new Error("搜索失敗");
      const newPosts = await response.json();
      setPosts((prev) => [...prev, ...newPosts]);
      setPage(page + 1);
      setHasMore(newPosts.length === 20);
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  const handleSearch = (e) => {
    e.preventDefault();
    setPosts([]);
    setPage(0);
    setHasMore(true);
    searchPosts();
  };

  useEffect(() => {
    if (query) searchPosts();
  }, [page]);

  return (
    <div className="max-w-2xl mx-auto p-4">
      <form onSubmit={handleSearch} className="mb-4">
        <input
          type="text"
          value={query}
          onChange={(e) => setQuery(e.target.value)}
          placeholder="搜索貼文..."
          className="w-full p-2 border rounded-md dark:bg-gray-700 dark:text-white"
        />
        <button
          type="submit"
          className="mt-2 px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600"
        >
          搜索
        </button>
      </form>
      <div>
        {posts.map((post) => (
          <div
            key={post.id}
            className="p-4 mb-4 bg-white dark:bg-gray-800 rounded-lg shadow-md"
          >
            <p className="text-gray-800 dark:text-gray-200">{post.content}</p>
            <p className="text-sm text-gray-500 dark:text-gray-400">
              {post.is_anonymous ? "匿名" : post.user_id} ·{" "}
              {new Date(post.created_at).toLocaleString()}
            </p>
          </div>
        ))}
      </div>
      {loading && <p className="text-center">加載中...</p>}
    </div>
  );
}
```

```jsx
// src/pages/Search.test.jsx
import { render, screen, fireEvent } from "@testing-library/react";
import Search from "./Search";
import { supabase } from "../lib/supabase";

jest.mock("../lib/supabase", () => ({
  supabase: {
    auth: {
      getSession: jest.fn(),
    },
  },
}));

describe("Search", () => {
  beforeEach(() => {
    supabase.auth.getSession.mockResolvedValue({
      data: { session: { access_token: "mock_token" } },
      error: null,
    });
    global.fetch = jest.fn();
  });

  test("should render search results", async () => {
    global.fetch.mockResolvedValueOnce({
      ok: true,
      json: () =>
        Promise.resolve([
          {
            id: "1",
            content: "Hello world",
            user_id: "user1",
            created_at: "2025-05-10T12:00:00Z",
          },
        ]),
    });
    render(<Search />);
    fireEvent.change(screen.getByPlaceholderText("搜索貼文..."), {
      target: { value: "hello" },
    });
    fireEvent.click(screen.getByText("搜索"));
    expect(await screen.findByText("Hello world")).toBeInTheDocument();
  });
});
```

#### 迭代 5.3：通知系統

##### Prompt 23：實現通知 API

###### 背景

實現簡單通知系統，記錄點讚和回覆事件，儲存到新創建的 `notifications` 表，提供 `GET /api/notifications` 返回用戶通知。

###### 任務

創建以下文件：

- `notifications.sql`：創建 `notifications` 表和觸發器。
- `NotificationController.java`：處理 `GET /api/notifications`。
- `NotificationService.java`：實現通知邏輯。
- `Notification.java`：通知實體類。
- `NotificationControllerTest.java`：單元測試。
- `NotificationIntegrationTest.java`：整合測試。

###### 測試要求

- **單元測試**：驗證通知查詢，模擬分頁。
- **整合測試**：驗證點讚和回覆觸發通知，檢查 RLS。

###### 整合方式

- 擴展 Prompt 21 的 Spring Boot 專案，添加新表和服務。
- 更新 `database-schema.sql`，添加 `notifications` 表。

```sql
// database/notifications.sql
-- 創建 notifications 表
CREATE TABLE notifications (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  actor_id UUID REFERENCES users(id) ON DELETE SET NULL,
  type TEXT CHECK (type IN ('like', 'reply')),
  post_id UUID REFERENCES posts(id) ON DELETE CASCADE,
  reply_id UUID REFERENCES replies(id) ON DELETE SET NULL,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);

-- 啟用 RLS
ALTER TABLE notifications ENABLE ROW LEVEL SECURITY;

-- RLS 政策
CREATE POLICY "Users view own notifications" ON notifications
FOR SELECT TO authenticated
USING (user_id = auth.uid());

-- 創建索引
CREATE INDEX idx_notifications_user_id ON notifications(user_id, created_at);

-- 點讚通知觸發器
CREATE OR REPLACE FUNCTION public.handle_new_like()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO public.notifications (user_id, actor_id, type, post_id, created_at)
  SELECT p.user_id, NEW.user_id, 'like', NEW.post_id, NOW()
  FROM posts p
  WHERE p.id = NEW.post_id AND p.user_id != NEW.user_id;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_like_created
  AFTER INSERT ON likes
  FOR EACH ROW EXECUTE FUNCTION public.handle_new_like();

-- 回覆通知觸發器
CREATE OR REPLACE FUNCTION public.handle_new_reply()
RETURNS TRIGGER AS $$
BEGIN
  INSERT INTO public.notifications (user_id, actor_id, type, post_id, reply_id, created_at)
  SELECT p.user_id, NEW.user_id, 'reply', NEW.post_id, NEW.id, NOW()
  FROM posts p
  WHERE p.id = NEW.post_id AND p.user_id != NEW.user_id;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql SECURITY DEFINER;

CREATE TRIGGER on_reply_created
  AFTER INSERT ON replies
  FOR EACH ROW EXECUTE FUNCTION public.handle_new_reply();
```

```java
// src/main/java/com/threadsclone/backend/controller/NotificationController.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.NotificationResponse;
import com.threadclone.backend.service.NotificationService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api")
public class NotificationController {
    private final NotificationService notificationService;

    public NotificationController(NotificationService notificationService) {
        this.notificationService = notificationService;
    }

    @GetMapping("/notifications")
    public ResponseEntity<List<NotificationResponse>> getNotifications(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size
    ) {
        List<NotificationResponse> notifications = notificationService.getNotifications(page, size);
        return ResponseEntity.ok(notifications);
    }
}
```

```java
// src/main/java/com/threadsclone/backend/service/NotificationService.java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.NotificationResponse;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

@Service
public class NotificationService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public NotificationService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public List<NotificationResponse> getNotifications(int page, int size) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var results = postgrest.from("notifications")
                .select("*")
                .order("created_at", "desc")
                .limit(size)
                .offset(page * size)
                .execute();
        return results.getData().stream()
                .map(result -> new NotificationResponse(
                        result.getString("id"),
                        result.getString("user_id"),
                        result.getString("actor_id"),
                        result.getString("type"),
                        result.getString("post_id"),
                        result.getString("reply_id"),
                        result.getString("created_at")
                ))
                .collect(Collectors.toList());
    }
}
```

```java
// src/main/java/com/threadsclone/backend/entity/Notification.java
package com.threadclone.backend.entity;

import jakarta.persistence.*;
import java.time.OffsetDateTime;
import java.util.UUID;

@Entity
@Table(name = "notifications")
public class Notification {
    @Id
    @GeneratedValue
    private UUID id;

    @Column(name = "user_id")
    private UUID userId;

    @Column(name = "actor_id")
    private UUID actorId;

    @Column
    private String type;

    @Column(name = "post_id")
    private UUID postId;

    @Column(name = "reply_id")
    private UUID replyId;

    @Column(name = "created_at")
    private OffsetDateTime createdAt;

    // Getters and setters
}
```

```java
// src/test/java/com/threadsclone/backend/controller/NotificationControllerTest.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.NotificationResponse;
import com.threadclone.backend.service.NotificationService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;

import java.util.List;

import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(NotificationController.class)
public class NotificationControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private NotificationService notificationService;

    @Test
    void shouldGetNotificationsSuccessfully() throws Exception {
        List<NotificationResponse> notifications = List.of(
                new NotificationResponse("uuid1", "user1", "user2", "like", "post1", null, "2025-05-10T12:00:00Z")
        );
        when(notificationService.getNotifications(0, 20)).thenReturn(notifications);

        mockMvc.perform(get("/api/notifications?page=0&size=20"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$[0].type").value("like"));
    }
}
```

```java
// src/test/java/com/threadsclone/backend/controller/NotificationIntegrationTest.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.NotificationResponse;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class NotificationIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldGetNotificationsSuccessfully() {
        ResponseEntity<NotificationResponse[]> response = restTemplate.getForEntity(
                "/api/notifications?page=0&size=20",
                NotificationResponse[].class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
    }
}
```

#### 迭代 5.4：前端通知

##### Prompt 24：實現通知頁面

###### 背景

實現通知頁面，調用 `GET /api/notifications`，顯示用戶的點讚和回覆通知，支援分頁。

###### 任務

創建以下文件：

- `src/pages/Notifications.jsx`：通知頁面組件。
- `src/pages/Notifications.test.jsx`：單元測試。

###### 測試要求

- **單元測試**：驗證通知渲染，模擬 API 呼叫。
- **手動測試**：驗證通知樣式和分頁。

###### 整合方式

- 基於階段 4 的 React 專案，添加新頁面。
- 調用 `GET /api/notifications`（Prompt 23）。

```jsx
// src/pages/Notifications.jsx
import { useState, useEffect } from "react";
import { supabase } from "../lib/supabase";

export default function Notifications() {
  const [notifications, setNotifications] = useState([]);
  const [page, setPage] = useState(0);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);

  const loadNotifications = async () => {
    if (loading || !hasMore) return;
    setLoading(true);
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/notifications?page=${page}&size=20`, {
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (!response.ok) throw new Error("加載通知失敗");
      const newNotifications = await response.json();
      setNotifications((prev) => [...prev, ...newNotifications]);
      setPage(page + 1);
      setHasMore(newNotifications.length === 20);
    } catch (err) {
      console.error(err);
    } finally {
      setLoading(false);
    }
  };

  useEffect(() => {
    loadNotifications();
  }, [page]);

  return (
    <div className="max-w-2xl mx-auto p-4">
      <h2 className="text-xl font-bold mb-4">通知</h2>
      <div>
        {notifications.map((notification) => (
          <div
            key={notification.id}
            className="p-4 mb-4 bg-white dark:bg-gray-800 rounded-lg shadow-md"
          >
            <p className="text-gray-800 dark:text-gray-200">
              {notification.type === "like"
                ? `${notification.actor_id} 讚了你的貼文`
                : `${notification.actor_id} 回覆了你的貼文`}
            </p>
            <p className="text-sm text-gray-500 dark:text-gray-400">
              {new Date(notification.created_at).toLocaleString()}
            </p>
          </div>
        ))}
      </div>
      {loading && <p className="text-center">加載中...</p>}
    </div>
  );
}
```

```jsx
// src/pages/Notifications.test.jsx
import { render, screen } from "@testing-library/react";
import Notifications from "./Notifications";
import { supabase } from "../lib/supabase";

jest.mock("../lib/supabase", () => ({
  supabase: {
    auth: {
      getSession: jest.fn(),
    },
  },
}));

describe("Notifications", () => {
  beforeEach(() => {
    supabase.auth.getSession.mockResolvedValue({
      data: { session: { access_token: "mock_token" } },
      error: null,
    });
    global.fetch = jest.fn();
  });

  test("should render notifications", async () => {
    global.fetch.mockResolvedValueOnce({
      ok: true,
      json: () =>
        Promise.resolve([
          {
            id: "1",
            actor_id: "user1",
            type: "like",
            created_at: "2025-05-10T12:00:00Z",
          },
        ]),
    });
    render(<Notifications />);
    expect(await screen.findByText("user1 讚了你的貼文")).toBeInTheDocument();
  });
});
```

### 階段 6：管理員功能

目標：實現管理員功能，允許管理員查看和刪除所有貼文/回覆。

#### 迭代 6.1：管理員 API

##### Prompt 25：實現管理員 API

###### 背景

實現 `GET /api/admin/posts` 和 `DELETE /api/admin/posts/:post_id`，允許管理員（`users.role = 'admin'`）查看和刪除所有貼文（包括匿名和已刪除貼文）。

###### 任務

創建以下文件：

- `AdminController.java`：處理管理員 API。
- `AdminService.java`：實現管理員邏輯。
- `AdminControllerTest.java`：單元測試。
- `AdminIntegrationTest.java`：整合測試。

###### 測試要求

- **單元測試**：驗證僅管理員可訪問，模擬 RLS。
- **整合測試**：驗證返回所有貼文（包括匿名/已刪除），檢查刪除功能。

###### 整合方式

- 擴展 Prompt 23 的 Spring Boot 專案，添加新控制器和服務。
- 依賴 `posts` 表的 RLS（`Admins view all posts`, `Admins delete all posts`）。

```java
// src/main/java/com/threadsclone/backend/controller/AdminController.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.AdminService;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.List;

@RestController
@RequestMapping("/api/admin")
public class AdminController {
    private final AdminService adminService;

    public AdminController(AdminService adminService) {
        this.adminService = adminService;
    }

    @GetMapping("/posts")
    public ResponseEntity<List<PostResponse>> getAllPosts(
            @RequestParam(defaultValue = "0") int page,
            @RequestParam(defaultValue = "20") int size,
            @RequestParam(defaultValue = "false") boolean includeDeleted,
            @RequestParam(defaultValue = "false") boolean includeAnonymous
    ) {
        List<PostResponse> posts = adminService.getAllPosts(page, size, includeDeleted, includeAnonymous);
        return ResponseEntity.ok(posts);
    }

    @DeleteMapping("/posts/{postId}")
    public ResponseEntity<Void> deletePost(@PathVariable String postId) {
        adminService.deletePost(postId);
        return ResponseEntity.noContent().build();
    }
}
```

```java
// src/main/java/com/threadsclone/backend/service/AdminService.java
package com.threadclone.backend.service;

import com.threadclone.backend.dto.PostResponse;
import io.github.jan.supabase.SupabaseClient;
import io.github.jan.supabase.postgrest.Postgrest;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.stream.Collectors;

@Service
public class AdminService {
    private final SupabaseClient supabaseClient;

    @Autowired
    public AdminService(SupabaseClient supabaseClient) {
        this.supabaseClient = supabaseClient;
    }

    public List<PostResponse> getAllPosts(int page, int size, boolean includeDeleted, boolean includeAnonymous) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        var query = postgrest.from("posts")
                .select("*")
                .order("created_at", "desc")
                .limit(size)
                .offset(page * size);
        if (!includeDeleted) {
            query.eq("is_deleted", false);
        }
        if (!includeAnonymous) {
            query.eq("is_anonymous", false);
        }
        var results = query.execute();
        return results.getData().stream()
                .map(result -> new PostResponse(
                        result.getString("id"),
                        result.getString("content"),
                        result.getString("image_url"),
                        result.getString("user_id"),
                        result.getBoolean("is_anonymous"),
                        result.getInt("likes_count"),
                        result.getInt("replies_count"),
                        result.getString("created_at")
                ))
                .collect(Collectors.toList());
    }

    public void deletePost(String postId) {
        Postgrest postgrest = supabaseClient.getPostgrest();
        postgrest.from("posts")
                .update(new DeletePostWrapper())
                .eq("id", postId)
                .execute();
    }

    private static class DeletePostWrapper {
        private boolean is_deleted = true;
    }
}
```

```java
// src/test/java/com/threadsclone/backend/controller/AdminControllerTest.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostResponse;
import com.threadclone.backend.service.AdminService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.boot.test.mock.mockito.MockBean;
import org.springframework.test.web.servlet.MockMvc;

import java.util.List;

import static org.mockito.Mockito.doNothing;
import static org.mockito.Mockito.when;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.delete;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.jsonPath;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(AdminController.class)
public class AdminControllerTest {
    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private AdminService adminService;

    @Test
    void shouldGetAllPostsSuccessfully() throws Exception {
        List<PostResponse> posts = List.of(
                new PostResponse("uuid1", "Post 1", null, "user_id", false, 0, 0, "2025-05-10T12:00:00Z")
        );
        when(adminService.getAllPosts(0, 20, false, false)).thenReturn(posts);

        mockMvc.perform(get("/api/admin/posts?page=0&size=20&includeDeleted=false&includeAnonymous=false"))
                .andExpect(status().isOk())
                .andExpect(jsonPath("$[0].content").value("Post 1"));
    }

    @Test
    void shouldDeletePostSuccessfully() throws Exception {
        String postId = "uuid";
        doNothing().when(adminService).deletePost(postId);

        mockMvc.perform(delete("/api/admin/posts/" + postId))
                .andExpect(status().isNoContent());
    }
}
```

```java
// src/test/java/com/threadsclone/backend/controller/AdminIntegrationTest.java
package com.threadclone.backend.controller;

import com.threadclone.backend.dto.PostResponse;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.boot.test.web.client.TestRestTemplate;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import static org.junit.jupiter.api.Assertions.assertEquals;
import static org.junit.jupiter.api.Assertions.assertNotNull;

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
public class AdminIntegrationTest {
    @Autowired
    private TestRestTemplate restTemplate;

    @Test
    void shouldGetAllPostsSuccessfully() {
        ResponseEntity<PostResponse[]> response = restTemplate.getForEntity(
                "/api/admin/posts?page=0&size=20&includeDeleted=false&includeAnonymous=false",
                PostResponse[].class
        );
        assertEquals(HttpStatus.OK, response.getStatusCode());
        assertNotNull(response.getBody());
    }

    @Test
    void shouldDeletePostSuccessfully() {
        String postId = "uuid"; // 假設存在的貼文 ID
        ResponseEntity<Void> response = restTemplate.exchange(
                "/api/admin/posts/" + postId,
                HttpMethod.DELETE,
                null,
                Void.class
        );
        assertEquals(HttpStatus.NO_CONTENT, response.getStatusCode());
    }
}
```

#### 迭代 6.2：前端管理員面板

感謝您的回饋！很高興您喜歡每個程式碼區塊開頭包含路徑的格式，我會繼續保持這種清晰的結構。以下是 **階段 6：管理員功能** 的 **迭代 6.2：前端管理員面板** 的詳細實現，延續前述的 Threads Clone 社交媒體平台藍圖，從 **Prompt 25** 的管理員 API 開始，實現前端管理員面板。我們將遵循您指定的風格：使用 **繁體中文（台灣）**，結構化、詳細且非冗餘，程式碼使用標準 Markdown 格式（```）包裝，不使用 `<xaiArtifact>`標籤，並在每個程式碼區塊開頭包含檔案路徑。回應將基於 **TDD（測試驅動開發）** 方法，確保與先前步驟（特別是 Prompt 25 的管理員 API）整合，並依賴更新的`database-schema.sql`（包含 `notifications` 表和相關 RLS 政策）。

---

## 步驟五：代碼生成提示（續）

### 階段 6：管理員功能（續）

#### 迭代 6.2：前端管理員面板

##### Prompt 26：實現管理員面板

###### 背景

實現管理員面板頁面，僅限管理員（`users.role = 'admin'`）訪問，調用 `GET /api/admin/posts` 顯示所有貼文（可選擇顯示已刪除或匿名貼文），並支援通過 `DELETE /api/admin/posts/:post_id` 刪除貼文。該功能提供管理員對平台內容的全面控制。

###### 任務

創建以下文件：

- `src/pages/AdminPanel.jsx`：管理員面板頁面組件，包含貼文列表、篩選選項（已刪除/匿名）和刪除按鈕。
- `src/pages/AdminPanel.test.jsx`：單元測試，驗證頁面渲染和交互。

###### 測試要求

- **單元測試**：模擬 API 呼叫，驗證：
  - 僅管理員可訪問頁面（非管理員顯示錯誤）。
  - 貼文列表正確渲染（包含已刪除/匿名選項）。
  - 刪除按鈕觸發 `DELETE /api/admin/posts/:post_id` 並刷新列表。
  - 錯誤處理（例如未登入或無權限）。
- **手動測試**：驗證：
  - 頁面樣式（使用 Tailwind CSS）。
  - 篩選選項切換正確更新貼文列表。
  - 刪除操作後列表即時更新。

###### 整合方式

- 基於階段 5 的 React 專案，添加新頁面。
- 調用 `GET /api/admin/posts` 和 `DELETE /api/admin/posts/:post_id`（Prompt 25）。
- 依賴 `users` 表中的 `role` 欄位（檢查 `admin` 角色）及 `posts` 表的 RLS（`Admins view all posts`, `Admins delete all posts`）。
- 使用 Supabase 驗證當前用戶角色，確保僅管理員可訪問。

```jsx
// src/pages/AdminPanel.jsx
import { useState, useEffect } from "react";
import { supabase } from "../lib/supabase";

export default function AdminPanel() {
  const [posts, setPosts] = useState([]);
  const [page, setPage] = useState(0);
  const [loading, setLoading] = useState(false);
  const [hasMore, setHasMore] = useState(true);
  const [includeDeleted, setIncludeDeleted] = useState(false);
  const [includeAnonymous, setIncludeAnonymous] = useState(false);
  const [error, setError] = useState(null);
  const [isAdmin, setIsAdmin] = useState(false);

  const checkAdmin = async () => {
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/users/${data.session.user.id}`, {
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (!response.ok) throw new Error("無法驗證用戶");
      const userData = await response.json();
      setIsAdmin(userData.role === "admin");
    } catch (err) {
      setError(err.message);
    }
  };

  const loadPosts = async () => {
    if (loading || !hasMore || !isAdmin) return;
    setLoading(true);
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(
        `/api/admin/posts?page=${page}&size=20&includeDeleted=${includeDeleted}&includeAnonymous=${includeAnonymous}`,
        {
          headers: {
            Authorization: `Bearer ${data.session.access_token}`,
          },
        }
      );
      if (!response.ok) throw new Error("加載貼文失敗");
      const newPosts = await response.json();
      setPosts((prev) => [...prev, ...newPosts]);
      setPage(page + 1);
      setHasMore(newPosts.length === 20);
    } catch (err) {
      setError(err.message);
    } finally {
      setLoading(false);
    }
  };

  const handleDelete = async (postId) => {
    try {
      const { data, error } = await supabase.auth.getSession();
      if (error || !data.session) throw new Error("未登入");
      const response = await fetch(`/api/admin/posts/${postId}`, {
        method: "DELETE",
        headers: {
          Authorization: `Bearer ${data.session.access_token}`,
        },
      });
      if (!response.ok) throw new Error("刪除失敗");
      setPosts([]);
      setPage(0);
      setHasMore(true);
      loadPosts();
    } catch (err) {
      setError(err.message);
    }
  };

  useEffect(() => {
    checkAdmin();
  }, []);

  useEffect(() => {
    if (isAdmin) {
      setPosts([]);
      setPage(0);
      setHasMore(true);
      loadPosts();
    }
  }, [includeDeleted, includeAnonymous, isAdmin]);

  if (!isAdmin) {
    return (
      <div className="max-w-2xl mx-auto p-4">
        <p className="text-red-500">僅限管理員訪問</p>
        {error && <p className="text-red-500">{error}</p>}
      </div>
    );
  }

  return (
    <div className="max-w-2xl mx-auto p-4">
      <h2 className="text-xl font-bold mb-4">管理員面板</h2>
      <div className="flex items-center mb-4">
        <label className="flex items-center mr-4">
          <input
            type="checkbox"
            checked={includeDeleted}
            onChange={(e) => setIncludeDeleted(e.target.checked)}
            className="mr-2"
          />
          包含已刪除貼文
        </label>
        <label className="flex items-center">
          <input
            type="checkbox"
            checked={includeAnonymous}
            onChange={(e) => setIncludeAnonymous(e.target.checked)}
            className="mr-2"
          />
          包含匿名貼文
        </label>
      </div>
      <div>
        {posts.map((post) => (
          <div
            key={post.id}
            className="p-4 mb-4 bg-white dark:bg-gray-800 rounded-lg shadow-md"
          >
            <p className="text-gray-800 dark:text-gray-200">{post.content}</p>
            {post.image_url && (
              <img
                src={post.image_url}
                alt="Post"
                className="mt-2 rounded-md max-w-full"
              />
            )}
            <p className="text-sm text-gray-500 dark:text-gray-400">
              {post.is_anonymous ? "匿名" : post.user_id} ·{" "}
              {new Date(post.created_at).toLocaleString()} ·{" "}
              {post.is_deleted ? "已刪除" : "未刪除"}
            </p>
            <button
              onClick={() => handleDelete(post.id)}
              className="mt-2 px-4 py-2 bg-red-500 text-white rounded-md hover:bg-red-600"
            >
              刪除
            </button>
          </div>
        ))}
      </div>
      {loading && <p className="text-center">加載中...</p>}
      {error && <p className="text-red-500">{error}</p>}
    </div>
  );
}
```

```jsx
// src/pages/AdminPanel.test.jsx
import { render, screen, fireEvent } from "@testing-library/react";
import AdminPanel from "./AdminPanel";
import { supabase } from "../lib/supabase";

jest.mock("../lib/supabase", () => ({
  supabase: {
    auth: {
      getSession: jest.fn(),
    },
  },
}));

describe("AdminPanel", () => {
  beforeEach(() => {
    supabase.auth.getSession.mockResolvedValue({
      data: { session: { access_token: "mock_token", user: { id: "uuid" } } },
      error: null,
    });
    global.fetch = jest.fn();
  });

  test("should restrict access to non-admins", async () => {
    global.fetch.mockResolvedValueOnce({
      ok: true,
      json: () => Promise.resolve({ role: "user" }),
    });
    render(<AdminPanel />);
    expect(await screen.findByText("僅限管理員訪問")).toBeInTheDocument();
  });

  test("should render posts for admin", async () => {
    global.fetch
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve({ role: "admin" }),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () =>
          Promise.resolve([
            {
              id: "1",
              content: "Test post",
              user_id: "user1",
              created_at: "2025-05-10T12:00:00Z",
              is_deleted: false,
              is_anonymous: false,
            },
          ]),
      });
    render(<AdminPanel />);
    expect(await screen.findByText("Test post")).toBeInTheDocument();
  });

  test("should delete post", async () => {
    global.fetch
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve({ role: "admin" }),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () =>
          Promise.resolve([
            {
              id: "1",
              content: "Test post",
              user_id: "user1",
              created_at: "2025-05-10T12:00:00Z",
              is_deleted: false,
              is_anonymous: false,
            },
          ]),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve({}),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve([]),
      });
    render(<AdminPanel />);
    fireEvent.click(await screen.findByText("刪除"));
    expect(global.fetch).toHaveBeenCalledWith(
      "/api/admin/posts/1",
      expect.objectContaining({
        method: "DELETE",
        headers: expect.any(Object),
      })
    );
  });

  test("should filter deleted posts", async () => {
    global.fetch
      .mockResolvedValueOnce({
        ok: true,
        json: () => Promise.resolve({ role: "admin" }),
      })
      .mockResolvedValueOnce({
        ok: true,
        json: () =>
          Promise.resolve([
            {
              id: "1",
              content: "Deleted post",
              user_id: "user1",
              created_at: "2025-05-10T12:00:00Z",
              is_deleted: true,
              is_anonymous: false,
            },
          ]),
      });
    render(<AdminPanel />);
    fireEvent.click(await screen.findByLabelText("包含已刪除貼文"));
    expect(await screen.findByText("Deleted post")).toBeInTheDocument();
  });
});
```

---

## 總結與整合說明

### 功能概述

- **管理員面板（Prompt 26）**：
  - 提供管理員專屬頁面，僅 `users.role = 'admin'` 的用戶可訪問。
  - 顯示所有貼文列表，支援篩選已刪除或匿名貼文。
  - 允許管理員刪除任何貼文，通過 `DELETE /api/admin/posts/:post_id` 實現。
  - 使用 Tailwind CSS 確保一致的樣式，與其他頁面（如時間軸、個人資料）保持視覺統一。
- **測試覆蓋**：
  - 單元測試驗證角色檢查、貼文渲染、篩選功能和刪除操作。
  - 模擬 Supabase 認證和 API 呼叫，確保錯誤處理（未登入、無權限）。
  - 手動測試確認 UI 交互和樣式。

### 與先前步驟的整合

- **後端依賴**：
  - 依賴 Prompt 25 的 `AdminController` 和 `AdminService`，確保 `GET /api/admin/posts` 返回所有貼文（包括篩選選項），`DELETE /api/admin/posts/:post_id` 正確更新 `posts.is_deleted`。
  - 使用 `database-schema.sql` 中的 `posts` 表 RLS 政策（`Admins view all posts`, `Admins delete all posts`），確保僅管理員可執行操作。
- **前端依賴**：
  - 基於階段 5 的 React 專案結構，特別是 `supabase.js`（用於認證）和 Tailwind CSS 配置。
  - 延續 `Timeline.jsx`（Prompt 17）的貼文卡片樣式，確保管理員面板的貼文顯示一致。
  - 使用 Supabase 客戶端檢查用戶角色（`GET /api/users/:user_id`），與 `Profile.jsx`（Prompt 20）共用邏輯。

### 下一步建議

- **增強功能**：
  - 添加批量刪除功能，允許管理員一次刪除多篇貼文。
  - 實現管理員對回覆的管理（類似 `GET /api/admin/replies` 和 `DELETE /api/admin/replies/:reply_id`）。
  - 提供貼文審核日誌，記錄管理員操作（例如儲存到新表 `admin_logs`）。
- **效能優化**：
  - 在 `GET /api/admin/posts` 中添加快取（例如使用 Redis），減少對 Supabase 的直接查詢。
  - 優化前端分頁邏輯，使用 IntersectionObserver（如 `Timeline.jsx`）實現無限滾動。
- **安全性**：
  - 強化管理員 API 的速率限制，防止濫用。
  - 定期審計 RLS 政策，確保無漏洞。

### 部署注意事項

- 確保 Supabase 環境變數（`SUPABASE_URL`, `SUPABASE_KEY`）正確配置。
- 在部署前運行所有單元測試和整合測試，特別是 `AdminPanel.test.jsx` 和 `AdminIntegrationTest.java`。
- 驗證 Tailwind CSS 在生產環境中的樣式一致性，檢查暗黑模式切換。

---

這完成了 **迭代 6.2：前端管理員面板** 的實現，涵蓋了所有要求的程式碼和測試。如果您希望繼續實現其他功能（例如管理員對回覆的管理或日誌功能），或者需要對現有代碼進行調整（例如添加新功能或優化），請隨時告訴我！您也可以指定是否需要特定的程式碼片段（例如路由配置 `src/App.jsx`）或更詳細的測試案例。
