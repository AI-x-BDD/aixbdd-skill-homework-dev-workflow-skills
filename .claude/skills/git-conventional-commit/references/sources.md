# 來源與閱讀路徑

查閱日期：2026-11-01。主基準：Conventional Commits **1.0.0 英文版**；版本語意補充：SemVer **2.0.0**。工具文件為查閱當日內容，使用 skill 時仍須依 repo 的安裝版本與設定核對。

## 主規範與網站內連結

| 來源 | 已閱讀範圍與用途 |
| --- | --- |
| [Conventional Commits 1.0.0](https://www.conventionalcommits.org/en/v1.0.0/) | 完整 Summary、Examples、16 條 Specification、Why Use、FAQ 與授權；格式、語意、breaking、大小寫、自訂 type、拆分、squash、revert、錯誤修正的主要依據 |
| [繁體中文版 1.0.0](https://www.conventionalcommits.org/zh-hant/v1.0.0/) | 比對術語與概述；該頁額外推薦 `improvement`，與指定英文版不完全一致，因此不把它列為標準要求 |
| [About](https://www.conventionalcommits.org/en/about/) | 閱讀規範起源與工具分類；沿此頁進入 semantic-release。列於工具清單不代表本 skill 建議安裝 |
| [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119.txt) | 主頁 Specification 連出；讀 §1–6，釐清 MUST、SHOULD、MAY 的約束程度 |
| [CC BY 3.0](https://creativecommons.org/licenses/by/3.0/) | 主頁 License 連出；核對改寫與署名條件 |

## 沿主規範延伸的官方資料

| 來源 | 路徑與採用內容 |
| --- | --- |
| [SemVer 2.0.0](https://semver.org/spec/v2.0.0.html) | CC Summary → SemVer；讀規範與初始開發 FAQ，採用 public API、0.x、相容性、deprecated 與版本不得原地修改的原則 |
| [Git interpret-trailers](https://git-scm.com/docs/git-interpret-trailers) | CC Summary／Specification → git trailer format；讀 Description、Other rules 與 parse 選項，區分 Git trailer 和 CC footer |
| [Git commit](https://git-scm.com/docs/git-commit) | Git trailers 文件導覽 → commit；讀 Description、`-F`、cleanup、amend、only、dry-run 與 hooks，用於 index／訊息檔／改寫的操作說明 |
| [Angular Commit Message Guidelines（固定 revision）](https://github.com/angular/angular/blob/22b96b96902e1a42ee8c5e807720424abad3082a/CONTRIBUTING.md#commit) | CC Summary → Angular convention；讀 Commit Message Guidelines 的 Format、Type、Scope、Subject、Body、Footer、Revert。固定在主站引用的 revision，僅作慣例來源，不代表 Angular 當前政策 |
| [commitlint config-conventional](https://github.com/conventional-changelog/commitlint/tree/master/%40commitlint/config-conventional) | CC Summary → config-conventional；讀 README 的 Beyond the specification 與 Rules，區別標準和 preset 額外限制 |
| [commitlint Rules](https://commitlint.js.org/reference/rules) | config-conventional README → Rules reference；查閱長度、type／scope 與 breaking-change-exclamation-mark 規則，確認工具可採用更嚴格的條件 |
| [semantic-release](https://github.com/semantic-release/semantic-release#commit-message-format) | CC About → semantic-release；讀 Commit message format 與 CI／觸發說明，確認工具會依設定分析並可能執行發版 |
| [semantic-release commit-analyzer](https://github.com/semantic-release/commit-analyzer#releaserules) | semantic-release → commit-analyzer；讀 Configuration、releaseRules、Default rules matching、Multiple commits，確認 parser／preset 與版本判定可配置 |

## 整理原則與歸屬

規範相關段落依 Conventional Commits contributors 發布的 *Conventional Commits 1.0.0* 整理，原文採 [Creative Commons Attribution 3.0 Unported](https://creativecommons.org/licenses/by/3.0/) 授權。本 skill 將內容改寫為繁體中文決策表與工作流程，合併條款、補充官方交叉資料，並自行編寫示例；不是官方逐字譯本，也不代表原作者背書。主規範與原始授權連結保留供核對。

證據優先、分開檢查語法／語意／工具、保留部分暫存、控制提交範圍與交付格式，是本 skill 的操作設計；不要宣稱它們是 CC 條文。Angular、commitlint 與 release 工具的政策只適用於採用它們的專案。

需要更新時，先核對固定版本的規範，再看 repo 使用的工具版本及設定；若翻譯、範例、歷史慣例與英文規範不一致，清楚標示差異，不把多方要求默默合併成新標準。
