# 三個跨專案 Skill 建立對話紀錄

匯出日期：2026-11-01  
時間標示：Asia/Taipei（UTC+08:00）  
原始對話 ID：`01a1968e-03af-7220-959c-48aef25fa6be`

本檔從原始對話紀錄擷取三次建立 skill 的使用者訊息、助理進度說明及完成回覆，依原順序保留文字與連結，未以摘要改寫。範圍截至第三個 skill 安裝完成；不含本次匯出操作、工具呼叫與執行輸出、環境資訊及自動載入的 skill 內容。

## 1. git-conventional-commit

### 使用者 · 2026-11-01 18:06:30

使用 $skill-creator ，並參考 https://www.conventionalcommits.org/en/v1.0.0/ 這個網站裡面的內容，幫忙產生 git-conventional-commit 的 skill，放在 ~/.agents/skills/ 。

創建 skill 時，均需要完成以下內容：
* 先讀完指定連結，再沿著網站中的相關頁面繼續查資料。
* 整理這個主題的關鍵字、方法論、執行步驟、規則、適用條件與例外
* 保留資料來源，讓其他人可以回到原始內容核對
* 透過 skill-creator，將整理結果寫成一個可以實際使用的 Skill。

### 助理｜進度說明 · 2026-11-01 18:06:32

我會使用 `skill-creator`，先完整閱讀指定規範及相關官方頁面，再整理成可實際使用的 `git-conventional-commit` skill，驗證後放到 `~/.agents/skills/`。

### 助理｜進度說明 · 2026-11-01 18:07:01

