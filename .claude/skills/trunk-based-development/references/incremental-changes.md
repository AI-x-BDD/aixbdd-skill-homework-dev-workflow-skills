# 長期變更如何逐步整合

用於功能需要多日、元件替換或相容性遷移的任務。先嘗試縮小切片；feature flag 與抽象層只在能解決真實整合問題時使用。

## 選擇方法

| 需求 | 合適方法 | 需要先證明 |
| --- | --- | --- |
| 一次可完成的獨立行為 | 小步直接整合或短期 PR | 該增量完整可用、檢查通過 |
| 多日功能，尚不宜對使用者開啟 | Release feature flag | 關閉狀態維持現行行為，未完成路徑不會被意外使用 |
| 替換函式庫、模組或內部實作 | Branch by Abstraction | 舊實作可先透過同一契約繼續運作 |
| 外部客戶不能同時升級 | 相容層、版本化契約與遷移窗口 | 舊客戶在支持期間仍可工作；旗標不能代替對外相容性 |
| 資料格式／schema 變更 | 相容的分階段遷移 | 新舊程式、既有資料及回退策略之間的相容關係 |

前三項依 [Deciding factors 的 Story size](https://trunkbaseddevelopment.com/deciding-factors/)、[Feature flags](https://trunkbaseddevelopment.com/feature-flags/) 與 [Branch by Abstraction](https://trunkbaseddevelopment.com/branch-by-abstraction/)。後兩項是根據網站的相容性限制整理的設計檢查；本 skill 不提供通用資料庫遷移指令。

## Feature flags

先選最簡單且符合 repo 的設定方式。記錄旗標控制的行為、預設值、適用環境、負責人、啟用／關閉條件及後續清理條件，不自動引入新服務。

- 未完成功能的預設生產狀態應維持現行體驗；flag 關閉也要能建置、啟動與使用。隱藏 UI 不代表後端、背景工作或資料寫入不會被觸發。
- 驗證目前正式配置與準備發布的配置，以及會影響本次變更的重要組合。不要假設只測 off 足夠，也不要機械要求枚舉所有旗標的笛卡兒積。
- 啟用功能是一個獨立決策，須符合任務範圍與 rollout 程序。整合關閉中的程式碼，不表示授權在正式環境開啟它。
- 狀態需要在重啟或多節點間保持一致時，使用專案已有配置機制，並檢查其行為。
- 功能穩定且不再需要切換時移除 release flag 與死路徑；用可追蹤清理事項處理，不能將日期到期直接當成自動刪除授權。

依 [Feature flags 的 CI、Runtime switchable 與 Tech Debt](https://trunkbaseddevelopment.com/feature-flags/) 整理；負責人與啟用邊界是本 skill 的操作設計。

**例外**：release toggles 是過渡機制；營運 kill switch、實驗與權限控制有不同生命週期，不能全部按相同期限刪除。分類與持有成本見 Pete Hodgson 的 [Feature Toggles](https://martinfowler.com/articles/feature-toggles.html#CategoriesOfToggles)。網站建議的清理時間是參考，應依目的與實際使用狀況決定。

## Branch by Abstraction

這是程式設計上的替換方法，不是建立一條長期 Git 分支。每一步可分為多個通過檢查的小增量：

1. 為現有實作引入契約／介面，先讓呼叫者經過它，保留現行行為。
2. 在同一契約下增加新實作，暫不成為預設；逐步補齊相同行為的驗證。
3. 證明新實作可替代後，按授權的啟用方式切換；保留尚需的回退路徑。
4. 確認舊實作沒有仍需支持的使用者後，移除舊程式與暫時切換機制。
5. 只在抽象已無價值時移除它；若它仍是有用的設計／測試介面，可以保留。

這些步驟及保留抽象的例外來自 [Branch by Abstraction 的 Ideal steps 與 Software example](https://trunkbaseddevelopment.com/branch-by-abstraction/)。對仍需長期支持的舊 API／客戶端，先處理支持政策；不要以完成新實作為由立刻刪掉舊契約。

## 切片範例（本 skill 自製）

任務：用新的計價引擎替換目前實作，預估需要兩週。

| 可獨立整合的增量 | 預設行為 | 驗證重點 |
| --- | --- | --- |
| 包住舊引擎的價格介面 | 舊引擎 | 現有價格規則與呼叫者行為不變 |
| 新引擎的第一組價格規則 | 舊引擎 | 新規則與既有資料的對照，沒有新增生產副作用 |
| 補齊其餘規則與切換配置 | 舊引擎 | 新舊路徑及重要配置組合 |
| 依允許的 rollout 啟用 | 由 rollout 政策決定 | 價格一致性、失敗處理與回退條件 |
| 清理舊實作與過渡旗標 | 新引擎 | 無遺留呼叫者；保留有用介面 |

兩週是整項需求的工期，不是讓一條 feature branch 隔離兩週的理由。每一列仍可細分；不必為了套用範例強行引入旗標或影子流量。

## 資料與契約限制

先列出每個階段可能同時運行的程式版本、資料形狀與客戶端。新增相容能力、移轉讀寫／資料、確認舊使用者退場、再移除舊格式，是可考慮的順序；實際遷移要遵守該系統的限制與任務授權。

程式碼 revert 或關閉 flag 不一定能復原已寫入資料、外部副作用或不可逆 migration。沒有證據支持相容性時，不宣稱該切片已可安全發布。此處是操作推論；網站的 [Database migrations](https://trunkbaseddevelopment.com/deciding-factors/#database-migrations) 僅指出 schema 與資料須納入版本管理，並非完整操作手冊。
