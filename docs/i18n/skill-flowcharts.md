# Skill 流程圖（Mermaid 對照）

> **這是一份「在地化 review 輔助」，不是 canonical 來源。**
>
> 每個 skill 的流程圖真理來源，是它 `SKILL.md` 裡的 ` ```dot `(Graphviz)區塊——那才是 agent 執行時讀到的內容,也是 `skills/writing-skills/render-graphs.js` 會 render 的對象。GitHub **不會**自動渲染 ` ```dot `，所以本頁把同樣的流程用 **Mermaid** 重畫一遍，好讓在 GitHub 上 review 的中文使用者能直接看到圖。
>
> 這些 Mermaid 圖是**便利檢視用**：節點文字譯成中文以利吸收，但 skill ID、檔案路徑、技術術語（`RED`／`GREEN`／`REFACTOR`、`setTimeout`／`sleep`、`defense-in-depth` 等）維持原文。若本頁與某個 `SKILL.md` 的 ` ```dot ` 有出入,**以 `SKILL.md` 為準**。canonical skill 不因本頁而有任何改動。

## 目錄

- [brainstorming —— 設計打磨流程](#brainstorming)
- [writing-skills —— 何時用流程圖](#writing-skills)
- [test-driven-development —— RED-GREEN-REFACTOR 循環](#test-driven-development)
- [subagent-driven-development —— 何時使用](#subagent-driven-development-when)
- [subagent-driven-development —— 執行流程](#subagent-driven-development-process)
- [dispatching-parallel-agents —— 何時使用](#dispatching-parallel-agents)
- [systematic-debugging / root-cause-tracing —— 何時使用](#root-cause-tracing-when)
- [systematic-debugging / root-cause-tracing —— 原則](#root-cause-tracing-principle)
- [systematic-debugging / condition-based-waiting —— 何時使用](#condition-based-waiting)

---

<a id="brainstorming"></a>
## brainstorming —— 設計打磨流程

Canonical:[`skills/brainstorming/SKILL.md`](../../skills/brainstorming/SKILL.md)

```mermaid
flowchart TD
    a["探索專案脈絡"] --> b["提出釐清問題"]
    b --> c["提出 2–3 種方案"]
    c --> d["分段呈現設計"]
    d --> e{"使用者核可設計?"}
    e -->|否,修改| d
    e -->|是| f["撰寫設計文件"]
    f --> g["spec 自我審查(就地修正)"]
    g --> h{"使用者審查 spec?"}
    h -->|要求修改| f
    h -->|核可| i(["調用 writing-plans skill"])
    class i terminal
    classDef terminal fill:#cfe8cf,stroke:#4a4;
```

---

<a id="writing-skills"></a>
## writing-skills —— 何時用流程圖

Canonical:[`skills/writing-skills/SKILL.md`](../../skills/writing-skills/SKILL.md)

```mermaid
flowchart TD
    a{"需要呈現資訊?"} -->|是| b{"是我可能出錯的決策點?"}
    b -->|是| c["小的 inline 流程圖"]
    b -->|否| d["用 markdown"]
```

---

<a id="test-driven-development"></a>
## test-driven-development —— RED-GREEN-REFACTOR 循環

Canonical:[`skills/test-driven-development/SKILL.md`](../../skills/test-driven-development/SKILL.md)

```mermaid
flowchart LR
    r["RED<br/>寫會失敗的測試"] --> vr{"驗證正確地失敗"}
    vr -->|是| g["GREEN<br/>最小程式碼"]
    vr -->|錯誤的失敗| r
    g --> vg{"驗證通過、全綠"}
    vg -->|是| rf["REFACTOR<br/>清理"]
    vg -->|否| g
    rf -->|維持綠| vg
    vg --> nx(["下一個"])
    nx --> r
    class r red
    class g grn
    class rf blu
    classDef red fill:#ffcccc,stroke:#c66;
    classDef grn fill:#ccffcc,stroke:#6c6;
    classDef blu fill:#ccccff,stroke:#66c;
```

---

<a id="subagent-driven-development-when"></a>
## subagent-driven-development —— 何時使用

Canonical:[`skills/subagent-driven-development/SKILL.md`](../../skills/subagent-driven-development/SKILL.md)

```mermaid
flowchart TD
    a{"有實作計畫?"} -->|是| b{"任務大致獨立?"}
    a -->|否| m["手動執行或先 brainstorm"]
    b -->|是| c{"留在這段 session?"}
    b -->|否,緊密耦合| m
    c -->|是| d["subagent-driven-development"]
    c -->|否,平行 session| e["executing-plans"]
