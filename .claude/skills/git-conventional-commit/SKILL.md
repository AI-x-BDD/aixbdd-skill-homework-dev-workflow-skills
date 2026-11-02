---
name: git-conventional-commit
description: "依 Conventional Commits 1.0.0 與專案慣例，根據 diff 撰寫或審查 Git commit message，並在使用者要求提交時執行 commit。適用於 type／scope 判定、breaking change 標記及 squash 訊息整理；不因一般 Git 操作或程式碼修改而自動啟用。"
---

# Git Conventional Commit

讓提交訊息忠實描述本次變更，能被人與工具理解。以 [Conventional Commits 1.0.0 英文版](https://www.conventionalcommits.org/en/v1.0.0/) 為規範基準；專案設定可以增加限制，須清楚區分規範、工具規則與寫作建議。

## 工作方法

依「確認提交範圍 → 閱讀變更證據 → 判定意圖與相容性 → 撰寫 → 驗證」處理。撰寫／審查訊息不包含建立 commit；使用者已要求提交時，完成必要檢查後直接執行授權範圍內的提交。

### 1. 確認上下文與範圍

- 先讀適用的 `AGENTS.md`、`CLAUDE.md`、`CONTRIBUTING`、commitlint／release 設定及近期訊息，確認語言、scope、type、長度與合併方式。以明文設定為準，歷史只作線索；不要複製歷史中的格式錯誤。
- 有本機 repo 時，按需要執行下列唯讀檢查。若使用者已提供完整 diff 或訊息，只讀與任務有關的資料。

```sh
git status --short --branch
git diff --cached --stat
git diff --cached
git diff
git log -8 --format=%s
```

- `--cached` 是預備提交的 index 內容；未暫存 diff 與未追蹤檔案不屬於它。只為 staged changes 寫訊息時，不混入其他變更。新 repo 沒有歷史屬正常情況。
- 若未指定範圍而已有 staged changes，先以該範圍為候選；沒有 staged changes 時可依工作區提出草稿並標明尚未暫存。只有無法從現有資料判定的歧義會影響結果時才詢問。

### 2. 判斷意圖與相容性

- 以變更造成的行為與目的選 type，依模組或套件選 scope；不要只看檔名。新增功能用 `feat`，修復缺陷用 `fix`。其餘 type 依專案慣例；判定細節見 [規則與例外](references/conventions.md)。
- 檢查公開契約是否不相容，例如移除 API、改必填參數、變更 CLI／設定格式或支援環境。內部重構本身不等於 breaking change；任何 type 都可能包含 breaking change。
- 無關目的優先拆成可理解的提交；同一修正所需的程式、回歸測試與文件通常可放一起。拆分是為了表達意圖，不按副檔名機械分組。
- 若使用者要求一筆 squash commit，描述整體交付結果，保留所有破壞性影響；不要自行改成多筆提交。

### 3. 撰寫訊息

```text
<type>[(scope)][!]: <description>

<optional body>

<optional footers>
```

方括號代表可省略，不是實際訊息的一部分。

- description 說明具體改變；body 僅在有助理解時補充原因、前後行為或限制。不虛構 issue 編號、測試結果、審查者或遷移方式。
- breaking change 使用緊鄰冒號的 `!`、大寫 `BREAKING CHANGE: 說明` footer，或兩者。只有 `!` 時，description 必須說清楚破壞性改變；有遷移資訊時補上受影響用法與替代方式。
- 依使用者指定及專案語言撰寫；沒有既定語言時沿用使用者語言。保留慣用 type 與 `BREAKING CHANGE` 等機器辨識 token。沒有既定樣式時，偏好小寫 type 與簡潔描述，但不把這些偏好當作標準強制規定。

### 4. 分別驗證三件事

1. **語法**：依 [規則與例外](references/conventions.md) 檢查 header、空行、footer 與 breaking 標記。審查模式須讀該參考，避免誤判規範容許的形式。
2. **語意**：訊息符合實際目標 diff、沒有漏掉 breaking change，也沒有把測試或文件誤當主功能。僅有文字、沒有 diff 時，明示語意尚無法驗證。
3. **專案工具**：存在已安裝的訊息驗證工具時，使用該專案的設定與命令；回報未執行的驗證。不要為單次訊息任務自行安裝依賴、修改 hook 或 release 設定。不要把 linter 通過當成語意正確的證明。

格式或工具相容性有疑問時回到 [來源索引](references/sources.md) 查核；查詢工具行為時，以專案安裝版本為準。一般訊息草稿不必重新爬完整網站。

### 5. 僅在任務包含提交時執行

- 確認 index 全部屬於這次授權範圍。需要暫存時只選相關檔案或 hunks；保留使用者已做的部分暫存與其他修改。遇到混雜且無法可靠分離的變更，先說明範圍問題，不直接 `git add .`。
- 完成專案要求且適合這次變更的檢查。多行訊息先寫入唯一的暫存文字檔，使用 `git commit -F <message-file>`，避免 shell 展開反引號或變數。Git 的 index 與 `-F` 行為見 [Git commit 官方文件](https://git-scm.com/docs/git-commit)。
- commit 失敗後先檢查 HEAD、index 與錯誤；可修復本次範圍內的問題再驗證，不盲目重試、繞過 hooks 或停用簽章。權限、憑證或範圍外問題阻止繼續時，回報具體阻礙。
- 成功後用 `git log -1 --format='%h%n%B'`、`git show --stat --oneline HEAD` 及 `git status --short` 確認訊息與內容。建立 commit 不自動包含 push、tag、release、amend 或 rebase；若這些操作已在任務中獲授權，依該範圍處理。

## 交付方式

- **草稿**：給可直接使用的完整訊息；只有影響採用的假設才另行解釋。
- **審查**：分開標明標準違規、專案限制與可選建議，提供修正版；不改動 Git 歷史。
- **提交**：回報 commit hash、訊息、已做的檢查，以及仍未提交的相關變更。

## 參考資料

- [規則、關鍵字、適用情境與例外](references/conventions.md)：選 type、檢查 footer、判斷 breaking change、squash／revert／版本影響時使用。
- [來源與閱讀路徑](references/sources.md)：保留原始章節、查閱日期、版本與整理依據，供核對與更新。
