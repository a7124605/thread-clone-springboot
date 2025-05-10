```
thread-clone/
├── backend/                                    # Spring Boot 後端專案
│   ├── src/
│   │   ├── main/
│   │   │   ├── java/com/threadclone/backend/
│   │   │   │   ├── controller/              # 控制器
│   │   │   │   │   ├── AdminController.java
│   │   │   │   │   ├── FollowController.java
│   │   │   │   │   ├── NotificationController.java
│   │   │   │   │   ├── PostController.java
│   │   │   │   │   └── UserController.java
│   │   │   │   ├── dto/                    # 資料傳輸對象
│   │   │   │   │   ├── NotificationResponse.java
│   │   │   │   │   ├── PostRequest.java
│   │   │   │   │   ├── PostResponse.java
│   │   │   │   │   ├── UserRequest.java
│   │   │   │   │   └── UserResponse.java
│   │   │   │   ├── entity/                 # 實體類
│   │   │   │   │   ├── Follow.java
│   │   │   │   │   ├── Notification.java
│   │   │   │   │   └── Post.java
│   │   │   │   ├── service/                # 服務邏輯
│   │   │   │   │   ├── AdminService.java
│   │   │   │   │   ├── FollowService.java
│   │   │   │   │   ├── NotificationService.java
│   │   │   │   │   ├── PostService.java
│   │   │   │   │   └── UserService.java
│   │   │   ├── resources/
│   │   │   │   └── application.properties  # Supabase 配置
│   │   ├── test/
│   │   │   ├── java/com/threadclone/backend/controller/
│   │   │   │   ├── AdminControllerTest.java
│   │   │   │   ├── AdminIntegrationTest.java
│   │   │   │   ├── FollowControllerTest.java
│   │   │   │   ├── FollowIntegrationTest.java
│   │   │   │   ├── NotificationControllerTest.java
│   │   │   │   ├── NotificationIntegrationTest.java
│   │   │   │   ├── PostControllerTest.java
│   │   │   │   ├── PostIntegrationTest.java
│   │   │   │   ├── UserControllerTest.java
│   │   │   │   └── UserIntegrationTest.java
│   ├── pom.xml                             # Maven 依賴
│   └── database/
│       ├── database-schema.sql             # 資料庫結構（users, posts, replies, likes, follows）
│       └── notifications.sql              # 通知表和觸發器
├── frontend/                                  # React 前端專案
│   ├── src/
│   │   ├── components/
│   │   │   ├── Timeline.jsx               # 時間軸組件
│   │   │   └── Timeline.test.jsx          # 時間軸測試
│   │   ├── lib/
│   │   │   └── supabase.js                # Supabase 客戶端配置
│   │   ├── pages/
│   │   │   ├── AdminPanel.jsx             # 管理員面板
│   │   │   ├── AdminPanel.test.jsx
│   │   │   ├── Notifications.jsx          # 通知頁面
│   │   │   ├── Notifications.test.jsx
│   │   │   ├── Profile.jsx                # 個人資料頁面
│   │   │   ├── Profile.test.jsx
│   │   │   ├── Search.jsx                 # 搜索頁面
│   │   │   └── Search.test.jsx
│   │   ├── App.jsx                        # 路由配置
│   │   ├── index.jsx                      # 入口文件
│   │   └── index.css                      # 全局樣式（Tailwind CSS）
│   ├── public/
│   │   ├── index.html                     # HTML 模板
│   │   └── favicon.ico                    # 圖標
│   ├── package.json                       # Node.js 依賴
│   ├── tailwind.config.js                 # Tailwind CSS 配置
│   └── vite.config.js                     # Vite 配置
├── README.md                              # 專案說明
└── .gitignore                             # Git 忽略文件
```

```mermaid
graph TD
    A[用戶] -->|HTTP 請求| B[前端: React + Vite]
    B -->|渲染頁面| C[時間軸]
    B -->|渲染頁面| D[個人資料]
    B -->|渲染頁面| E[搜索]
    B -->|渲染頁面| F[通知]
    B -->|渲染頁面| G[管理員面板]

    B -->|API 呼叫| H[後端: Spring Boot]
    H -->|認證| I[Supabase 認證]
    H -->|資料操作| J[Supabase Postgrest]

    I -->|JWT 驗證| K[資料庫: PostgreSQL]
    J -->|SQL 查詢| K

    K --> L[users 表]
    K --> M[posts 表]
    K --> N[replies 表]
    K --> O[likes 表]
    K --> P[follows 表]
    K --> Q[notifications 表]

    subgraph 前端功能
        C -->|GET /api/timeline| H
        C -->|POST /api/posts/:post_id/replies| H
        C -->|POST/DELETE /api/posts/:post_id/like| H
        D -->|GET /api/users/:user_id| H
        D -->|PUT /api/users/me| H
        D -->|POST/DELETE /api/users/:user_id/follow| H
        E -->|GET /api/search| H
        F -->|GET /api/notifications| H
        G -->|GET /api/admin/posts| H
        G -->|DELETE /api/admin/posts/:post_id| H
    end

    subgraph 資料庫結構
        L -->|RLS: Users view/update own profile| K
        M -->|RLS: Users view public posts, Admins view all| K
        N -->|RLS: Users view public replies| K
        O -->|RLS: Users manage own likes| K
        P -->|RLS: Users manage own follows| K
        Q -->|RLS: Users view own notifications| K
        M -->|觸發器: handle_new_like| Q
        N -->|觸發器: handle_new_reply| Q
    end
```
