---
name: trunk-based-development
description: "跨專案評估、導入或實踐 Trunk-Based Development：辨識共同主幹、拆分可整合的小步變更，處理短期分支、CI、功能旗標與發布維護。適用於使用者要求主幹開發，或專案已採此流程的整合任務；不因一般 Git 操作、單次 commit message 或任意程式修改而自動改造分支策略。"
---

# Trunk Based Development

將變更持續整合到一條共同開發主幹，讓每個落地主幹的增量可驗證、可供其他人使用，並維持可發布狀態。主幹可以使用任意名稱；短期審查分支與必要的版本維護分支都可存在。[主題來源](https://trunkbaseddevelopment.com/)

本 skill 不綁定語言、框架、Git hosting、remote 名稱、monorepo 或發布產品。網站提供實務方法，並非 RFC 式標準；區分核心原則、作者建議、專案政策與本 skill 的操作設計。

## 先辨識任務與專案

判定本次是流程評估／導入規劃、在既有流程中實作整合、長期變更拆分，或 release／hotfix 維護。只處理該範圍；評估流程本身不表示要修改 repo 或發布系統。

從適用的 `AGENTS.md`、`CLAUDE.md`、`CONTRIBUTING`、CI／發布設定、分支與 PR 資訊建立最小專案輪廓：

| 項目 | 要確認的內容 |
| --- | --- |
| 整合目標 | 實際共同開發主幹、權威 remote／repo；default branch 只作線索 |
| 工作狀態 | 目前分支／worktree、未提交與部分暫存內容、進行中的 merge／rebase、他人共用狀況 |
| 進入主幹方式 | direct commit、PR／MR、patch review 或 merge queue；必要 review／checks 與允許的 merge 方法 |
| 驗證入口 | repo 實際 build／lint／test 命令、目前基線、遠端限定檢查與 CI 對應 commit |
| 發布限制 | 從主幹或 release branch 發布、維護中的版本、旗標與相容性要求 |
| 本次範圍 | 已授權的實作、commit、push、PR、merge、backport 或部署；沿用既有授權 |

Git 專案可先用以下唯讀資訊輔助判斷，不以分支名稱代替政策：

```sh
git status --short --branch
git branch --show-current
git branch -vv
git remote
git worktree list
```

再按需要讀具體 refs、相關 diff、CI 與歷史。遠端追蹤 ref 可能過時；確認 remote 後才 fetch。沒有 repo 時以使用者提供的事實規劃，標明未知項；只有歧義會影響下一個動作時才詢問。不要把目前工作目錄或前一專案的設定帶入新專案。

## 核心工作流程

### 1. 選擇整合路徑

沿用專案既有 direct-to-trunk、短期 PR／MR 或 queue 流程。讀 [整合與 CI](references/integration.md) 處理分支壽命、review、同步與檢查。

- 沒有既定流程時，依並行寫入量、review 能力、建置速度與分支保護決定；不以固定人數門檻選型。
- 保護規則要求 PR 時，仍可實踐主幹開發；不得為了「直接主幹」繞過 review。若專案實際仍採多條長期開發分支，先說明差距，導入範圍見 [發布與導入](references/releases-and-adoption.md)。

### 2. 拆出可獨立整合的增量

對每個增量說清楚：改變的行為、依賴的已落地主幹內容、驗證方法，以及尚未完成時為何仍可發布。功能可尚未對使用者開啟，但既有行為與建置必須維持可用。

以當日整合為目標；網站的一至兩天短分支準則包含 review 等待。若預期超過，就縮小範圍或調整設計，不靠反覆 rebase、改名或強行合併達標。需要隱藏未完成功能或漸進替換元件時，讀 [增量變更技巧](references/incremental-changes.md)。

### 3. 實作並驗證候選內容

- 同步已確認的主幹，保留使用者工作與部分暫存；不要自行 stash、reset、覆蓋或重寫共享分支。需要隔離時可使用適當工作區，但不為此額外開發無關功能。
- 使用專案的本機／私有建置入口，完成與變更相關且專案要求的檢查。遠端才可執行的檢查應成為整合 gate，並明示本機未覆蓋的部分。
- 主幹在驗證期間改變、候選內容更新或解決了衝突後，要驗證新的整合結果。文字合併乾淨不代表行為相容；過期綠燈不能證明當前候選內容安全。

### 4. 按授權完成整合

只在本次任務涵蓋的範圍內建立 commit／PR、push、merge 或進入 queue。主幹開發不是授權擴張的理由，也不要求已授權動作再重複確認。實作已完成但外部 gate 未通過時，保留可審查結果並回報 gate。

整合成功以實際主幹包含預期變更、對應檢查狀態與必要 review 為準；PR 已開、分支 CI 綠燈或排入 queue 都不等於已整合。只在授權範圍內清理已完成的分支／worktree，先確認無獨有工作；保留 review 與提交記錄。

push／merge 遭拒時先辨識 stale base、衝突、checks、權限或政策問題。可在授權範圍修復並重新驗證；同一原因未變時停止重試。高競爭造成反覆落後時使用既有 queue 或回報瓶頸，不無限 sync／push／poll，也不以 force-push 繞過。

### 5. 主幹失敗時優先恢復

讀取失敗證據，區分新回歸、既有故障、測試不穩定與環境問題。以可快速驗證的修復或範圍明確的 revert 恢復整合能力；不要繼續堆疊依賴已知失敗的變更。依事故政策處理，停用 gate、鎖主幹與正式環境 rollback 都不是自動獲准的副作用。[CI 原理](https://trunkbaseddevelopment.com/continuous-integration/)

## 條件式工作

- **長功能／大重構**：讀 [增量變更技巧](references/incremental-changes.md)，選小步切片、feature flags 或 Branch by Abstraction；這些方法不要求一定採購旗標服務。
- **release／hotfix／backport**：讀 [發布與導入](references/releases-and-adoption.md)，確認來源 commit、目標版本、trunk-first 修復路徑及無法在主幹重現的例外。
- **導入或流程審查**：同上，先以證據找出整合與 review 瓶頸，提出漸進改善；不擅自刪除 `develop`、改預設分支或開啟自動部署。
- **來源查核**：讀 [來源索引](references/sources.md)。網站的數字與工具能力須依情境及安裝版本核對；一般整合任務不必重讀所有頁面。

## 完成時回報

依任務簡要交付：確認的主幹與流程、完成的增量／拆分方案、驗證證據、目前停在本機／PR／queue／主幹／發布的哪一階段，以及尚待處理的 gate 或遷移事項。發布、部署與功能啟用分開說明；不要把「可發布」寫成「已上線」。
