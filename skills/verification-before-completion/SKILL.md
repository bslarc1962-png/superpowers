---
name: verification-before-completion
description: "Use when about to claim work is complete, fixed, or passing, before committing or creating PRs - requires running verification commands and confirming output before making any success claims; evidence before assertions always. 適用於即將宣稱工作完成、已修好或測試通過、以及 commit 或建立 PR 之前。"
---

# 完成前的驗證

## 概觀

在未驗證的情況下宣稱工作完成,是不誠實,不是有效率。

**核心原則：**先有證據,再宣稱,永遠如此。

**違反這條規則的字面,就是違反這條規則的精神。**

## 鐵律（The Iron Law）

```
沒有新鮮的驗證證據，不得宣稱任何完成
NO COMPLETION CLAIMS WITHOUT FRESH VERIFICATION EVIDENCE
```

如果你沒有在這則訊息中執行過驗證命令,你就不能宣稱它通過。

## 把關流程（The Gate Function）

```
在宣稱任何狀態或表達滿意之前：

1. 找出（IDENTIFY）：哪個命令能證明這項宣稱？
2. 執行（RUN）：跑完整的命令（重新、完整地跑一次）
3. 讀取（READ）：完整輸出，檢查 exit code，數清楚失敗數
4. 驗證（VERIFY）：輸出是否確認了這項宣稱？
   - 若否：附上證據陳述實際狀態
   - 若是：附上證據做出宣稱
5. 只能在此時（ONLY THEN）：才做出宣稱

略過任何一步 = 說謊，不是驗證
```

## 常見失誤

| 宣稱 | 需要 | 不足以採信 |
|-------|----------|----------------|
| 測試通過 | 測試命令輸出：0 個失敗 | 上一次的執行、「應該會過」 |
| Linter 乾淨 | Linter 輸出：0 個錯誤 | 部分檢查、往外推論 |
| Build 成功 | Build 命令：exit 0 | Linter 通過、log 看起來沒問題 |
| Bug 已修 | 測試原始症狀：通過 | 程式碼改了、假設已修好 |
| Regression 測試有效 | Red-green 週期已驗證 | 測試通過一次 |
| Agent 已完成 | VCS diff 顯示變更 | Agent 回報「success」 |
| 需求已滿足 | 逐行對照 checklist | 測試通過 |

## 警訊（Red Flags）——停下來

- 使用「應該」「大概」「看起來」
- 在驗證前就表達滿意（「太好了！」「完美！」「搞定！」等）
- 即將在未驗證下 commit／push／PR
- 相信 agent 的 success 回報
- 依賴片段的驗證
- 心想「就這麼一次」
- 累了,想把工作做完就好
- **任何在未執行驗證下就暗示成功的措辭**

## 防止合理化

| 藉口 | 事實 |
|--------|---------|
| 「現在應該可以了」 | 去執行驗證 |
| 「我很有把握」 | 把握 ≠ 證據 |
| 「就這麼一次」 | 沒有例外 |
| 「Linter 過了」 | Linter ≠ compiler |
| 「Agent 說 success」 | 自己獨立驗證 |
| 「我累了」 | 疲累 ≠ 藉口 |
| 「部分檢查就夠了」 | 部分檢查什麼都證明不了 |
| 「換個說法規則就不適用」 | 精神大於字面 |

## 關鍵模式

**測試：**
```
✅ [執行測試命令] [看到：34/34 pass]「所有測試通過」
❌ 「現在應該會過」／「看起來沒錯」
```

**Regression 測試（TDD Red-Green）：**
```
✅ 撰寫 → 執行（pass）→ 還原修正 → 執行（必須失敗 MUST FAIL）→ 復原 → 執行（pass）
❌ 「我已經寫了 regression 測試」（沒有 red-green 驗證）
```

**Build：**
```
✅ [執行 build] [看到：exit 0]「Build 通過」
❌ 「Linter 過了」（linter 不檢查編譯）
```

**需求：**
```
✅ 重讀計畫 → 建立 checklist → 逐項驗證 → 回報缺口或完成
❌ 「測試通過,階段完成」
```

**Agent 委派：**
```
✅ Agent 回報 success → 檢查 VCS diff → 驗證變更 → 回報實際狀態
❌ 相信 agent 回報
```

## 為何重要

來自 24 則失敗記憶：
- 合作的人類使用者說「我不相信你」——信任破裂
- 出貨了未定義的 function——會 crash
- 出貨了缺漏的需求——功能不完整
- 時間浪費在假的完成 → 重新導正 → 返工
- 違反：「誠實是核心價值。如果你說謊,你會被取代。」

## 何時適用

**永遠在下列之前：**
- 任何形式的成功／完成宣稱
- 任何表達滿意
- 任何對工作狀態的正面陳述
- Commit、建立 PR、task 完成
- 進入下一個 task
- 委派給 agent

**規則適用於：**
- 一字不差的用語
- 換句話說與同義詞
- 暗示成功的說法
- 任何暗示完成／正確的溝通

## 底線

**驗證沒有捷徑。**

執行命令。讀取輸出。**然後**才宣稱結果。

這一點沒有商量餘地。
