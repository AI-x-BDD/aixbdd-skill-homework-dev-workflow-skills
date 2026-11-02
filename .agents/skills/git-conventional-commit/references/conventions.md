# 規則、關鍵字與例外

規範依據為 [Conventional Commits 1.0.0（CC）](https://www.conventionalcommits.org/en/v1.0.0/#specification)。以下按任務重整，不是逐字翻譯；各來源與改寫聲明見 [來源索引](sources.md)。

## 關鍵字與規範層級

| 關鍵字 | 判斷時的用途 |
| --- | --- |
| type | 提交的主要意圖，不是檔案種類 |
| scope | 選填的程式區域，例如模組或套件名稱 |
| description／subject | header 的簡短變更摘要 |
| body | 選填的背景、原因與行為說明 |
| footer／trailer | 末尾的結構化資訊；CC 與 Git trailer 語法並非完全相同 |
| breaking change／`!` | 公開契約不相容的變更標記 |
| SemVer | 版本相容性語意；不等同實際 release 工具的規則 |
| atomic commit | 以單一可理解的目的組織變更的方法 |
| squash／revert | 前者將變更整合為一筆提交，後者撤回變更；須看最終效果 |

[RFC 2119](https://www.rfc-editor.org/rfc/rfc2119.txt) 中 MUST／MUST NOT 是必要條件或禁止事項；SHOULD 是容許有理由例外的建議；MAY 是可選。不要把建議升格成規範要求。

## 語法核對

| 檢查項目 | 要求與例外 | CC 條款 |
| --- | --- | --- |
| header | type 為名詞；依序可有 `(scope)`、`!`，再接必要的 `: ` 與非空的簡短 description | 1、5 |
| 功能與修復 | 新功能使用 `feat`，修復缺陷使用 `fix`；其他意圖可有自訂 type | 2、3、14 |
| scope | 可省略；若提供，以括號包住描述程式區域的名詞，不能為空 | 4 |
| body | 可省略；與 header 隔一個空白行，內容可為自由格式、多段落 | 6、7 |
| footer 區塊 | 可有多個，與前面正文隔空白行；沒有 body 時可直接接在 header 後的空白行 | 8 與官方 Examples |
| footer 項目 | token 後接 `: ` 或 ` #`，再接值，例如 `Refs: #42` 或 `Refs #42` | 8 |
| footer token | 以連字號取代空白；`BREAKING CHANGE` 是允許含空白的例外 | 9 |
| footer 值 | 可跨行、含空白；遇下一個合法 token／分隔符組合才結束 | 10 |
| breaking 標記 | header 的 `!` 必須緊鄰 `:`；或使用大寫的 `BREAKING CHANGE: 說明` footer | 11–13 |
| 單用 `!` | 合法，此時 description 要描述破壞性改變；不強制同時寫 footer | 13 |
| 大小寫 | 除 breaking footer token 必須大寫外，規範資訊不應以大小寫區別；團隊可約定一致樣式 | 15、FAQ |
| 別名 | footer token `BREAKING-CHANGE` 與 `BREAKING CHANGE` 同義 | 16 |

只在 body 寫「這是 breaking change」而缺少合法 header／footer 標記，不滿足 breaking 標記要求。只有 `BREAKING CHANGE:` 而缺乏具體說明也不足。

## Type 選擇

先辨識變更目的，再依 repo 的允許清單選擇。下表中只有 `feat`／`fix` 的用途由 CC 強制指定；其餘是 [Angular 歷史慣例](https://github.com/angular/angular/blob/22b96b96902e1a42ee8c5e807720424abad3082a/CONTRIBUTING.md#type)、[commitlint config-conventional](https://github.com/conventional-changelog/commitlint/tree/master/%40commitlint/config-conventional) 及常見團隊做法的實務參考，不是封閉清單。

| Type | 適用條件 |
| --- | --- |
| `feat` | 新增應用程式或函式庫功能 |
| `fix` | 修復既有錯誤行為 |
| `docs` | 單純文件內容變更 |
| `refactor` | 調整程式結構，目的不是新增功能或修復 bug |
| `perf` | 以效能改善為主要目的 |
| `test` | 單純新增或修正測試 |
| `style` | 不影響程式意義的格式變動；不能把所有 CSS／視覺功能修改都歸類在此 |
| `build` | 建置系統、打包或相依套件調整 |
| `ci` | 持續整合設定與流程 |
| `chore` | 依 repo 定義的其他維護，不用來掩蓋功能、bug fix 或破壞性變更 |
| `revert` | 依專案與工具慣例描述撤回變更 |

若同一筆修復也加測試，用 `fix` 表達主要效果，不因出現測試檔就改成 `test`。套件升級也要看動機與公開影響，不能只按 lockfile 決定 type。

## 工具與風格的邊界

- CC 不強制 50／72／100 字元上限、英文、祈使句、小寫、無句點、固定 scope 清單或 body 必填。可遵循專案的額外規則，但審查時要標明來源。
- [commitlint config-conventional](https://github.com/conventional-changelog/commitlint/tree/master/%40commitlint/config-conventional#beyond-the-specification) 增加 type 清單、大小寫、句尾與長度等限制。通用 rules 文件的參數範例也不等於 repo 生效值；讀本地 config、extends 與版本。
- 若啟用 [`breaking-change-exclamation-mark`](https://commitlint.js.org/reference/rules#breaking-change-exclamation-mark)，工具會要求 `!` 與 breaking footer 同時出現或同時缺席。這是額外規則；CC 本身允許二選一。
- [`git interpret-trailers`](https://git-scm.com/docs/git-interpret-trailers#_description) 可輔助查看 Git metadata，但其 key 與分隔符規則不同，不能用它判定所有 CC footer，尤其是 `BREAKING CHANGE` 與 `Refs #42`。

## 相容性與版本影響

CC 的基本對應是非破壞性 `fix` → PATCH、非破壞性 `feat` → MINOR、任何 type 的 breaking change → MAJOR；其他 type 本身沒有規範指定的版本影響。這是語意對應，不是執行發版的授權。[CC：SemVer FAQ](https://www.conventionalcommits.org/en/v1.0.0/#how-does-this-relate-to-semver)

[SemVer 2.0.0 §1、4–8](https://semver.org/spec/v2.0.0.html#spec-item-1) 以明確的 public API 為基準。`0.y.z` 屬初始開發，不能一看到 `!` 就斷言下一版必為 `1.0.0`；仍應清楚標記相容性改變，再看專案發版政策。新增 deprecated 公開 API 標記在 SemVer 屬 MINOR；只機械讀 type 可能漏掉這類語意。

[semantic-release commit-analyzer](https://github.com/semantic-release/commit-analyzer#releaserules) 的 preset、parserOpts、releaseRules 會影響結果，例如預設規則也將 `perf` 對應 PATCH。只有查到專案設定與待發版提交範圍後才預測實際版本；不要為確認訊息而直接啟動發版工具。

## 情境與例外

| 情境 | 處理方式與依據 |
| --- | --- |
| 多個獨立目的 | 可行時拆成多筆；必要測試與文件隨對應功能一起提交。依 CC FAQ 的拆分建議，後半是實務判斷 |
| squash workflow | 檢查實際會落入主線的完整訊息，保留整體語意及 breaking footer。只改 PR title 是否足夠取決於平台設定；不推定每個中間 commit 都要重寫 |
| 初始開發 | CC FAQ 建議仍描述功能、修復與破壞性影響；版本決策另依 SemVer 的 0.x 規則 |
| type 拼錯或用錯 | 能解析不代表語意正確；`feet` 可能被當自訂 type，但新增功能仍應用 `feat`。發現錯誤不等於獲准改寫歷史 |
| 已存在的錯誤訊息 | 未合併／未發版時可在已授權範圍內修正；已共享歷史先確認改寫範圍，已發布版本依流程補救。CC FAQ 的 rebase 建議不授予 force-push 權限 |
| revert | CC 不定義固定的 revert 發版演算法。可用 `revert` 並引用真實被撤回 SHA；是否恢復相容、抵消某筆變更或觸發版本更新，要看實際內容與工具 |
| 只改最後一筆訊息 | 若任務已授權 amend 且必須保留其他 staged changes，可使用 Git 的 `--amend --only -F <message-file>`；普通 amend 可能納入 index。先確認原提交與共享狀態，再核對提交 tree 未變 |

前六項的原始問答見 [CC FAQ](https://www.conventionalcommits.org/en/v1.0.0/#faq)；最後一項依 [Git commit 的 --only 與 --amend](https://git-scm.com/docs/git-commit#Documentation/git-commit.txt---only)。

## 自製範例

單行修復，無 body 合法：

```text
fix(cache): 修正過期項目仍被回傳的問題
```

僅用 `!`，description 已說明破壞性行為：

```text
feat(cli)!: 移除 --legacy-output 選項
```

保留遷移說明與多個 footers；issue 編號僅為示例，實際使用須有證據：

```text
refactor(config)!: 改用毫秒表示請求逾時

統一設定檔與執行時的時間單位，避免載入時隱含轉換。

BREAKING CHANGE: 移除 timeoutSeconds，改用 timeoutMs。
既有設定值需乘以 1000 並更換欄位名稱。
Refs: #42
```

| 待審訊息或片段 | 判斷 |
| --- | --- |
| `fix(api):修正空回應` | 缺冒號後空格，應為 `fix(api): 修正空回應` |
| `fix!(api): 修正舊版協定` | `!` 位置錯誤，應為 `fix(api)!: ...` |
| `FIX: 修正快取失效` | CC 允許；若 repo 要求小寫，才屬專案規則違反 |
| `build: 更新打包器` 搭配 `BREAKING-CHANGE: 停止支援舊版執行環境` | 有適當空白行與真實破壞性影響時合法，不限 `feat`／`fix` |
| 只有 `breaking change: 移除舊欄位` footer | 不是有效 breaking marker；需大寫或合法 header `!` |
| `Refs #42` | 合法 footer 形式；前面須有 footer 區塊的空白行 |
