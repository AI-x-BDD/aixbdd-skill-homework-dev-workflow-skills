# DevToys Base64 Encoder & Decoder 開發對話

- 日期：2026-11-04
- 語言：繁體中文
- 專案：`/Users/adrianli/Documents/GitHub/DevToys`
- 功能分支：`feat/base64-file-encoder-decoder`
- 後續修正工作區：`/private/tmp/devtoys-base64-worktree`
- 最終測試版：`/tmp/devtoys-base64-preview/DevToys Base64 Preview.app`
- 本機提交：`42c53ccf` — `feat(base64): add file encoding and decoding`
- 提交驗證：249 個測試通過；Base64 舊 stash 已移除

> 匯出範圍：本次對話中使用者與助理的文字訊息，包含進度更新、需求釐清及最終回覆，依對話順序排列。互動式提問的文字與選項一併保留。工具呼叫、原始工具輸出及畫面截圖未納入；系統與開發者指令不屬於本檔內容。使用者提供的三份技能附件收錄於文末。本次沿用先前匯出內容，補上 commit 與 stash 處理對話，記錄截至使用者提出「幫忙再把這次的對話 export」。

## 對話紀錄

### 使用者

$double-diamond-principle $git-conventional-commit $trunk-based-development 我們之前有為 DevToys 額外開發一些功能，我想新增一個 Base64 Encoder & Decoder 功能。目的是不想經常打開 guru base 64 進行相關操作。情境通常是我收到一端 Base 64 的文字，可能需要將它轉換為特定文件；或者是我收到一個檔案，看能不能將它加密為 Base 64 Text。大概是這樣

### 使用者：技能附件

依序提供以下三份技能的內容，全文見文末附錄：

1. `double-diamond-principle` — `/Users/adrianli/.agents/skills/double-diamond-principle/SKILL.md`
2. `git-conventional-commit` — `/Users/adrianli/.agents/skills/git-conventional-commit/SKILL.md`
3. `trunk-based-development` — `/Users/adrianli/.agents/skills/trunk-based-development/SKILL.md`

### 助理

我會先查看 DevToys 既有的 Base64 工具、之前新增功能的方式，以及專案的開發規則，再依你提供的兩個情境完成實作與驗證。我會套用你指定的雙鑽石、Conventional Commits 和主幹開發技能。

這裡的檔案轉 Base64 屬於「編碼」，可以解碼還原，沒有加密效果；介面也會用這個名稱。

### 助理

目前找到的缺口是「一般檔案」：既有工具分成 Base64 文字與圖片，無法直接涵蓋 PDF、ZIP、Office 等檔案的原始位元組往返轉換。

我會在這個 DevToys fork 新增「Base64 檔案編碼／解碼」工具：貼上 Base64 後另存檔案，或選取檔案產生可複製的 Base64。解碼會還原原始內容；存成 `.pdf` 等副檔名時，內容本身也必須是該格式。

### 助理：互動式提問

你收到的 Base64 通常對應哪些檔案？這會影響預設檔名與驗證案例；我會先保留任意檔案的支援。

選項：

- PDF、Office 文件為主
- 圖片為主
- 各種檔案都有，例如 ZIP、PDF、圖片

### 使用者

各種檔案都有

### 使用者

不過通常是 PDF, txt, jpg 這些

### 助理

收到，我會以 PDF、TXT、JPG 為主要驗證案例，並保留任意檔案支援。介面會沿用 DevToys 的檔案選擇器和文字編輯器，讓你選檔後複製 Base64，或貼上 Base64 後直接另存，檔名與副檔名由你決定。

目前已從乾淨的 `main` 開出短期功能分支，也找到之前使用的 .NET 建置環境，正在跑基線測試。

### 助理

基線的 190 個測試已全部通過。核心流程已接上 DevToys 原生元件，接下來會驗證檔案往返後是否逐位元組一致，以及錯誤輸入、取消儲存和覆寫較短檔案的行為。

第一版會支援常見的換行 Base64、Base64url 和 `data:...;base64,` 前綴，並在介面標示 20 MiB 的檔案上限。

### 助理

目前 233 個測試全部通過，macOS 應用程式也已建置成功。測試涵蓋 PDF／TXT／JPG 與任意二進位資料往返、文字 BOM 和換行保留、大小限制，以及無效輸入不開啟儲存對話框。

