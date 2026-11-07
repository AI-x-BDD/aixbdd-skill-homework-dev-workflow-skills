# <repo 名稱>

> 用這份 README 當範例，改成你自己的版本。repo 名稱請當成自己的作品集來命名。各節都可以改寫或刪除，只有文末的官方區塊要保留。

## 關於這個 repo

我在這個 repo 整理了 <主題> 相關的 skills，用來解決 <開發過程中遇到的問題>。

<!-- 範例：我在這個 repo 整理了從撰寫規格到 TDD 實作的 skills，做法來自水球軟體學院《AI x BDD》課程，讓 AI 產出的程式碼能逐條對應回規格。 -->

## 結構說明

| 路徑 | 內容 |
|---|---|
| `.claude/skills/` | Claude Code 格式的 skills |
| `.agents/skills/` | Codex／.agents 格式的 skills |
| `VERSION` | template 版本 |

> 以上路徑是《AI x BDD》課程平台抓取與展示 skills 的固定位置，請勿更名、搬移或刪除。

## 道館作業

<!-- 若本 repo 不是道館作業，可刪除本節。 -->

本 repo 用於繳交 <道館名稱>：

1. 我依《AI x BDD》課程平台道館頁列出的題目與完成條件，完成該館要求的 skills。
2. 我錄製了一支 3 分鐘內的 demo 影片，呈現 skill 的實際執行過程。
3. 我在《AI x BDD》課程平台的道館頁貼上本 repo 連結與影片連結，完成繳交。

## Skills 與設計想法

以下說明這個 repo 收錄的 skills，以及我設計時的考量。

三個 skill 都透過 `skill-creator` 建立：先讀完指定的官方網站並沿相關頁面查證，整理關鍵字、方法論、執行步驟、規則、適用條件與例外，並保留資料來源供他人核對。它們都設計成**跨專案**使用，不綁定特定 repo、語言、分支名稱或工具。建立過程見 [`conversation/2026-11-01-three-skills-conversation.md`](conversation/2026-11-01-three-skills-conversation.md)。

實際使用時，我通常三個一起啟用：`double-diamond-principle` 負責「做什麼」，`trunk-based-development` 負責「怎麼小步整合」，`git-conventional-commit` 負責「怎麼記錄變更」。

### `git-conventional-commit`

- **用途**：依 [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) 與專案慣例，根據實際 diff 撰寫或審查 commit message；使用者要求提交時，也會在授權範圍內執行 commit。
- **使用時機**：需要撰寫／審查 commit message、判定 type 與 scope、標記 breaking change、整理 squash 訊息，或請 AI 幫忙提交時。一般 Git 操作或程式碼修改不會自動啟用。
- **設計想法**：
  - 以英文 1.0.0 版為規範基準，並明確區分「規範要求」與「專案／工具慣例」，避免把字數限制、小寫、固定 type 清單誤當成標準（查證時發現繁中頁面額外推薦 `improvement`，因此以英文版為準）。
  - 流程固定為「確認提交範圍 → 閱讀變更證據 → 判定意圖與相容性 → 撰寫 → 驗證」，以行為與目的選 type，而不是只看檔名。
  - 驗證拆成語法、語意、專案工具三件事，linter 通過不等於語意正確。
  - 提交時只納入授權範圍的暫存內容、不 `git add .`；多行訊息用 `git commit -F` 避免 shell 展開；不自動 push、amend 或 rebase。
- **驗證狀態**：已驗證。
  - 建立時：通過 `skill-creator` 的 `quick_validate.py`、參考連結檢查，以及隔離 Git 操作測試（多行訊息完整保存、只納入暫存內容、修改最後一筆訊息時保留其他 staged changes）。
  - 實際使用：在我的 DevToys fork 開發 6 個功能時使用，產出如 `feat(history): add remove and clear actions for recent tools`、`feat(base64): add file encoding and decoding`、`feat(smart-detection): add per-tool recommendation toggles` 等訊息，並實際完成多筆 commit。

### `trunk-based-development`

