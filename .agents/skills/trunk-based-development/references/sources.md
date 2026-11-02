# 來源與查閱範圍

查閱日期：2026-11-01。先完整閱讀指定首頁，再循其導覽與內文連結閱讀下列相關章節。主要來源為 Paul Hammant 與 contributors 維護的 [Trunk Based Development](https://trunkbaseddevelopment.com/)。這是實務知識網站，不是帶規範版本號的標準。

查閱時網站頁尾顯示 published SHA `44abf38fc1215e859c3157536c0dfa5353925ce0`。該 SHA 僅記錄頁面顯示的版本線索；GitHub commit 頁此次未能載入，不將它當作已獨立驗證的完整快照。

## 從首頁沿導覽閱讀

| 原始頁面 | 已閱讀範圍與採用內容 |
| --- | --- |
| [Introduction](https://trunkbaseddevelopment.com/) | 完整正文：共同主幹、每日整合、大小團隊風格、短分支與 release branch 的適用性 |
| [Five-minute overview](https://trunkbaseddevelopment.com/5-min-overview/) | Distance、What it is、Releasability、Committing、Code Reviews、Safety net；形成小步整合與可發布的目的 |
| [Deciding factors](https://trunkbaseddevelopment.com/deciding-factors/) | Story size、Build times、Peak commit frequency、Database migrations、Shared code；以工作大小與回饋能力判斷流程 |
| [Committing straight to the trunk](https://trunkbaseddevelopment.com/committing-straight-to-the-trunk/) | 基本風格、Benefits、Challenges、並行寫入與 queue；直接主幹的驗證與恢復條件 |
| [Short-Lived Feature Branches](https://trunkbaseddevelopment.com/short-lived-feature-branches/) | 壽命、review 等待、Merge directionality、worktrees、Pitfalls、Breaking the contract；短分支生命週期與依賴方向 |
| [Styles and Trade-offs](https://trunkbaseddevelopment.com/styles/) | 風格選擇與直接主幹的代價；比較 direct、短期 branch 與 patch review 的適用性 |
| [Continuous Integration](https://trunkbaseddevelopment.com/continuous-integration/) | 定義、Human actions、CI services、Quick build news、Pre or Post Commit、The high bar；合併前後驗證與快速回饋 |
| [Fast, Green Builds](https://trunkbaseddevelopment.com/fast-green-builds/) | Why it matters、Local and remote、Leased pre-push capacity、Tiered tests；區分本機可做的檢查與遠端 gate，改善速度而不放棄有效驗證 |
| [Continuous Code Review](https://trunkbaseddevelopment.com/continuous-review/) | High bar、PRs、開源與內部 review 差異、review 積壓；沒有將特定 AI review 主張改成必要流程 |
| [Feature flags](https://trunkbaseddevelopment.com/feature-flags/) | Implementation、CI pipelines、Runtime switchable、Build flags、Tech Debt；配置、測試組合與清理 |
| [Branch by Abstraction](https://trunkbaseddevelopment.com/branch-by-abstraction/) | Rules、Ideal steps、Software example、Not a panacea；逐步替換、抽象保留與舊 API 的限制 |
| [Branch for release](https://trunkbaseddevelopment.com/branch-for-release/) | Late creation、Branching from before HEAD、Fix production bugs on Trunk、Cherry-pick 方向與例外、Patch releases；發布維護流程 |
| [Release from trunk](https://trunkbaseddevelopment.com/release-from-trunk/) | 完整正文：主幹發布、fix forward、事後從歷史狀態建立維護分支 |
| [Continuous Delivery](https://trunkbaseddevelopment.com/continuous-delivery/) | CD 與 Continuous Deployment；可交付和自動上線的差別 |
| [You're doing it wrong](https://trunkbaseddevelopment.com/youre-doing-it-wrong/) | 分支命名、錯誤修復方向、長 feature branch、release branch 的用途與結束；用於流程審查 |

## 沿 Feature flags 延伸

[Pete Hodgson：Feature Toggles (aka Feature Flags)](https://martinfowler.com/articles/feature-toggles.html)，刊於 MartinFowler.com，頁面標示 2017-10-09。從主站 Feature flags 的 References elsewhere 連入；閱讀 Categories of toggles 與 Managing the carrying cost，補充 release、experiment、ops、permissioning 的不同生命週期，避免把所有旗標一律視為短期過渡。

## 本 skill 的整理邊界

- 內容是以繁體中文重整的決策流程、摘要與自製範例，沒有複製原站圖表或整篇文章；各段保留原始頁面與章節連結。
- 主站的長期共同主幹、小步頻繁整合、快速可靠驗證與發布維護方向是方法核心。人數、分鐘數、旗標清理月份、特定平台與產品推薦是作者情境建議，不直接升格為所有專案的硬規則。
- 專案輪廓、授權對照、部分暫存保護、候選內容核對、停止重試條件、遷移步驟與交付格式屬本 skill 的操作設計。它們服務於實作，不宣稱是網站逐字規定。
- 作者對工具、AI review 與 CI 的強烈判斷不等於本專案已採納的政策。測試全綠只能支持已測的範圍；不轉述為絕無缺陷的保證。
- 更新時先查原站相關章節與本專案實況。需要配置 hosting、merge queue、VCS 或 CI 時，再核對對應版本的官方文件；不要從網站歷史示例推定今天的平台行為。