我正在做最後的桌面操作確認，檢查工具能否被搜尋到，以及實際選檔、貼上和儲存是否順暢。

### 助理

桌面實測已確認：選取含中文、BOM 和換行的 TXT，轉成 Base64 再存回檔案後，內容與 SHA-256 完全一致；貼上無效 Base64 也會顯示錯誤，不會開啟儲存對話框。

功能已可在搜尋與「Encoders / Decoders」分類找到。我正在整理最後的變更與驗證結果。

### 助理

最後的連續操作測試發現一個介面問題：快速解碼失敗後，編輯器有機會停留在唯讀狀態。我已調整忙碌狀態的切換方式，會重新確認錯誤後能立刻修改內容再試。

### 助理：第一版交付

已完成，可開啟[測試版 DevToys](/Users/adrianli/Documents/GitHub/DevToys/bin/Release/AnyCPU/DevToys.MacOS/net8.0-macos/osx-arm64/DevToys.app)，搜尋 **Base64 File**。

- 選取任意檔案 → Base64，可複製或存成文字檔。
- 貼上 Base64 → 還原檔案，儲存時指定 `.pdf`、`.txt`、`.jpg` 等副檔名。
- 全程本機處理，單檔上限 **20 MiB**，含繁體中文介面。

**233 個測試通過**，macOS 建置與桌面操作驗證完成；Windows／Linux 尚未實機驗證。

變更位於 `feat/base64-file-encoder-decoder`，尚未 commit／push。建議提交訊息：`feat(base64): add file encoding and decoding`。

### 使用者

幫我開一下測試版 DevToys，進行人工測試

### 助理

我會開啟剛才建置的測試版，讓你直接操作 Base64 工具。

### 助理

測試版 DevToys 已開啟，目前在 **Base64 File Encoder / Decoder** 頁面，可以直接進行人工測試。

### 使用者

目前我輸入了一段 Base64 的圖片檔案，Decoder 不會幫我將它儲存成圖片。這不對吧

### 助理

對，這個情境應該能直接存成圖片。目前的流程要你自行補上副檔名，確實不夠完整。我會先確認你現在遇到的儲存行為，再補上圖片格式辨識與對應的儲存預設值，也一併處理 PDF。

### 助理

找到原因了：目前解碼後一律使用「任意檔案」儲存，沒有辨識 PNG／JPEG，也沒有預填副檔名，所以可能存成系統不認得的無副檔名檔案。

我會改成依解碼內容辨識格式，預填像 `decoded.png`、`decoded.jpg` 或 `decoded.pdf` 的檔名。這次也會直接驗證「按儲存後能被辨識為圖片」，補上前一版只驗證位元組一致所漏掉的使用流程。

### 助理

格式辨識與預設檔名已完成，新增測試也已通過：PNG、JPEG、GIF、BMP、TIFF、WebP、PDF 和常見文字編碼都會帶入對應副檔名；即使 data URL 標示錯誤，也會以實際內容判斷。

目前共 249 個測試通過。我正在重新建置測試版，接著會用原生儲存視窗確認預填檔名及圖片開啟結果。

### 助理

原生視窗實測已成功：直接按儲存，會產生 `decoded.png`，macOS 能正常讀取為圖片，內容也與原圖完全一致。

我再補了兩個顯示細節：儲存視窗明確顯示副檔名，成功提示也會顯示實際檔名。新版會以「DevToys Base64 Preview」開啟，方便你辨識。

### 助理：修正版交付

對，前一版漏了格式辨識與副檔名處理。已修正，並開啟[新版 DevToys Base64 Preview](</tmp/devtoys-base64-preview/DevToys Base64 Preview.app>)。

現在貼上圖片 Base64，按 **Decode and save file**，會自動辨識格式並預填 `decoded.png`、`decoded.jpg` 等檔名。

已實測成功存成可讀取的 PNG，內容與原圖一致；249 個測試通過。你可以用剛才那段 Base64 再試一次。

### 使用者

目前 OK 了，你將這次的對話 export 成 markdown，然後告知檔案在哪

### 上次匯出交付（摘要）

助理已建立 Markdown 匯出檔，並提供以下檔案位置：

`/Users/adrianli/Documents/devtoys-base64-encoder-decoder-conversation-2026-11-04.md`

