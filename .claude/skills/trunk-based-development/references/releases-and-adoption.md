# 發布、修補與漸進導入

## 發布模式

共同開發主幹可以維持同一條，而發布方式依產品需求選擇：

| 模式 | 適用條件 | 應保留的事實 |
| --- | --- | --- |
| 從主幹發布 | 可頻繁交付、以小步修正向前推進 | 已驗證的來源 SHA／tag、產物與配置、必要 gate |
| 按需切 release branch | 需維護已發布版本、認證窗口或不同發布節奏 | 來源主幹 SHA／tag、支持範圍、允許 backport 的修復與獨立 CI |

兩者均由 [Release from trunk](https://trunkbaseddevelopment.com/release-from-trunk/) 與 [Branch for release](https://trunkbaseddevelopment.com/branch-for-release/) 描述。release branch 是選定主幹狀態的維護線，不能變成持續接收一般功能的第二開發主幹。可從過去已確認的 tag／commit 按需建立，不必一定來自目前 HEAD。

**可發布、部署與功能開放不同。** 維持可發布不要求每筆提交自動上正式環境；部署可由其他 gate 控制，旗標啟用也可另有時機。[Continuous Delivery 與 Continuous Deployment](https://trunkbaseddevelopment.com/continuous-delivery/)、[Feature flags](https://trunkbaseddevelopment.com/feature-flags/)

## 修復與 backport

一般路徑是「在主幹重現 → 加回歸驗證並修復 → 主幹驗證 → 選取必要修復到受影響版本 → 該版本再驗證」。保留原修復與 backport 的對應記錄，因為不同分支的環境與依賴可能不同。

不把整條主幹 merge 到凍結功能範圍的 release branch，也不在版本分支間輪流交換一般開發內容。cherry-pick 前檢查修復所需的依賴與版本差異，不能只因沒有文字衝突就宣稱成功。來源見 [Branch for release 的 Fix production bugs on Trunk](https://trunkbaseddevelopment.com/branch-for-release/#fix-production-bugs-on-trunk)。

**主幹無法重現的例外**：若缺陷僅存在受支持的舊版本，可以先在該版本重現與修復；同時確認主幹是否已被其他變更修正、是否仍需等效修復或回歸測試，並記錄理由。這是網站明示的例外，不能一律拒絕 release-only 修復，也不能忘掉後續主幹評估。[Cherry-picks from the trunk to branch ONLY](https://trunkbaseddevelopment.com/branch-for-release/#cherry-picks-from-the-trunk-to-branch-only)

backport、tag、release 發布及生產部署是不同動作，依本次授權執行。普通修補任務不包含自動重寫已發布歷史或移動 tag。

## 維護分支的結束

依產品支持政策結束維護，保留必要 tag 與歷史。release branch 不套用 feature branch 的一至兩天壽命；支持數月的舊版本可能合理。新功能仍持續整合到主幹，舊版本只接收政策允許的維護。

當不再需要該版本的後續發布，才在授權範圍內清理維護分支。沒有獨有變更需要帶回主幹時，無須在結束時整條 merge back。不要只因名字像 `release/*` 或日期較舊就刪除。[You're doing it wrong 的 release branch 章節](https://trunkbaseddevelopment.com/youre-doing-it-wrong/)

## 評估或導入既有專案

這一節是本 skill 綜合 [Deciding factors](https://trunkbaseddevelopment.com/deciding-factors/)、[Continuous Review](https://trunkbaseddevelopment.com/continuous-review/) 與 [You're doing it wrong](https://trunkbaseddevelopment.com/youre-doing-it-wrong/) 提出的操作方法，並非原站固定遷移程序。

1. **描述現況**：以分支／PR 的實際生命週期、整合頻率、review 等待、CI 耗時與失敗、發布來源及維護版本為證據。看不到的資料標示未知。
2. **辨識限制**：哪些是必要的產品支持、審查與環境約束，哪些是可改善的批次過大、慢建置或等待流程。
3. **選最小試行範圍**：在一個 repo／元件或一條交付流程先試小切片、可信的 checks 與更短 review 等待，不同時替換 Git 平台、CI 與整個組織流程。
4. **逐步收斂**：先讓新工作進入已確定的整合點，將長期 feature work 改成可獨立落地的切片；要移轉既有 branch 職責時，列出 CI／PR base／發布設定與未完成工作如何銜接，不能先刪分支再找回內容。
5. **檢查效果**：看變更從開始到主幹的時間、review 等待、主幹回歸及恢復時間是否改善。不要只用分支數量或命名作為成功指標。

評估任務交付「觀察與來源 → 影響 → 最小改善 → 適用條件／例外 → 如何驗證」即可，不必每次產生整套治理文件。使用者要求實際導入時，先完成授權範圍內可審查的修改；遠端保護規則等變動須符合原有授權。

## 常見誤判

| 觀察 | 正確解讀與處理 |
| --- | --- |
| 分支叫 `main`，但功能數週才整合 | 名稱不能證明採用主幹開發；檢查批次與等待 |
| 每天把主幹 merge 進功能分支 | 只有單向同步；變更尚未與團隊共同整合 |
| PR 超過兩天但尚未審查 | 顯示 review／切片瓶頸，不能繞過必要 review |
| 分支 CI 通過但主幹已改變 | 需要新的整合候選驗證或平台 queue 的對等保證 |
| 大功能整合後預設關閉 | 若正常路徑可用且隔離有效，可符合逐步整合目標 |
| 支持多個 release branch | 未必違反原則；要看它們是版本維護還是多條一般開發線 |
| 直接主幹被禁止 | 使用短期 PR／MR；保護規則不是需要繞過的障礙 |
| 多個 repo／非 Git | 保留各 repo 的整合點與相容契約，使用該 VCS 流程；不要求搬成 monorepo |
| 外部開源 PR 等待較久 | 網站承認志願 review 的限制；仍可縮小變更並同步，不能控制維護者時程 |

最後一項的適用條件見 [Continuous Review 的 Open Source contributions](https://trunkbaseddevelopment.com/continuous-review/#open-source-contributions-via-prs)。其他判讀由上述原理與本 skill 的驗證方式組合而成。
