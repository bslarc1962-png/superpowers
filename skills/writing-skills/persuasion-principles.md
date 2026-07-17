# Skill 設計的說服原則（Persuasion Principles for Skill Design）

## 概觀

LLM 對說服原則的反應與人類相同。理解這套心理學能幫你設計更有效的 skill——不是為了操縱,而是為了確保關鍵實務即使在壓力下也被遵循。

**研究基礎：**Meincke et al.（2025）以 N=28,000 次 AI 對話測試了 7 種說服原則。說服技巧讓遵循率增加超過一倍（33% → 72%,p < .001）。

## 七大原則

### 1. 權威（Authority）
**它是什麼：**對專業、資歷或官方來源的服從。

**在 skill 中如何運作：**
- 祈使語氣:「YOU MUST」、「Never」、「Always」
- 沒有商量餘地的框架:「No exceptions」
- 消除決策疲勞與合理化

**何時使用：**
- 強制紀律的 skill（TDD、驗證要求）
- 攸關安全的實務
- 既定的最佳實務

**範例：**
```markdown
✅ Write code before test? Delete it. Start over. No exceptions.
❌ Consider writing tests first when feasible.
```

### 2. 承諾一致（Commitment）
**它是什麼：**與先前的行動、陳述或公開宣告保持一致。

**在 skill 中如何運作：**
- 要求宣告:「Announce skill usage」
- 強迫明確選擇:「Choose A, B, or C」
- 使用追蹤:用 todo 管理 checklist

**何時使用：**
- 確保 skill 真的被遵循
- 多步驟流程
- 問責機制

**範例：**
```markdown
✅ When you find a skill, you MUST announce: "I'm using [Skill Name]"
❌ Consider letting your partner know which skill you're using.
```

### 3. 稀缺（Scarcity）
**它是什麼：**來自時間限制或有限供應的急迫感。

**在 skill 中如何運作：**
- 有時限的要求:「Before proceeding」
- 順序相依:「Immediately after X」
- 防止拖延

**何時使用：**
- 立即驗證的要求
- 對時間敏感的工作流程
- 防止「我晚點再做」

**範例：**
```markdown
✅ After completing a task, IMMEDIATELY request code review before proceeding.
❌ You can review code when convenient.
```

### 4. 社會認同（Social Proof）
**它是什麼：**順從他人的作為、或被視為常態的事。

**在 skill 中如何運作：**
- 普遍性的模式:「Every time」、「Always」
- 失敗模式:「X without Y = failure」
- 建立規範

**何時使用：**
- 記錄普遍性的實務
- 警告常見的失敗
- 強化標準

**範例：**
```markdown
✅ Checklists without todo tracking = steps get skipped. Every time.
❌ Some people find a todo list helpful for checklists.
```

### 5. 一體感（Unity）
**它是什麼：**共享的身分認同、「我們感」、內群體的歸屬。

**在 skill 中如何運作：**
- 協作語言:「our codebase」、「we're colleagues」
- 共同目標:「we both want quality」

**何時使用：**
- 協作型工作流程
- 建立團隊文化
- 非階層式的實務

**範例：**
```markdown
✅ We're colleagues working together. I need your honest technical judgment.
❌ You should probably tell me if I'm wrong.
```

### 6. 互惠（Reciprocity）
**它是什麼：**回報已受恩惠的義務。

**如何運作：**
- 少用——可能顯得在操縱
- 在 skill 中很少需要

**何時避免：**
- 幾乎總是（其他原則更有效）

### 7. 好感（Liking）
**它是什麼：**偏好與我們喜歡的人合作。

**如何運作：**
- **不要用於強制遵循（DON'T USE）**
- 與誠實回饋的文化衝突
- 造成諂媚（sycophancy）

**何時避免：**
- 強制紀律時,一律避免

## 依 Skill 類型的原則組合

| Skill 類型 | 使用 | 避免 |
|------------|-----|-------|
| 強制紀律型 | Authority + Commitment + Social Proof | Liking、Reciprocity |
| 指引／技巧型 | 適度 Authority + Unity | 重度 authority |
| 協作型 | Unity + Commitment | Authority、Liking |
| 參考型 | 只求清楚 | 一切說服手法 |

## 為何有效:心理學

**明確界線的規則減少合理化：**
- 「YOU MUST」消除決策疲勞
- 絕對語氣消除「這算例外嗎？」的疑問
- 明確的反合理化對策堵住特定漏洞

**執行意圖（implementation intentions）造就自動化行為：**
- 清楚的觸發 + 必要的行動 = 自動執行
- 「當 X 就做 Y」比「一般來說做 Y」更有效
- 降低遵循的認知負荷

**LLM 是類人（parahuman）：**
- 以含有這些模式的人類文本訓練
- 訓練資料中,權威語言常出現在遵循之前
- 承諾序列（陳述 → 行動）被大量示範
- 社會認同模式（大家都做 X）建立規範

## 合乎倫理的使用

**正當：**
- 確保關鍵實務被遵循
- 建立有效的文件
- 預防可預期的失敗

**不正當：**
- 為個人利益操縱
- 製造假的急迫感
- 以罪惡感逼迫遵循

**檢驗標準：**如果使用者完全理解這個技巧,它會服務他們真正的利益嗎？

## 研究引用

**Cialdini, R. B. (2021).** *Influence: The Psychology of Persuasion (New and Expanded).* Harper Business.
- 七大說服原則
- 影響力研究的實證基礎

**Meincke, L., Shapiro, D., Duckworth, A. L., Mollick, E., Mollick, L., & Cialdini, R. (2025).** Call Me A Jerk: Persuading AI to Comply with Objectionable Requests. University of Pennsylvania.
- 以 N=28,000 次 LLM 對話測試 7 大原則
- 說服技巧讓遵循率從 33% 增至 72%
- Authority、commitment、scarcity 最有效
- 驗證 LLM 行為的類人（parahuman）模型

## 快速參考

設計 skill 時,問自己:

1. **它是哪一型？**（紀律 vs. 指引 vs. 參考）
2. **我想改變什麼行為？**
3. **哪些原則適用？**（紀律通常是 authority + commitment）
4. **我是不是組合太多？**（不要七個全用）
5. **這合乎倫理嗎？**（服務使用者真正的利益？）