```

---

<a id="subagent-driven-development-process"></a>
## subagent-driven-development —— 執行流程

Canonical:[`skills/subagent-driven-development/SKILL.md`](../../skills/subagent-driven-development/SKILL.md)

```mermaid
flowchart TD
    p0["讀計畫,記下脈絡與全域約束,建立 todos"] --> disp

    subgraph pertask["每個任務(Per Task)"]
        disp["派發 implementer subagent<br/>(./implementer-prompt.md)"] --> ask{"implementer subagent 提問?"}
        ask -->|是| ans["回答問題、提供脈絡"]
        ans --> disp
        ask -->|否| impl["implementer subagent 實作、測試、commit、自我審查"]
        impl --> diff["寫 diff 檔,派發 task reviewer subagent<br/>(./task-reviewer-prompt.md)"]
        diff --> rev{"task reviewer 回報 spec ✅ 且品質核可?"}
        rev -->|否| fix["為 Critical/Important 問題派發 fix subagent"]
        fix -->|重新審查| diff
        rev -->|是| done["在 todo list 與進度 ledger 標記任務完成"]
    end

    done --> more{"還有任務?"}
    more -->|是| disp
    more -->|否| final["派發最終 code reviewer subagent<br/>(../requesting-code-review/code-reviewer.md)"]
    final --> fin(["使用 superpowers:finishing-a-development-branch"])
    class fin terminal
    classDef terminal fill:#ccffcc,stroke:#4a4;
```

---

<a id="dispatching-parallel-agents"></a>
## dispatching-parallel-agents —— 何時使用

Canonical:[`skills/dispatching-parallel-agents/SKILL.md`](../../skills/dispatching-parallel-agents/SKILL.md)

```mermaid
flowchart TD
    a{"多個失敗?"} -->|是| b{"它們彼此獨立?"}
    b -->|否,相關| c["單一 agent 調查全部"]
    b -->|是| d{"它們能並行嗎?"}
    d -->|是| e["並行派發"]
    d -->|否,共享狀態| f["循序 agent"]
    g["每個問題領域一個 agent"]
    %% 註:此節點在 canonical .dot 中已定義但無連線(orphan),此處如實保留。
```

---

<a id="root-cause-tracing-when"></a>
## systematic-debugging / root-cause-tracing —— 何時使用

Canonical:[`skills/systematic-debugging/root-cause-tracing.md`](../../skills/systematic-debugging/root-cause-tracing.md)

```mermaid
flowchart TD
    a{"bug 出現在 stack 深處?"} -->|是| b{"能往回追?"}
    b -->|是| c["追到最初的觸發點"]
    b -->|否,死路| d["在症狀點修"]
    c --> e["更好:再加 defense-in-depth"]
```

---

<a id="root-cause-tracing-principle"></a>
## systematic-debugging / root-cause-tracing —— 原則

Canonical:[`skills/systematic-debugging/root-cause-tracing.md`](../../skills/systematic-debugging/root-cause-tracing.md)

```mermaid
flowchart TD
    a(["找到直接原因"]) --> b{"能再往上追一層?"}
    b -->|是| c["往回追"]
    b -->|否| x{{"絕不只修症狀"}}
    c --> d{"這是源頭嗎?"}
    d -->|否,繼續| c
    d -->|是| e["在源頭修"]
    e --> f["在每一層加驗證"]
    f --> g(["bug 不可能發生"])
    class x danger
    class g terminal
    classDef danger fill:#e23,stroke:#a00,color:#fff;
    classDef terminal fill:#ccffcc,stroke:#4a4;
```

---

<a id="condition-based-waiting"></a>
## systematic-debugging / condition-based-waiting —— 何時使用

Canonical:[`skills/systematic-debugging/condition-based-waiting.md`](../../skills/systematic-debugging/condition-based-waiting.md)

```mermaid
flowchart TD
    a{"測試用了 setTimeout/sleep?"} -->|是| b{"在測試時序行為?"}
    b -->|是| c["記錄為何需要 timeout"]
    b -->|否| d["用 condition-based waiting"]
```