> 此段保留上次交付事實，並非逐字重建當時回覆。以下接續後續對話。

### 使用者

exit

### 使用者

幫我處理一下這次 base 64 的 commit 和 stash.

### 助理

我會沿用 Conventional Commits 與主幹開發流程，先確認 Base64 的工作區、暫存內容和 stash，提交完整功能與修正，再清理已被提交涵蓋的 stash。

### 助理

Base64 的完整修改保存在獨立工作區，stash 裡是較早的版本；目前的圖片副檔名修正也會一起提交。主幹後來只更新了 CI 設定，我會先同步這些更新，再跑測試並清理舊 stash。

### 助理

249 個測試全部通過，已確認這次提交只包含 Base64 相關的 14 個檔案。接著會建立 `feat(base64): add file encoding and decoding`，確認提交完整後移除已被涵蓋的舊 stash。

### 助理：commit 與 stash 處理完成

已處理完成：

- Commit：`42c53ccf` — `feat(base64): add file encoding and decoding`
- 分支：`feat/base64-file-encoder-decoder`
- 包含完整 Base64 功能與圖片儲存修正。
- 已移除舊 Base64 stash，目前 stash 清單為空。
- 249 個測試通過，工作區乾淨。

目前已完成本機提交，尚未 push 或合併至 main。

### 使用者

幫忙再把這次的對話 export

## 附錄：技能附件

以下保留本次使用的三份 `SKILL.md` 內容。

### double-diamond-principle

來源：`/Users/adrianli/.agents/skills/double-diamond-principle/SKILL.md`

````markdown
---
name: double-diamond-principle
description: "跨專案運用 Design Council 雙鑽石，探索與界定問題、發展不同方案，透過原型與小規模測試收斂及迭代。適用於使用者要求 Double Diamond、釐清未定義挑戰、設計產品／服務／流程或檢視創新決策；不因單純美化、文案修改或解法已明確的小修正而啟動完整研究流程。"
---

# Double Diamond Principle