- **用途**：依 [trunkbaseddevelopment.com](https://trunkbaseddevelopment.com/) 評估、導入或實踐主幹開發：辨識共同主幹、把變更拆成可獨立整合的小步增量，處理短期分支、CI、功能旗標與 release／hotfix 維護。
- **使用時機**：使用者要求以主幹開發方式整合變更、專案已採用此流程、需要把長功能或大重構拆成小增量，或評估團隊分支策略時。一般 Git 操作或單次 commit message 不會自動改造分支策略。
- **設計想法**：
  - 跨專案優先：先從 `AGENTS.md`、`CLAUDE.md`、CI 設定與分支資訊建立專案輪廓，不固定套用 `main`、`origin` 或特定 Git hosting。
  - 忠於網站原意：短期 PR 分支與版本維護用的 release branch 都允許存在；等待 review 的時間也算分支壽命，目標是當日整合。
  - 納入例外：只能在舊版重現的缺陷可先在維護分支修復；營運用 kill switch 不套用短期 release flag 的清理期限。
  - 「主幹開發」不是擴張授權的理由：commit、push、PR、merge 只在本次任務授權範圍內執行；PR 已開或分支 CI 綠燈不等於已整合。
- **驗證狀態**：已驗證，但完整的「頻繁整合回主幹」仍在建立中。
  - 建立時：通過格式與 metadata 驗證，以及獨立跨專案情境測試（主幹為 `team/integration`、remote 不是 `origin` 的受保護專案；只能在舊 release branch 重現的缺陷）。
  - 實際使用：在 DevToys 開發時，開工前同步 `origin/main`、先跑基線測試、每個功能使用短期分支（如 `feat/gui-tool-search-aliases`），搜尋別名功能已透過 PR 合併回 `main`，並據此討論 CI 與合併約定。
  - 已知限制：多數功能停在本機分支，由我自行 push／merge；這是 skill 刻意不自動擴張授權的結果。使用中也發現 AI 若沒有主動說明套用了哪些原則，使用者很難看出 skill 有被使用。

### `double-diamond-principle`

- **用途**：運用 Design Council 的 [Framework for Innovation（雙鑽石）](https://www.designcouncil.org.uk/resources/framework-for-innovation/)，透過 Discover、Define、Develop、Deliver 兩次發散與收斂，先找清楚要處理的問題，再找出值得採用的解法，並以原型與小規模測試迭代。
- **使用時機**：需求只有「想要某個功能」、問題尚未釐清，或要設計產品／服務／流程、檢視創新決策時。單純美化、文案修改或解法已明確的小修正，不會啟動完整研究流程。
- **設計想法**：
  - 依證據選切入點：依目前最大的不確定性決定從哪個階段開始，沿用已確認的需求與研究，不強制每次跑完四階段。
  - 保留框架的非線性：可回返、交疊，早期原型也能用來探索問題；測試動搖問題定義時回到 Define，而不是只修飾原型。
  - 嚴格區分證據與假設：模擬人物、工作坊偏好或精美原型都不能算使用者驗證，不虛構訪談或「已驗證」結論。
  - 不限軟體：適用於軟體、實體產品、服務與組織流程，不預設產物一定是 app 或程式碼。
- **驗證狀態**：已驗證，屬輕量應用。
  - 建立時：通過格式驗證，以及「服務流程」與「既有原型」兩項獨立情境測試，確認能區分證據與假設、沿用已確認需求而不重啟研究。
  - 實際使用：在 DevToys 開發時，先探索既有程式找出缺口（例如既有 Base64 工具無法處理一般檔案、收藏順序重啟後會改變），提出工作版問題定義並請我確認痛點，再收斂功能範圍；人工測試發現 Base64 解碼未辨識圖片格式後回頭修正。
  - 已知限制：Develop 階段較薄弱，缺少正式方案比較與實作前原型測試，也沒有使用者訪談與成效量測，因此只能稱為輕量雙鑽石應用。

## 交流

這些 skills 歡迎直接取用，或依你的專案調整。如果有使用上的問題或改進建議，歡迎開 issue 與我討論。

---

## 官方宣告（以下區塊請保留）

本 repo 為[水球軟體學院](https://world.waterballsa.tw)《AI x BDD：規格驅動全自動化開發術》課程的學員產出，用於公告課程中道館作業的展示，並依[官方 Template](https://github.com/AI-x-BDD/aixbdd-skill-homework-template) 建立。

《AI x BDD：規格驅動全自動化開發術》課程四大特色：

1. **【軟工方法論＋結果導向】**
   只談 AI coding 不談軟工方法論，是不學無術！課程中強調結果導向的開發方法：只要訂好驗收標準，就可以 One-Shot 開發到位！
2. **【最完整的 SDD 概念與實作】**
   市面上最完整的 SDD 課程，一次教你 Skills、SDD、TDD、BDD 的知識與實踐，輕鬆實踐高效可靠全自動開發。
3. **【企業級實戰導入】**
   用企業級實踐導入高規格，不只學會工具，更知道如何舉一反三完全客製化企業專案需求。
4. **【課程教學方式與物超所值】**
   這是一堂理論、實務跟實戰三者兼具的 SDD 線上課程，用工作坊級別的學習體驗，不是只是教概念，而是直接帶著做！

[了解更多課程內容說明](https://waterballs.tw/Ghl6O)

## 開源專案：AI x BDD

水球老師把這套 AI x BDD 的方法論做成開源 pipeline skill：把產品迭代寫成 Gherkin，再一路帶到 RED → GREEN → REFACTOR。

覺得有幫助，歡迎給它一顆 Star：

[![Star Waterball-Software-Academy/aixbdd](https://img.shields.io/github/stars/Waterball-Software-Academy/aixbdd?style=social)](https://github.com/Waterball-Software-Academy/aixbdd)

## 相關問題洽詢管道

1. [水球軟體學院 LINE 官方帳號](https://lin.ee/STIsOLZ)
2. 客服信箱：[support@waterballsa.tw](mailto:support@waterballsa.tw)
3. [水球軟體學院（社群）Discord](https://discord.gg/Ymjz7NmZXn)

---

© 2026 水球球特務有限公司版權所有，侵害必究。
