# 整合、分支與 CI

用於日常整合路徑選擇與流程審查。這些是跨專案決策準則；實際命令、權限與工具使用專案既有設定。

## 關鍵字

| 詞彙 | 實際判斷 |
| --- | --- |
| trunk／mainline | 共同開發的整合點，不要求字面名稱是 `main` 或 `trunk` |
| integration distance | 變更與共同主幹分離的時間與內容差距；每天 pull 只縮短一個方向的差距 |
| small batch／vertical slice | 可理解、可驗證並能獨立整合的變更單位；一個 story 可有多個增量 |
| short-lived branch | 服務一個增量的暫時分支，包含開發、檢查與 review 的完整等待時間 |
| continuous integration | 開發者頻繁把變更整合到共同主幹，並驗證整合結果；不只是架設 CI server |
| continuous review | 快速且持續處理 review，使待整合變更不積壓 |
| pre-integration gate | 在變更落地主幹之前驗證候選結果 |
| merge／commit queue | 對最新主幹上的候選合併結果排序與驗證，處理競爭，不是免測的合併捷徑 |
| green build | 已明確標識內容與範圍的檢查通過；不能保證沒測到的性質 |
| releasable | 可依發布程序交付的狀態，不代表每筆提交都已部署到正式環境 |

詞彙依 [Five-minute overview](https://trunkbaseddevelopment.com/5-min-overview/)、[CI](https://trunkbaseddevelopment.com/continuous-integration/) 與 [Continuous Review](https://trunkbaseddevelopment.com/continuous-review/) 重整。

## 選擇日常路徑

| 路徑 | 適用條件 | 需要維持的條件 |
| --- | --- | --- |
| 直接主幹 | 專案明確允許，review 與整合驗證能跟上寫入量 | 同步、驗證、小步落地；有失敗後的快速修復方式 |
| 短期 PR／MR | 需要合併前 review、required checks 或權限分工 | 小增量、快速 review、以主幹為 base 與目標 |
| Patch review／queue | 已有 patch 驗證流程，或多人／代理同時寫入造成競爭 | 檢查真正即將落地主幹的候選組合，維持可追溯性 |

依 [Styles and Trade-offs](https://trunkbaseddevelopment.com/styles/) 與 [Committing straight to the trunk](https://trunkbaseddevelopment.com/committing-straight-to-the-trunk/) 整理。文章中的團隊人數與分鐘數是情境建議，不是讓跨專案 skill 自動改變治理規則的門檻。

## 短分支的生命週期

工作流為「已確認的主幹 → 單一增量 → 檢查與 review → 主幹 → 收尾」。網站主張頻繁、至少每日整合，短分支通常至多一至兩天；等待 review 也算在內。這些指標用於提早發現批次過大，不是允許未通過檢查就硬合併。[首頁](https://trunkbaseddevelopment.com/)、[短期分支](https://trunkbaseddevelopment.com/short-lived-feature-branches/)

- 一個分支服務一個開發意圖，可由單人、pair／mob 或其協作工具完成；避免成為多人持續加入的第二條共同開發線。
- 同步主幹的更新後，將完成的增量送回主幹。其他工作應從主幹取得依賴，不靠 feature branch 彼此交換未整合變更。
- 有先後依賴的切片，先讓前置切片落地主幹，再更新後續切片。現有 stacked PR 須辨識依賴與實際 base，不能假裝它們全都獨立；未要求遷移時不擅自重寫整串歷史。
- rebase、squash 或重建分支不會消除工作實際已隔離的時間。以 PR／工作開始與最後整合的證據估計，不能只用最新 commit 日期判定壽命。
- worktree 可用來隔離 review／hotfix，但增加 worktree 不會增加 review 能力。按在製變更的數量與老化程度控管，避免尚未整合的分支持續累積。

前兩項及 worktree 原則源於 [Short-Lived Feature Branches](https://trunkbaseddevelopment.com/short-lived-feature-branches/)；依賴切片與壽命判讀是本 skill 的操作推論。

## 如何驗證整合

1. 找到專案既有的 build 與 test 入口，先辨識基線是否已失敗。不要只依 README 上的徽章推定目前主幹健康。
2. 在本機或專案已有的私有環境跑適用檢查；需要遠端平台的矩陣檢查留給合併前 gate。缺少環境時回報未覆蓋處，不以跳過檢查宣稱成功。
3. 確認被驗證的是本次候選內容以及必要的主幹整合組合。遇到 base 前進、解衝突或 queue 重組，按平台機制更新／重驗證。
4. 在真正落地主幹後，核對後續 CI 與對應 commit。squash merge 會改 hash，應以平台 merge 記錄與內容確認，不能只做原分支 SHA 的祖先判斷。

前後兩階段驗證依 [CI 的 Pre or Post Commit 與 The high bar](https://trunkbaseddevelopment.com/continuous-integration/)。候選內容識別與 squash 核對是本 skill 的操作設計。分支獨自通過，不能替代與其他變更組合後的驗證。

## 建置慢或不穩定

依 [Fast, Green Builds](https://trunkbaseddevelopment.com/fast-green-builds/) 的方法，先量測編譯、測試、排隊與環境準備的耗時，再選增量建置、可靠快取、平行執行、測試分層或有證據支持的影響範圍選測。保留有意義的涵蓋範圍，不因加速而只剩無法抓出回歸的測試。

本機快速回饋、整合 gate 與較慢的發布檢查可有不同層次；都必須有明確責任。不能本機執行的檢查可由即時遠端 gate 承擔。不要反覆 push 試錯來取代可在本機完成的建置，也不要為一次任務自行購買／租用雲端資源。

不穩定測試須有可重現的診斷與修復；只為取得綠燈而無限 rerun 或取消 required check，不算恢復可靠性。聲稱測試「通過」時同時說清驗證對象、測試範圍與未驗證處。

## 主幹變紅

先建立「失敗檢查 → 受影響 commit／組合 → 可重現原因」的證據。能在短時間內明確修復且驗證時採 fix；原因不明或修復會拖延時評估針對性 revert。兩者都需符合任務授權與 repo 政策；不要混淆 Git revert 與正式資料回復。[直接主幹模式的失敗處理](https://trunkbaseddevelopment.com/committing-straight-to-the-trunk/)

重試前要有新證據或實際改變。若 checks、外部權限、人工 review 或 queue 無法推進，交付現有可審查變更與阻礙；不要以 bypass、force-push 或改分支保護換取形式上的完成。