以 Design Council 的 [Framework for Innovation](https://www.designcouncil.org.uk/resources/framework-for-innovation/) 為依據，運用兩次發散與收斂：先找清楚要處理的問題，再找出值得採用的解法。四階段是 Discover、Define、Develop、Deliver；可以回返、交疊，也可用早期原型探索問題。

適用於軟體、實體產品、公共／商業服務、組織流程與其他創新任務。不預設產物必須是 app、網站、功能清單或程式碼，也不綁定產業、repo、工具或文件格式。

## 啟動：依證據選擇切入點

先讀本專案可用的 brief、研究、現行流程、原型、測試結果與適用工作規則，確認目標對象、希望改善的結果、限制、期限及本次交付範圍。已有資料可回答的事不重問；只釐清會影響下一個決策的未知，同時推進不依賴答案的工作。

| 目前最重要的不確定性 | 優先工作 |
| --- | --- |
| 不知道誰遇到什麼問題，或只有要求的功能 | Discover：檢查現況與人們的需要 |
| 研究不少，但問題與優先順序仍不清楚 | Define：綜合證據並形成工作版問題定義 |
| 問題有依據，尚未決定如何解決 | Develop：比較不同機制的方案與原型 |
| 已有候選解法，需要判斷效果與交付方式 | Deliver：小規模驗證、改進、採用或捨棄 |

這是工作選擇，不是強制階段簽核。沿用有效的既有研究與決策；出現反證時回到相關問題。依 [適用條件與例外](references/adaptation.md) 調整深度與工期，不必每個任務都跑完四階段。

## 四階段工作

### Discover／探索：擴大對問題的理解

- 釐清受影響的人、使用情境、目前如何完成事情、遇到的障礙，以及可利用的能力與資源。納入實際使用者、未能使用者與交付服務的人，依問題選擇研究對象。
- 先利用已有證據，必要時規劃觀察、訪談、日誌、資料分析或次級研究；方法選擇見 [方法參考](references/methods.md)。尚未獲得的訪談／測試資料只能列為研究計畫。
- 區分直接觀察、當事人說法、數據、推論與假設，保留來源和涵蓋範圍。保留相互矛盾與少數人的經驗，不把利害關係人的偏好自動當成使用者需要。
- 產出足以供下一個決策使用的問題線索與證據缺口；缺資料時交付可檢驗的假設與最有價值的下一步，不虛構人物訪談、需求頻率或「已驗證」結論。

### Define／界定：收斂到值得處理的挑戰

- 將線索分群，連結行為、情境與需要，區分表面症狀和仍待驗證的成因。每個重要洞察應能追溯到證據，並標示限制。
- 寫出精簡工作 brief：為誰、在什麼情境、要改善什麼、依據為何、範圍與限制、如何判斷改善。只有能提升討論品質時才轉成「我們如何能……」問題，避免在問題句中預先綁定解法。
- 設定選擇方案與後續驗證的準則，例如人的受益、可及性、執行可行性、成本與持續營運；依專案選取，不套用固定權重。
- 找到足以指引下一步的共同焦點即可繼續。證據不足時明示是暫定 brief，並把可能推翻它的條件帶入下一階段。

### Develop／發展：探索不同解法

- 針對已界定的挑戰產生有實質差異的方案；差異可來自服務、流程、資訊、空間或技術，而不只是同一方案的外觀。使用者已選定且要求實作的路線，應在該範圍內發展，除非新證據顯示關鍵前提失效。
- 先擴大可能性，再按已知準則比較。與受影響者和交付者共同設計；無法直接參與時標明替代方式與其限制。
- 為關鍵未知選擇最低足夠精度的原型，例如草圖、情境演練、服務藍圖、紙本模型或互動樣本。原型要回答明確問題，不因看起來完成就視為成效已獲證明。
- 留下候選方案、取捨理由、關鍵假設及下一個測試。若測試動搖問題定義，回到 Define／Discover，而非只修飾原型。

### Deliver／交付與驗證：以小規模證據收斂

- 測試前說清楚要驗證的假設、對象與情境、觀察方式、成功／停止條件；缺少基線或門檻時標為待確認的提案，不偽稱是既有目標。
- 依任務範圍執行或準備小規模試驗，記錄結果、失敗與未涵蓋情境。結果可支持採用、修改、另試方案、回返問題或停止；不要求一定推出原先偏好的解法。
- 測試有利時逐步擴大，並確認服務交付、維護責任、實際使用環境與後續回饋。小樣本可發現問題，不自動證明整體人口效果或因果關係。
- 完成使用者已要求且獲授權的設計、原型、修改或交付；不要永遠停在方法介紹。研究計畫、模擬結果、使用者驗證、正式發布是不同狀態，分別回報。

四階段的原義見 [The Double Diamond](https://www.designcouncil.org.uk/resources/the-double-diamond/)；以上證據記錄、brief 與測試決策是本 skill 的操作化方式。

## 貫穿全程的原則與條件

- **以人為先**：理解需要、優勢與期望；依實際情境評估誰會受益、誰可能被排除。
- **視覺化且包容地溝通**：用適合對象的語言、草圖、流程或表格建立共同理解；圖像提供文字說明，不以術語或精美外觀取代證據。
- **協作與共同創造**：辨識使用者、實際執行者、合作方與決策者的角色，保留重要分歧。代理模擬或單人腦力激盪不等於已完成共同設計。
- **持續迭代**：根據新學習調整問題、方案或實施方式；每輪聚焦最重要的未知，避免無止境研究或只重做外觀。

領導支持與參與關係是框架的支撐條件：需要有人能協調資源、允許試驗與採取結果，也要讓交付及使用成果的人有參與方式。它們不是額外的固定階段。[官方原則與文化條件](https://www.designcouncil.org.uk/resources/framework-for-innovation/)

## 任務邊界與交付

依既有授權直接推進，不為每個階段另設 approval。規劃研究不自動包含聯絡受訪者、發問卷、支出、改正式流程或發布；需要這些動作時先完成可審查的材料，再確認尚缺的授權。

交付使用者需要的成果，附最少但足夠的決策脈絡：目前問題／方案、證據與假設、選擇理由、驗證狀態和下一個行動。沿用專案既有文件格式，不必建立全套模板、工作坊或新工具。來源查核、歷史方法與當前框架的差異見 [來源索引](references/sources.md)。
````

### git-conventional-commit

來源：`/Users/adrianli/.agents/skills/git-conventional-commit/SKILL.md`

````markdown
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
````

### trunk-based-development

來源：`/Users/adrianli/.agents/skills/trunk-based-development/SKILL.md`

````markdown
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
````
