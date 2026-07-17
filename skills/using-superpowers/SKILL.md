---
name: using-superpowers
description: "Use when starting any conversation or task. 適用於開始任何對話或任務時。"
---

# 使用 Superpowers

<SUBAGENT-STOP>
如果你是被派發來執行特定任務的 subagent，忽略這個 skill。
</SUBAGENT-STOP>

<EXTREMELY-IMPORTANT>
只要你認為某個 skill 有哪怕 1% 的機會適用，你就**絕對必須（MUST）**呼叫它。

如果某個 skill 適用於你的任務，你沒有選擇權。你**必須（MUST）**使用它。

這不是可以協商的規則。你不能用任何理由說服自己跳過它。
</EXTREMELY-IMPORTANT>

## 核心規則

**在進行任何回覆或動作之前，先呼叫相關或使用者指定的 skill**——包括提出釐清問題、探索程式碼庫或檢查檔案。如果呼叫後發現不適合目前情境，可以不繼續使用。

**進入 plan mode 之前：**如果尚未完成 brainstorming，先呼叫 `brainstorming` skill。

接著，以目前對話使用的語言宣告「使用 [skill] 來 [目的]」（英文環境可使用 `Using [skill] to [purpose]`），並完整遵循該 skill。如果 skill 有 checklist，為每一個項目建立一個 task。

## Skill 優先順序

當多個 skill 同時適用時，先處理流程型 skill——它們決定做事的方法；接著才由實作型 skill（例如 `frontend-design`）執行工作。`brainstorming` 與 `systematic-debugging` 是 Superpowers 最常見的流程型 skill，但這項規則適用於所有 skill。

- 「我們來做 X」→ 先使用 `superpowers:brainstorming`，再使用實作型 skills。
- 「修好這個 bug」→ 先使用 `superpowers:systematic-debugging`，再使用領域型 skills。

## 警訊（Red Flags）

出現下列想法時，代表你應該停下來——你正在為跳過規則找合理化藉口：

| 想法 | 事實 |
|---|---|
| 「這只是一個簡單問題」 | 問題也是任務。先檢查 skills。 |
| 「我需要先取得更多背景」 | skill 檢查發生在釐清問題**之前**。 |
| 「讓我先探索程式碼庫」 | skills 會告訴你**如何**探索。先檢查。 |
| 「我可以快速看一下 git／檔案」 | 檔案沒有對話脈絡。先檢查 skills。 |
| 「讓我先收集資料」 | skills 會告訴你**如何**收集資料。 |
| 「這不需要正式的 skill」 | 只要有適用的 skill，就使用它。 |
| 「我記得這個 skill」 | skills 會持續演進。閱讀目前版本。 |
| 「這不算任務」 | 任何動作都是任務。先檢查。 |
| 「這個 skill 太重了」 | 簡單任務也可能變複雜。使用它。 |
| 「我先做這一小件事就好」 | 任何事情都要在開始前先檢查。 |
| 「這感覺很有效率」 | 沒有紀律的行動會浪費時間。skills 用來避免這件事。 |
| 「我知道那是什麼意思」 | 理解概念 ≠ 實際使用 skill。呼叫它。 |

## 平台適配

如果你目前使用的 harness 出現在下列清單，閱讀對應的 reference 以取得特殊指示：

- Codex：`references/codex-tools.md`
- Pi：`references/pi-tools.md`
- Antigravity：`references/antigravity-tools.md`

## 使用者指示

使用者指示（`CLAUDE.md`、`AGENTS.md`、`GEMINI.md` 等文件中的規則，以及使用者直接提出的要求）優先於 skills；skills 又優先於預設行為。只有在合作的人類使用者明確要求跳過時，才可以略過 skill 工作流程或其中的指示。
