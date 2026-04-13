# Hermes Agent Skills 完整指南

> 共 78 個內建 Skills，依類別分類整理。
> 每個 Skill 包含：說明、前置需求、用法、結果範例。

---

## 目錄

1. [Apple 生態系](#1-apple-生態系)
2. [軟體開發](#2-軟體開發)
3. [GitHub 整合](#3-github-整合)
4. [創意工具](#4-創意工具)
5. [媒體處理](#5-媒體處理)
6. [研究與學術](#6-研究與學術)
7. [生產力工具](#7-生產力工具)
8. [MLOps / AI 模型](#8-mlops--ai-模型)
9. [MCP 整合](#9-mcp-整合)
10. [自主 AI Agent](#10-自主-ai-agent)
11. [其他工具](#11-其他工具)

---

## 1. Apple 生態系

### 1.1 apple-notes — Apple 備忘錄管理

| 項目 | 說明 |
|------|------|
| **功能** | 建立、檢視、搜尋、編輯 Apple Notes，透過 iCloud 同步所有裝置 |
| **前置需求** | macOS + `brew tap antoniorodr/memo && brew install antoniorodr/memo/memo` |
| **授權** | System Settings → Privacy → Automation（允許 Notes.app） |

**用法：**

```bash
# 列出所有筆記
memo notes

# 搜尋筆記
memo notes -s "會議紀錄"

# 依資料夾篩選
memo notes -f "工作"

# 建立筆記
memo notes -a "專案規劃"

# 編輯筆記（互動選擇）
memo notes -e

# 匯出為 Markdown/HTML
memo notes -ex
```

**結果範例：**
```
┌──────┬─────────────────┬──────────┬───────────────────┐
│  ID  │     Title       │  Folder  │    Modified       │
├──────┼─────────────────┼──────────┼───────────────────┤
│  1   │ 專案規劃         │ 工作     │ 2026-04-13 10:30  │
│  2   │ 買菜清單         │ 個人     │ 2026-04-12 18:00  │
└──────┴─────────────────┴──────────┴───────────────────┘
```

---

### 1.2 apple-reminders — Apple 提醒事項

| 項目 | 說明 |
|------|------|
| **功能** | 管理 Apple Reminders，建立/完成/刪除待辦，同步 iOS |
| **前置需求** | macOS + `brew install steipete/tap/remindctl` |
| **授權** | `remindctl authorize` 授權 Reminders 存取 |

**用法：**

```bash
# 查看今天的提醒
remindctl today

# 查看本週 / 逾期 / 全部
remindctl week
remindctl overdue
remindctl all

# 建立提醒
remindctl add "買牛奶"
remindctl add --title "打電話給客戶" --list 工作 --due tomorrow
remindctl add --title "交報告" --due "2026-04-20 09:00"

# 管理清單
remindctl list                    # 列出所有清單
remindctl list 工作 --create      # 建立新清單

# 完成提醒
remindctl complete 1 2 3

# JSON 輸出（方便程式處理）
remindctl today --json
```

**結果範例：**
```
Today's Reminders:
  1. [○] 買牛奶                    (Personal)
  2. [○] 打電話給客戶    due 14:00  (工作)
  3. [✓] 回覆 email               (工作)  ✓ completed
```

---

### 1.3 imessage — iMessage / SMS 訊息

| 項目 | 說明 |
|------|------|
| **功能** | 透過 macOS Messages.app 發送/讀取 iMessage 和 SMS |
| **前置需求** | macOS + Messages.app 已登入 + `brew install steipete/tap/imsg` |
| **授權** | System Settings → Privacy → Full Disk Access（終端機） |

**用法：**

```bash
# 列出最近對話
imsg chats --limit 10 --json

# 查看對話歷史
imsg history --chat-id 1 --limit 20 --json

# 發送訊息
imsg send --to "+886912345678" --text "我晚點到"

# 附加檔案
imsg send --to "+886912345678" --text "看一下這個" --file /path/to/image.jpg

# 強制使用 iMessage 或 SMS
imsg send --to "+886912345678" --text "Hi" --service imessage

# 監聽新訊息
imsg watch --chat-id 1 --attachments
```

**結果範例：**
```json
[
  {
    "chatId": 1,
    "displayName": "媽媽",
    "lastMessage": "晚餐吃什麼？",
    "date": "2026-04-13T12:30:00Z"
  }
]
```

> **注意：** 發送前 Hermes 會先確認收件人和內容，不會自動發送。

---

### 1.4 findmy — 尋找我的裝置 / AirTag

| 項目 | 說明 |
|------|------|
| **功能** | 透過 FindMy.app 追蹤 Apple 裝置和 AirTag 位置 |
| **前置需求** | macOS + iCloud 已登入 + Screen Recording 權限 |
| **選配** | `brew install steipete/tap/peekaboo`（更可靠的 UI 自動化） |

**用法：**

```bash
# 方法 1：AppleScript + 截圖
osascript -e 'tell application "FindMy" to activate'
sleep 3
screencapture -w -o /tmp/findmy.png
# → Hermes 用 vision 分析截圖讀取位置

# 方法 2：Peekaboo（推薦）
osascript -e 'tell application "FindMy" to activate'
sleep 3
peekaboo see --app "FindMy" --annotate --path /tmp/findmy-ui.png
peekaboo click --on B3 --app "FindMy"   # 點選特定裝置
peekaboo image --app "FindMy" --path /tmp/findmy-detail.png

# 切換到裝置/物品頁籤
osascript -e 'tell application "System Events"
    tell process "FindMy"
        click button "Devices" of toolbar 1 of window 1
    end tell
end tell'
```

**結果範例：**
```
裝置位置：
  📱 iPhone 15 Pro — 台北市信義區松仁路 100 號（5 分鐘前更新）
  🎧 AirPods Pro — 目前位置：與 iPhone 同位置
  🏷️ AirTag "鑰匙" — 台北市大安區忠孝東路 200 號（30 分鐘前更新）
```

> **限制：** AirTag 需要 FindMy 視窗保持前景才會持續更新位置。

---

## 2. 軟體開發

### 2.1 plan — 計畫模式

| 項目 | 說明 |
|------|------|
| **功能** | 進入純規劃模式，只分析和討論，不修改任何程式碼 |
| **前置需求** | 無 |

**用法：**

在 Hermes 對話中說：
```
幫我規劃一個用戶認證系統的架構
```

**結果範例：**
```
📋 計畫：用戶認證系統

1. 技術選型
   - JWT + Refresh Token 機制
   - bcrypt 密碼雜湊
   
2. API 端點設計
   - POST /auth/register
   - POST /auth/login
   - POST /auth/refresh
   
3. 資料庫 Schema
   - users 表：id, email, password_hash, created_at
   - refresh_tokens 表：id, user_id, token, expires_at

（不會修改任何檔案，只提供分析）
```

---

### 2.2 writing-plans — 撰寫實作計畫

| 項目 | 說明 |
|------|------|
| **功能** | 撰寫詳細的實作計畫，包含分階段任務、檔案清單、依賴關係 |
| **前置需求** | 無 |

**用法：**

```
寫一份把 REST API 遷移到 GraphQL 的實作計畫
```

**結果範例：**
```
# 實作計畫：REST → GraphQL 遷移

## Phase 1：基礎設施（1-2 天）
  - [ ] 安裝 Apollo Server 依賴
  - [ ] 建立 schema.graphql
  - [ ] 設定 GraphQL endpoint /graphql

## Phase 2：Schema 定義（2-3 天）
  - [ ] 定義 User type
  - [ ] 定義 Query / Mutation
  - [ ] 撰寫 resolvers

## Phase 3：遷移端點（3-5 天）
  - [ ] 遷移 GET /users → Query.users
  - [ ] 遷移 POST /users → Mutation.createUser
  ...

## 風險評估
  - 前端需同步修改 API 呼叫
  - 需要保留 REST 端點過渡期
```

---

### 2.3 test-driven-development — TDD 開發

| 項目 | 說明 |
|------|------|
| **功能** | 嚴格的 RED-GREEN-REFACTOR TDD 循環，先寫測試再寫實作 |
| **前置需求** | 專案已有測試框架（Jest, pytest, etc.） |

**用法：**

```
用 TDD 幫我實作一個購物車的折扣計算功能
```

**流程：**
```
🔴 RED — 先寫失敗的測試：
   test("滿 1000 打 9 折", () => {
     const cart = new Cart([{ price: 600 }, { price: 500 }]);
     expect(cart.total()).toBe(990);
   });
   → 執行測試 → ❌ FAIL

🟢 GREEN — 寫最少的程式碼讓測試通過：
   total() {
     const sum = this.items.reduce((s, i) => s + i.price, 0);
     return sum >= 1000 ? sum * 0.9 : sum;
   }
   → 執行測試 → ✅ PASS

🔵 REFACTOR — 重構但保持測試通過：
   - 抽取 discount 策略模式
   → 執行測試 → ✅ PASS
```

---

### 2.4 systematic-debugging — 系統化除錯

| 項目 | 說明 |
|------|------|
| **功能** | 四階段根因分析：收集證據 → 形成假設 → 驗證 → 修復 |
| **前置需求** | 無 |

**用法：**

```
用戶登入後一直被踢回登入頁，幫我除錯
```

**流程：**
```
📌 Phase 1：收集證據
  - 檢查瀏覽器 console 錯誤
  - 檢查 Network tab 的 API 回應
  - 檢查 cookie/localStorage 中的 token

📌 Phase 2：形成假設
  假設 A：Token 過期時間設定太短
  假設 B：Cookie SameSite 屬性導致跨域失效
  假設 C：Auth middleware 路由配置錯誤

📌 Phase 3：逐一驗證
  ✗ 假設 A：Token TTL 為 1 小時，正常
  ✓ 假設 B：SameSite=Strict 導致跨 subdomain 時 cookie 未攜帶

📌 Phase 4：修復
  → 將 SameSite 改為 Lax，加入 Domain 設定
```

---

### 2.5 subagent-driven-development — 多 Agent 協作開發

| 項目 | 說明 |
|------|------|
| **功能** | 將任務分派給多個子 agent 並行執行，兩階段 review |
| **前置需求** | 無 |

**用法：**

```
幫我同時重構 auth 模組和 payment 模組
```

**流程：**
```
🤖 主 Agent：拆分任務
  ├── 子 Agent A → 重構 auth 模組
  ├── 子 Agent B → 重構 payment 模組
  └── 主 Agent 等待兩者完成

📝 Review 階段 1：各子 agent 自我檢查
📝 Review 階段 2：主 agent 整合審查，確認無衝突
✅ 合併結果
```

---

### 2.6 requesting-code-review — 程式碼審查

| 項目 | 說明 |
|------|------|
| **功能** | 提交前的驗證流程：lint、測試、安全掃描、code review |
| **前置需求** | 無 |

**用法：**

```
幫我 review 目前的修改
```

**結果範例：**
```
📋 Code Review 報告

✅ Lint：無問題
✅ 測試：12/12 通過
⚠️ 安全性：第 45 行有潛在 SQL injection 風險
   → 建議使用 parameterized query
⚠️ 效能：第 78 行 N+1 query 問題
   → 建議使用 eager loading

整體評分：7/10 — 修復安全性問題後可合併
```

---

## 3. GitHub 整合

### 3.1 github-auth — GitHub 認證設定

| 項目 | 說明 |
|------|------|
| **功能** | 設定 GitHub 認證（Personal Access Token、SSH、gh CLI） |
| **前置需求** | Git 已安裝 |

**用法：**

```bash
# 方法 1：Git credential（不需額外安裝）
git config --global credential.helper store
# 第一次 push 時輸入 username + PAT

# 方法 2：SSH key
ssh-keygen -t ed25519 -C "your@email.com"
# 將 ~/.ssh/id_ed25519.pub 加到 GitHub Settings → SSH Keys

# 方法 3：gh CLI（推薦）
brew install gh
gh auth login
```

---

### 3.2 github-repo-management — Repo 管理

| 項目 | 說明 |
|------|------|
| **功能** | Clone、建立、Fork repo，管理 release、workflows |
| **前置需求** | `gh` CLI 或 Git + PAT |

**用法：**

```bash
# Clone repo
gh repo clone owner/repo

# 建立新 repo
gh repo create my-project --public --clone

# Fork
gh repo fork owner/repo --clone

# 建立 Release
gh release create v1.0.0 --title "v1.0.0" --notes "Initial release"

# 查看 CI workflows
gh workflow list
gh run list
```

---

### 3.3 github-pr-workflow — PR 工作流

| 項目 | 說明 |
|------|------|
| **功能** | 完整 PR 生命週期：建立分支、commit、push、CI、merge |
| **前置需求** | `gh` CLI 已認證 |

**用法：**

```bash
# 建立功能分支
git checkout -b feature/add-login

# ... 修改程式碼 ...

# 建立 PR
gh pr create --title "Add login feature" --body "## Summary\n- Added login page"

# 查看 PR 狀態
gh pr status
gh pr checks 123

# Merge PR
gh pr merge 123 --squash
```

**結果範例：**
```
Creating pull request for feature/add-login into main

https://github.com/owner/repo/pull/123

✓ CI checks passed
✓ 1 approval received
→ Ready to merge
```

---

### 3.4 github-code-review — Code Review

| 項目 | 說明 |
|------|------|
| **功能** | 對本地修改或 PR 進行 code review |
| **前置需求** | `gh` CLI |

**用法：**

```bash
# 查看 PR diff
gh pr diff 123

# 提交 review
gh pr review 123 --approve --body "LGTM!"
gh pr review 123 --request-changes --body "請修正第 45 行"

# 加入行內 comment
gh pr comment 123 --body "建議用 const 取代 let"
```

---

### 3.5 github-issues — Issue 管理

| 項目 | 說明 |
|------|------|
| **功能** | 建立、管理、分類 Issues |
| **前置需求** | `gh` CLI |

**用法：**

```bash
# 列出 issues
gh issue list
gh issue list --label bug --state open

# 建立 issue
gh issue create --title "登入頁 404 錯誤" --body "步驟：..." --label bug

# 查看 issue
gh issue view 42

# 關閉 issue
gh issue close 42 --comment "已在 PR #50 修復"
```

---

### 3.6 codebase-inspection — 程式碼統計

| 項目 | 說明 |
|------|------|
| **功能** | 使用 pygount 分析程式碼行數、語言分佈 |
| **前置需求** | `pip install pygount` |

**用法：**

```bash
pygount --format=summary .
```

**結果範例：**
```
Language    Files    Code    Comment    Blank    Total
─────────────────────────────────────────────────────
TypeScript    45    3,200      480       620    4,300
Python        12      890      120       180    1,190
JSON           8      340        0        10      350
Markdown       5      120        0        40      160
─────────────────────────────────────────────────────
Total         70    4,550      600       850    6,000
```

---

## 4. 創意工具

### 4.1 ascii-art — ASCII 藝術

| 項目 | 說明 |
|------|------|
| **功能** | 用 pyfiglet（571 種字體）、cowsay、boxes、toilet 生成 ASCII 藝術 |
| **前置需求** | `pip install pyfiglet` / `brew install cowsay boxes toilet` |

**用法：**

```bash
# 文字轉 ASCII
pyfiglet "Hello"

# cowsay
echo "Hermes Agent!" | cowsay

# 圖片轉 ASCII
ascii-image-converter image.png --color
```

**結果範例：**
```
 _   _      _ _
| | | | ___| | | ___
| |_| |/ _ \ | |/ _ \
|  _  |  __/ | | (_) |
|_| |_|\___|_|_|\___/
```

---

### 4.2 ascii-video — ASCII 影片

| 項目 | 說明 |
|------|------|
| **功能** | 將影片/音訊/圖片轉換成彩色 ASCII 動畫（MP4、GIF） |
| **前置需求** | Python + ffmpeg |

**支援模式：**
- 影片 → ASCII 影片
- 音訊反應式視覺化
- 生成式 ASCII 動畫
- 文字/歌詞疊加
- 即時終端機渲染

---

### 4.3 excalidraw — 手繪風格圖表

| 項目 | 說明 |
|------|------|
| **功能** | 生成 .excalidraw JSON 檔，可在 excalidraw.com 開啟 |
| **前置需求** | 無 |

**支援圖表類型：**
- 架構圖（Architecture Diagram）
- 流程圖（Flowchart）
- 序列圖（Sequence Diagram）
- 概念圖（Concept Map）

**用法：**

```
幫我畫一個微服務架構圖
```

**結果：** 生成 `.excalidraw` 檔案 → 用瀏覽器開啟 excalidraw.com 匯入即可看到手繪風格圖表。

---

### 4.4 ideation — 創意發想

| 項目 | 說明 |
|------|------|
| **功能** | 腦力激盪、創意發想、概念探索 |
| **前置需求** | 無 |

**用法：**

```
幫我想一些 SaaS side project 的點子
```

---

### 4.5 manim-video — 數學動畫影片

| 項目 | 說明 |
|------|------|
| **功能** | 用 Manim 製作 3Blue1Brown 風格的數學/技術動畫 |
| **前置需求** | `pip install manim` + LaTeX + ffmpeg |

**用法：**

```
用 Manim 製作一個解釋快速排序的動畫
```

**結果：** 生成 MP4 影片，展示排序過程的視覺化動畫。

```python
# Hermes 會自動生成類似的 Manim 程式碼
class QuickSort(Scene):
    def construct(self):
        bars = VGroup(*[Rectangle(height=h) for h in [3,1,4,1,5]])
        self.play(Create(bars))
        # ... 排序動畫邏輯
```

---

### 4.6 p5js — 互動視覺藝術

| 項目 | 說明 |
|------|------|
| **功能** | 用 p5.js 建立瀏覽器端互動藝術、生成式視覺、資料視覺化 |
| **前置需求** | 瀏覽器 |

**支援：** 生成式藝術、資料視覺化、互動體驗、3D 場景、音訊反應視覺、動態圖形

**結果：** 生成 HTML 檔案，用瀏覽器開啟即可互動。

---

### 4.7 popular-web-designs — 知名網站設計模板

| 項目 | 說明 |
|------|------|
| **功能** | 54 套來自真實網站的設計系統（Stripe、Linear、Vercel、Notion、Airbnb 等） |
| **前置需求** | 無 |

**用法：**

```
用 Stripe 的設計風格幫我做一個定價頁面
```

**結果：** 生成符合 Stripe 視覺風格的 HTML/CSS 頁面。

---

### 4.8 songwriting-and-ai-music — AI 音樂創作

| 項目 | 說明 |
|------|------|
| **功能** | 歌曲創作指導、AI 音樂生成提示詞（Suno 為主）、歌詞寫作技巧 |
| **前置需求** | Suno 帳號（選配） |

**用法：**

```
幫我寫一首關於程式設計師生活的歌詞，適合用 Suno 生成
```

**結果範例：**
```
🎵 標題：Debug 人生

[Verse 1]
凌晨三點的螢幕光
Stack trace 像是一道牆
console.log 一行一行
找不到那個 bug 在哪裡藏

[Chorus]
Git push, git pull, 每天在 loop
Coffee driven development
...

Suno 提示詞：indie rock, upbeat, male vocals, coding theme
```

---

## 5. 媒體處理

### 5.1 gif-search — GIF 搜尋

| 項目 | 說明 |
|------|------|
| **功能** | 從 Tenor 搜尋並下載 GIF |
| **前置需求** | `curl` + `jq` + `TENOR_API_KEY` 環境變數 |

**用法：**

```bash
curl -s "https://tenor.googleapis.com/v2/search?q=coding&key=$TENOR_API_KEY&limit=5" | jq '.results[].media_formats.gif.url'
```

---

### 5.2 heartmula — AI 音樂生成

| 項目 | 說明 |
|------|------|
| **功能** | 開源音樂生成模型（類似 Suno），從歌詞 + 標籤生成完整歌曲 |
| **前置需求** | GPU（建議 24GB+ VRAM）、Python |

**用法：**

```
用 HeartMuLa 從歌詞生成一首歌
```

---

### 5.3 songsee — 音訊視覺化

| 項目 | 說明 |
|------|------|
| **功能** | 生成頻譜圖和音訊特徵視覺化（mel、chroma、MFCC、tempogram） |
| **前置需求** | Go 語言環境 |

**用法：**

```bash
songsee spectrogram --input song.mp3 --output spec.png
songsee mel --input song.mp3 --output mel.png
```

---

### 5.4 youtube-content — YouTube 影片摘要

| 項目 | 說明 |
|------|------|
| **功能** | 擷取 YouTube 影片字幕，轉換為章節、摘要、部落格文章 |
| **前置需求** | `pip install youtube-transcript-api` |

**用法：**

```
幫我摘要這個 YouTube 影片：https://youtube.com/watch?v=xxx
```

**結果範例：**
```
📺 影片摘要：「React Server Components 完整教學」

⏱ 章節：
  00:00 - 簡介
  02:30 - 什麼是 RSC
  08:15 - 實作範例
  15:00 - 效能比較
  20:00 - 總結

📝 重點摘要：
  1. RSC 讓元件在伺服器端渲染，減少 client bundle size
  2. 可直接在元件中存取資料庫
  3. 與傳統 SSR 的差異在於...
```

---

## 6. 研究與學術

### 6.1 arxiv — 學術論文搜尋

| 項目 | 說明 |
|------|------|
| **功能** | 搜尋 arXiv 論文、下載 PDF、提取摘要 |
| **前置需求** | `pip install arxiv` |

**用法：**

```
搜尋最近關於 RAG 的論文
```

**結果範例：**
```
📄 arXiv 搜尋結果：RAG (Retrieval-Augmented Generation)

1. [2026.04123] "Adaptive RAG with Dynamic Chunking"
   作者：Zhang et al.
   摘要：We propose a novel approach to...
   PDF：https://arxiv.org/pdf/2026.04123

2. [2026.03987] "RAG vs Fine-tuning: A Comprehensive Benchmark"
   ...
```

---

### 6.2 blogwatcher — 部落格監控

| 項目 | 說明 |
|------|------|
| **功能** | 監控指定部落格/網站的更新 |
| **前置需求** | 無 |

**用法：**

```
幫我監控 OpenAI 和 Anthropic 的部落格更新
```

---

### 6.3 llm-wiki — LLM 知識庫

| 項目 | 說明 |
|------|------|
| **功能** | LLM 相關知識百科，涵蓋架構、訓練技術、評估方法 |
| **前置需求** | 無 |

**用法：**

```
解釋 Flash Attention 的原理
什麼是 RLHF？
比較 LoRA 和 QLoRA 的差異
```

---

### 6.4 polymarket — 預測市場

| 項目 | 說明 |
|------|------|
| **功能** | 查詢 Polymarket 預測市場的事件和機率 |
| **前置需求** | 無 |

**用法：**

```
查詢 Polymarket 上關於 AI 的預測
```

---

### 6.5 research-paper-writing — 論文撰寫

| 項目 | 說明 |
|------|------|
| **功能** | 端對端論文撰寫流程（NeurIPS、ICML、ICLR 等頂會） |
| **前置需求** | LaTeX 環境 |

**涵蓋階段：**
1. 專案設定
2. 文獻回顧
3. 實驗設計
4. 論文撰寫
5. 自我審查
6. 投稿流程

---

## 7. 生產力工具

### 7.1 obsidian — Obsidian 筆記管理

| 項目 | 說明 |
|------|------|
| **功能** | 讀取、搜尋、建立 Obsidian vault 中的筆記 |
| **前置需求** | Obsidian 已安裝，設定 `OBSIDIAN_VAULT_PATH` 環境變數 |

**用法：**

```bash
VAULT="${OBSIDIAN_VAULT_PATH:-$HOME/Documents/Obsidian Vault}"

# 搜尋筆記
grep -rli "keyword" "$VAULT" --include="*.md"

# 建立筆記
cat > "$VAULT/新筆記.md" << 'EOF'
# 標題
內容，支援 [[Wikilinks]] 語法連結其他筆記。
EOF

# 附加內容
echo "新增內容" >> "$VAULT/既有筆記.md"
```

---

### 7.2 notion — Notion 整合

| 項目 | 說明 |
|------|------|
| **功能** | 管理 Notion 頁面和資料庫 |
| **前置需求** | Notion API Token |

**用法：**

```
在 Notion 建立一個新的專案追蹤頁面
列出 Notion 資料庫中的待辦事項
```

---

### 7.3 linear — Linear 專案管理

| 項目 | 說明 |
|------|------|
| **功能** | 管理 Linear 的 issues、projects、cycles |
| **前置需求** | Linear API Key |

**用法：**

```
列出 Linear 中目前 sprint 的所有 issues
建立一個新的 bug issue
```

---

### 7.4 nano-pdf — PDF 處理

| 項目 | 說明 |
|------|------|
| **功能** | 讀取、分析 PDF 內容 |
| **前置需求** | 無 |

**用法：**

```
幫我讀取並摘要這份 PDF：/path/to/document.pdf
```

---

### 7.5 powerpoint — 簡報製作

| 項目 | 說明 |
|------|------|
| **功能** | 用 python-pptx 建立 PowerPoint 簡報 |
| **前置需求** | `pip install python-pptx` |

**用法：**

```
幫我做一份 10 頁的產品介紹簡報
```

**結果：** 生成 `.pptx` 檔案，包含標題頁、內容頁、圖表頁等。

---

### 7.6 google-workspace — Google 文件/試算表

| 項目 | 說明 |
|------|------|
| **功能** | 管理 Google Docs、Sheets、Drive |
| **前置需求** | Google API 憑證 |

**用法：**

```
建立一個 Google Sheet 來追蹤專案進度
讀取 Google Doc 的內容
```

---

### 7.7 ocr-and-documents — OCR 文件辨識

| 項目 | 說明 |
|------|------|
| **功能** | 圖片/掃描件 OCR 文字辨識 |
| **前置需求** | 依方法而定（Tesseract 或 vision API） |

**用法：**

```
辨識這張圖片中的文字：/path/to/scan.png
```

---

### 7.8 himalaya — Email 管理

| 項目 | 說明 |
|------|------|
| **功能** | CLI Email 客戶端（IMAP/SMTP），收發、搜尋、管理郵件 |
| **前置需求** | `brew install himalaya` + 設定 `~/.config/himalaya/config.toml` |

**用法：**

```bash
# 列出收件匣
himalaya envelope list

# 讀取郵件
himalaya message read 42

# 搜尋郵件
himalaya envelope list from john@example.com subject meeting

# 發送郵件
cat << 'EOF' | himalaya template send
From: you@example.com
To: recipient@example.com
Subject: 會議通知

明天下午 3 點開會。
EOF

# 回覆郵件
himalaya template reply 42 | himalaya template send

# 下載附件
himalaya attachment download 42 --dir ~/Downloads

# 多帳號切換
himalaya --account work envelope list
```

---

## 8. MLOps / AI 模型

### 8.1 huggingface-hub — HuggingFace 模型管理

| 項目 | 說明 |
|------|------|
| **功能** | 搜尋、下載、上傳模型和 datasets；SQL 查詢 datasets |
| **前置需求** | `HF_TOKEN` 環境變數 |

**用法：**

```bash
# 下載模型
hf download meta-llama/Llama-3-8B

# 上傳模型
hf upload my-model ./output

# SQL 查詢 dataset
hf datasets sql "SELECT * FROM dataset WHERE label='positive' LIMIT 10"
```

---

### 8.2 fine-tuning-with-trl — TRL 微調

| 項目 | 說明 |
|------|------|
| **功能** | 用 HuggingFace TRL 進行模型微調（SFT、DPO、PPO） |
| **前置需求** | GPU + `pip install trl` |

---

### 8.3 unsloth — Unsloth 快速微調

| 項目 | 說明 |
|------|------|
| **功能** | 2-5x 加速的 LLM 微調，減少 80% 記憶體 |
| **前置需求** | GPU + `pip install unsloth` |

---

### 8.4 axolotl — Axolotl 微調框架

| 項目 | 說明 |
|------|------|
| **功能** | 基於 YAML 設定的 LLM 微調工具 |
| **前置需求** | GPU + Docker 或本地安裝 |

---

### 8.5 grpo-rl-training — GRPO 強化學習訓練

| 項目 | 說明 |
|------|------|
| **功能** | Group Relative Policy Optimization 訓練 |
| **前置需求** | GPU + TRL |

---

### 8.6 peft-fine-tuning — PEFT 參數高效微調

| 項目 | 說明 |
|------|------|
| **功能** | LoRA、QLoRA、Prefix Tuning 等參數高效微調方法 |
| **前置需求** | GPU + `pip install peft` |

---

### 8.7 pytorch-fsdp — PyTorch FSDP 分散式訓練

| 項目 | 說明 |
|------|------|
| **功能** | Fully Sharded Data Parallel 多 GPU 訓練 |
| **前置需求** | 多 GPU 環境 |

---

### 8.8 stable-diffusion-image-generation — Stable Diffusion 圖片生成

| 項目 | 說明 |
|------|------|
| **功能** | 用 Stable Diffusion 從文字生成圖片 |
| **前置需求** | GPU + `pip install diffusers` |

---

### 8.9 whisper — 語音轉文字

| 項目 | 說明 |
|------|------|
| **功能** | OpenAI Whisper 語音辨識，支援多語言 |
| **前置需求** | `pip install openai-whisper` |

**用法：**

```bash
whisper audio.mp3 --language zh --model medium
```

**結果範例：**
```
[00:00.000 --> 00:03.500] 大家好，今天要介紹的是
[00:03.500 --> 00:07.200] Hermes Agent 的完整功能
```

---

### 8.10 serving-llms-vllm — vLLM 模型部署

| 項目 | 說明 |
|------|------|
| **功能** | 用 vLLM 部署高效能 LLM 推理服務 |
| **前置需求** | GPU + `pip install vllm` |

---

### 8.11 dspy — DSPy 程式化 LLM

| 項目 | 說明 |
|------|------|
| **功能** | 用 DSPy 框架建構可最佳化的 LLM pipeline |
| **前置需求** | `pip install dspy-ai` |

---

### 8.12 clip — CLIP 圖文匹配

| 項目 | 說明 |
|------|------|
| **功能** | OpenAI CLIP 模型，圖片-文字配對和零樣本分類 |
| **前置需求** | `pip install clip` |

---

### 8.13 audiocraft-audio-generation — AudioCraft 音訊生成

| 項目 | 說明 |
|------|------|
| **功能** | Meta AudioCraft（MusicGen / AudioGen）音樂和音效生成 |
| **前置需求** | GPU + `pip install audiocraft` |

---

### 8.14 segment-anything-model — SAM 影像分割

| 項目 | 說明 |
|------|------|
| **功能** | Meta SAM 模型，通用影像分割 |
| **前置需求** | GPU + `pip install segment-anything` |

---

### 8.15 modal-serverless-gpu — Modal 無伺服器 GPU

| 項目 | 說明 |
|------|------|
| **功能** | 在 Modal 平台上執行 GPU 任務（無需自備 GPU） |
| **前置需求** | Modal 帳號 + `pip install modal` |

---

### 8.16 guidance — Guidance 結構化輸出

| 項目 | 說明 |
|------|------|
| **功能** | 用 Guidance 框架控制 LLM 的結構化輸出 |
| **前置需求** | `pip install guidance` |

---

### 8.17 gguf-quantization — GGUF 量化

| 項目 | 說明 |
|------|------|
| **功能** | 將模型量化為 GGUF 格式（用於 llama.cpp） |
| **前置需求** | llama.cpp |

---

### 8.18 llama-cpp — llama.cpp 本地推理

| 項目 | 說明 |
|------|------|
| **功能** | 用 llama.cpp 在 CPU/GPU 上本地執行 LLM |
| **前置需求** | `brew install llama.cpp` 或從原始碼編譯 |

---

### 8.19 outlines — Outlines 結構化生成

| 項目 | 說明 |
|------|------|
| **功能** | 用正則表達式或 JSON schema 約束 LLM 輸出 |
| **前置需求** | `pip install outlines` |

---

### 8.20 evaluating-llms-harness — LLM 評估

| 項目 | 說明 |
|------|------|
| **功能** | 用 lm-evaluation-harness 評估模型性能 |
| **前置需求** | `pip install lm-eval` |

---

### 8.21 weights-and-biases — W&B 實驗追蹤

| 項目 | 說明 |
|------|------|
| **功能** | 用 Weights & Biases 追蹤訓練實驗、視覺化指標 |
| **前置需求** | `pip install wandb` + `WANDB_API_KEY` |

---

### 8.22 obliteratus — Obliteratus

| 項目 | 說明 |
|------|------|
| **功能** | LLM 模型壓縮工具 |
| **前置需求** | 依工具而定 |

---

## 9. MCP 整合

### 9.1 mcporter — MCP 工具發現與呼叫

| 項目 | 說明 |
|------|------|
| **功能** | 發現並直接呼叫 MCP 伺服器/工具，無需預設定 |
| **前置需求** | Node.js（透過 npx 執行） |

**用法：**

```bash
# 列出可用 MCP 工具
mcporter list

# 呼叫特定工具
mcporter call <server.tool> --args '{"key": "value"}'
```

---

### 9.2 native-mcp — 原生 MCP 客戶端

| 項目 | 說明 |
|------|------|
| **功能** | 內建 MCP 客戶端，啟動時自動連接 MCP 伺服器並載入工具 |
| **前置需求** | `pip install mcp` + 在 config.yaml 設定 `mcp_servers` |

**設定範例（~/.hermes/config.yaml）：**

```yaml
mcp_servers:
  - name: "filesystem"
    command: "npx"
    args: ["-y", "@modelcontextprotocol/server-filesystem", "/path"]
```

---

## 10. 自主 AI Agent

### 10.1 claude-code — Claude Code 委派

| 項目 | 說明 |
|------|------|
| **功能** | 將程式碼任務委派給 Claude Code CLI |
| **前置需求** | `npm install -g @anthropic-ai/claude-code` + 已認證 |

**用法：**

```bash
# 一次性任務（print 模式）
claude -p "幫我重構 auth middleware"

# 互動模式（需要 tmux）
tmux new-session -d -s claude
tmux send-keys -t claude "claude" Enter
```

---

### 10.2 codex — OpenAI Codex 委派

| 項目 | 說明 |
|------|------|
| **功能** | 將任務委派給 OpenAI Codex CLI |
| **前置需求** | `npm install -g @openai/codex` + 認證 |

**用法：**

```bash
codex exec "新增 dark mode 支援"
codex exec "修復所有 TypeScript 錯誤" --full-auto
```

---

### 10.3 hermes-agent — Hermes 自我委派

| 項目 | 說明 |
|------|------|
| **功能** | 啟動另一個 Hermes 實例處理子任務 |
| **前置需求** | Hermes 已安裝 |

---

### 10.4 opencode — OpenCode 委派

| 項目 | 說明 |
|------|------|
| **功能** | 委派給 OpenCode（provider 無關的程式碼 agent） |
| **前置需求** | `npm i -g opencode-ai@latest` |

**用法：**

```bash
opencode run "重構資料庫層"
opencode run "加入單元測試" --model claude-sonnet-4-6
```

---

## 11. 其他工具

### 11.1 webhook-subscriptions — Webhook 事件訂閱

| 項目 | 說明 |
|------|------|
| **功能** | 建立事件驅動的 webhook（GitHub、Stripe、CI/CD 觸發 Hermes） |
| **前置需求** | `hermes gateway setup` |

**用法：**

```bash
hermes webhook subscribe
hermes webhook list
```

---

### 11.2 find-nearby — 附近地點搜尋

| 項目 | 說明 |
|------|------|
| **功能** | 用 OpenStreetMap 搜尋附近餐廳、咖啡廳、藥局等 |
| **前置需求** | 無（不需 API key） |

**用法：**

```bash
python3 find_nearby.py --near "台北101" --type restaurant --radius 500
```

**結果範例：**
```
📍 台北101 附近的餐廳（500m 內）：
  1. 鼎泰豐（信義店）— 120m
  2. 欣葉台菜 — 230m
  3. 添好運 — 340m
```

---

### 11.3 jupyter-live-kernel — Jupyter 即時核心

| 項目 | 說明 |
|------|------|
| **功能** | 透過 Jupyter kernel 執行 Python，保持狀態的 REPL |
| **前置需求** | `uv` + JupyterLab 執行中 |

**用法：**

```
幫我做一個資料分析：讀取 sales.csv，畫出月營收趨勢圖
```

**流程：** Hermes 會在 Jupyter kernel 中逐步執行 pandas + matplotlib 程式碼，保持變數狀態。

---

### 11.4 godmode — LLM 安全測試

| 項目 | 說明 |
|------|------|
| **功能** | LLM 紅隊測試工具（jailbreak 技術研究） |
| **前置需求** | OpenRouter API Key |

> **注意：** 僅限授權的安全測試和研究用途。

---

### 11.5 openhue — Philips Hue 智慧燈控制

| 項目 | 說明 |
|------|------|
| **功能** | 控制 Philips Hue 燈光和場景 |
| **前置需求** | Hue Bridge 在區網內 |

**用法：**

```bash
openhue set light "客廳" --on --brightness 50 --color warm
openhue set light "臥室" --off
```

---

### 11.6 xitter — X/Twitter 社群管理

| 項目 | 說明 |
|------|------|
| **功能** | 發文、搜尋、按讚、轉推、書籤 |
| **前置需求** | X API 的 5 組憑證（API Key, Secret, Access Token, Secret, Bearer） |

**用法：**

```bash
x-cli tweet post "Hello from Hermes Agent!"
x-cli user get elonmusk
x-cli me mentions
```

---

### 11.7 dogfood — Hermes 自我測試

| 項目 | 說明 |
|------|------|
| **功能** | Hermes Agent 的內部測試/品質保證工具 |
| **前置需求** | 無 |

---

## 附錄：快速參考表

### 不需要任何 API Key 即可使用的 Skills

| Skill | 功能 |
|-------|------|
| apple-notes | Apple 備忘錄 |
| apple-reminders | Apple 提醒事項 |
| imessage | iMessage |
| findmy | 尋找裝置 |
| plan / writing-plans | 開發規劃 |
| test-driven-development | TDD |
| systematic-debugging | 除錯 |
| requesting-code-review | Code Review |
| excalidraw | 圖表繪製 |
| ascii-art | ASCII 藝術 |
| p5js | 互動藝術 |
| popular-web-designs | 設計模板 |
| nano-pdf | PDF 處理 |
| find-nearby | 附近搜尋 |
| obsidian | 筆記管理 |
| powerpoint | 簡報製作 |

### 需要額外安裝 CLI 工具的 Skills

| Skill | 需安裝 |
|-------|--------|
| apple-notes | `memo` |
| apple-reminders | `remindctl` |
| imessage | `imsg` |
| himalaya | `himalaya` |
| codebase-inspection | `pygount` |
| whisper | `openai-whisper` |
| songsee | Go 語言環境 |

### 需要 API Key 的 Skills

| Skill | 需要 |
|-------|------|
| gif-search | `TENOR_API_KEY` |
| huggingface-hub | `HF_TOKEN` |
| weights-and-biases | `WANDB_API_KEY` |
| xitter | X API 5 組憑證 |
| notion | Notion API Token |
| linear | Linear API Key |
| google-workspace | Google API 憑證 |
| godmode | `OPENROUTER_API_KEY` |

---

> 📅 文件產生日期：2026-04-13
> 📦 Hermes Agent Skills 總數：78 個內建 Skills