已讀完主規範與 FAQ，並沿連結查閱 SemVer、Git trailers、Angular 慣例及 commitlint。整理時會區分「規範要求」與「專案／工具慣例」，避免把字數限制、小寫或固定 type 清單誤當成標準；也會納入 breaking change、squash、revert 等例外情境。[規範來源](https://www.conventionalcommits.org/en/v1.0.0/)

### 助理｜進度說明 · 2026-11-01 18:09:03

Skill 草稿已完成，包含提交工作流程、規則與例外參考、來源索引。也保留了查到的差異：繁中頁面額外推薦 `improvement`，因此本 skill 以你指定的英文 1.0.0 版為準。接著會檢查格式、參考連結及提交操作範例。

### 助理｜進度說明 · 2026-11-01 18:10:12

官方 `quick_validate.py`、本機參考連結與 metadata 檢查均已通過。隔離測試也確認了多行訊息可完整保存、commit 只納入暫存內容，以及只修改最後一筆訊息時能保留其他 staged changes。現在將安裝這 4 個檔案到指定目錄。

### 助理｜完成回覆 · 2026-11-01 18:10:30

已建立並安裝 [git-conventional-commit Skill](/Users/adrianli/.agents/skills/git-conventional-commit/SKILL.md)。

- 包含關鍵字、方法論、執行步驟、規則、適用條件與例外。
- 已閱讀指定規範並沿相關官方連結查證，保留[來源索引](/Users/adrianli/.agents/skills/git-conventional-commit/references/sources.md)。
- 通過官方驗證器、參考連結檢查及隔離 Git 操作測試。

使用範例：`使用 $git-conventional-commit，根據暫存變更產生 commit message。`

## 2. trunk-based-development

### 使用者 · 2026-11-01 18:12:41

再次使用 $skill-creator ，並參考 https://trunkbaseddevelopment.com/ 這個網站裡面的內容，幫忙產生 trunk-based-development 的 skill，放在 ~/.agents/skills 。這個 skill 我希望是跨專案可以使用，同 git-conventional-commit 一樣。

### 助理｜進度說明 · 2026-11-01 18:12:44

我會再次使用 `skill-creator`，先讀完網站首頁，再沿相關頁面整理主幹開發、短生命週期分支、CI、功能旗標與發布流程，做成可跨專案使用的 skill，驗證後安裝到 `~/.agents/skills/`。

### 助理｜進度說明 · 2026-11-01 18:13:33

已確認幾個會影響 skill 設計的重點：主幹開發容許短期 PR 分支，也容許用於維護版本的 release branch；正式的整合目標仍是共同主幹，等待 review 的時間也算分支壽命。跨專案使用時，會先辨識各 repo 的主幹與既有流程，不固定套用 `main`、`origin` 或特定平台。[來源](https://trunkbaseddevelopment.com/short-lived-feature-branches/)

### 助理｜進度說明 · 2026-11-01 18:16:28

草稿已分成日常整合、長期變更拆分、發布維護與導入三部分。已納入兩個重要例外：舊版缺陷若無法在主幹重現，可先在維護分支修復；營運用 kill switch 也不應套用短期 release flag 的清理期限。接著會驗證格式，並用隔離情境檢查跨專案的判斷是否正確。

### 助理｜進度說明 · 2026-11-01 18:17:20

格式、metadata 與參考連結檢查已通過。獨立情境驗證正在測試兩種情況：主幹不是 `main`、remote 不是 `origin` 的受保護專案，以及只能在舊 release branch 重現的缺陷，確認 skill 能依專案證據選擇流程。

### 助理｜進度說明 · 2026-11-01 18:18:42

跨專案檢查也已確認，skill 沒有包含這次測試專案的路徑或設定。安裝內容共 6 個檔案；待情境驗證結果核對完成後，就會安裝到指定目錄。

### 助理｜進度說明 · 2026-11-01 18:18:52

情境驗證通過：skill 正確辨識 `team/integration` 為主幹、遵守 PR 保護規則，並為舊版限定缺陷選擇了合適的修補路徑。測試 repo 的分支、暫存區與未提交內容都未改動，也未發現需要修正的 skill 歧義。現在安裝已驗證的版本。

### 助理｜完成回覆 · 2026-11-01 18:19:07

已建立並安裝 [trunk-based-development Skill](/Users/adrianli/.agents/skills/trunk-based-development/SKILL.md)，可跨專案使用。

- 包含關鍵字、執行流程、適用條件與例外，涵蓋短期分支、CI、功能旗標、發布與修補。
- 已閱讀首頁及相關延伸頁面，保留[來源索引](/Users/adrianli/.agents/skills/trunk-based-development/references/sources.md)。
- 通過官方格式驗證與獨立跨專案情境測試。

使用範例：`使用 $trunk-based-development，評估這個專案的開發流程並規劃下一個功能的整合方式。`

## 3. double-diamond-principle

### 使用者 · 2026-11-01 18:20:42

再使用一次 $skill-creator，並參考 https://www.designcouncil.org.uk/resources/framework-for-innovation/ 這個網站裡面的內容，幫忙產生 double-diamond-principle 的 skill，放在 ~/.agents/skills。也是跨專案skill

### 助理｜進度說明 · 2026-11-01 18:20:45

我會使用 `skill-creator`，先讀完 Design Council 的指定頁面，再沿相關官方資料整理雙鑽石的階段、方法、適用條件與例外，做成可跨專案使用的 skill，驗證後安裝到 `~/.agents/skills/`。

### 助理｜進度說明 · 2026-11-01 18:21:34

已讀完指定頁面與雙鑽石的官方說明。這份 skill 會保留兩次「發散—收斂」、四個階段及四項設計原則，並依現有證據選擇切入點；官方也明確指出流程可以迭代，早期原型測試也能用於探索問題。[來源](https://www.designcouncil.org.uk/resources/framework-for-innovation/)

### 助理｜進度說明 · 2026-11-01 18:24:15

草稿已完成，包含四階段工作方式、方法選擇、證據記錄及跨專案調整。也特別區分了研究證據與假設：模擬人物、工作坊偏好或精美原型，都不能直接算作使用者驗證。接著會檢查格式，並用服務流程與既有原型兩種情境驗證實際用法。

### 助理｜進度說明 · 2026-11-01 18:26:13

格式與兩個獨立情境測試已通過：skill 能區分證據與假設，也能沿用已確認的需求，避免重啟整套研究。接下來將 5 個已驗證檔案安裝到全域 skills 目錄，包含使用指引、方法、適用條件與來源索引。

### 助理｜完成回覆 · 2026-11-01 18:27:26

已使用 `skill-creator` 建立並安裝跨專案的 [double-diamond-principle](/Users/adrianli/.agents/skills/double-diamond-principle/SKILL.md)。

- 已讀指定網站及相關官方頁面，保留[來源索引](/Users/adrianli/.agents/skills/double-diamond-principle/references/sources.md)。
- 包含四階段、關鍵字、方法、執行步驟、規則、適用條件與例外。
- 已通過格式驗證、兩項獨立情境測試及安裝完整性檢查。

使用範例：`使用 $double-diamond-principle，協助釐清這個專案的問題並規劃下一步驗證。`
