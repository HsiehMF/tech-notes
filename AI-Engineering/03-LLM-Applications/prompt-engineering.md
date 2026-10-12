# Prompt Engineering

Prompt Engineering 是將任務、資料、限制與輸出要求整理成清楚指令，並透過測試改進 LLM 輸出的工程方法。

[回到本章](README.md) · [AI Engineering 索引](../README.md)

## Prompt 的四個基本要素

| 要素 | 要回答的問題 |
|---|---|
| Task | 要完成什麼任務？ |
| Context | 有哪些背景與可用資料？ |
| Constraints | 操作邊界與限制是什麼？ |
| Output | 回覆需要什麼格式與內容？ |

清楚、無矛盾且有足夠資料的指令，比單純增加篇幅更有用。任務要求和限制應相容，例如要求唯讀診斷時，不應同時要求直接修改系統。

## Zero-shot 與 Few-shot

| 方法 | 做法 | 適用考量 |
|---|---|---|
| Zero-shot | 直接說明任務，不提供示範答案 | 任務與要求可直接描述 |
| Few-shot | 提供少量正確的輸入與輸出範例 | 需要展示分類標準、格式或表達方式 |

Few-shot 的範例應符合實際任務且彼此一致。加入範例可能有幫助，但不保證比 Zero-shot 更準確，仍需測試。

## Task Decomposition 與 Prompt Chaining

Task Decomposition 是把複雜任務拆成較小步驟，可以在同一次呼叫中要求模型依序處理。

Prompt Chaining 則是在應用程式中串接多次模型呼叫，把前一步的輸出作為下一步的輸入。例如先抽取文件重點，再將重點交給第二次呼叫產生報告。

| 比較 | Task Decomposition | Prompt Chaining |
|---|---|---|
| 核心 | 任務拆解 | 多次呼叫的資料傳遞 |
| 呼叫次數 | 可以只有一次 | 多次 |
| 步驟控制 | 可透過指令安排 | 由應用程式安排呼叫與交接 |
| 中間檢查 | 取決於執行方式 | 可在呼叫之間驗證與分支 |

在 Prompt 裡列出「先做 A，再做 B」，不代表應用程式已實作 Chaining。

## Delimiters：區分指令與資料

Markdown 標題或 XML tags 可區隔任務指令、外部資料及輸出要求。

```text
任務：根據下方資料整理問題與證據。

<資料>
這裡放待分析的文件。
</資料>

輸出：問題、支持證據、未確認事項。
```

這種結構有助閱讀，但不能保證阻止 Prompt Injection。外部資料仍可能含有惡意指令；權限、工具操作限制及必要的人工核准應由應用程式管理。

## Output Specification

輸出要求應分清楚三件事：

- **Output Format**：Markdown、表格或其他格式。
- **Required Content**：必須包含的欄位及資訊。
- **Acceptance Criteria**：如何判斷輸出符合要求。

例如診斷報告可要求包含觀察、證據、推測、缺少的資料，以及「已確認／無法確認」的狀態。未實際執行驗證時，不能只因格式完整就宣稱驗證通過。

## Grounding 與不確定性

Grounding 要求結論建立在可用證據上，並能區分：

| 類型 | 表達方式 |
|---|---|
| 已確認事實 | 指出支持資料 |
| 推測或假設 | 標明推測與理由 |
| 未確認項目 | 說明缺少什麼資料或驗證 |

RAG 可以提供外部資料，是實現 Grounding 的方式之一。取得資料本身仍不足以保證結論正確，還需要確認來源與結論是否相符。

## Prompt Evaluation

Output Specification 定義期望的輸出；Prompt Evaluation 檢查實際輸出是否達到要求，兩者不能混為一談。

比較 Prompt V1 與 V2 時：

1. 定義評估面向，例如事實正確性、資訊完整性、可執行性與安全限制。
2. 準備多個有代表性的測試案例。
3. 固定模型、測試條件及評分規則。
4. 比較輸出和失敗案例；需要時重複測試。
5. 根據證據決定是否採用新版本。

不要只看到一次回答變好，就推論整體品質已提升。

## 通用範例：根據資料診斷應用程式問題

```text
Task
根據提供的 Logs 與 Events，整理應用程式啟動失敗的可能原因。

Context
可用資料是下方的 Logs、Events 與設定摘要。

Constraints
只分析已提供的資料。
區分已確認事實與假設。
缺少資料時，指出需要補充的內容。
不得聲稱已執行未實際執行的操作。

Output
以 Markdown 提供：
1. 觀察與支持證據。
2. 可能原因及其推測依據。
3. 尚未確認的事項。
4. 建議的下一個調查步驟。
```

Prompt 明確描述操作邊界，但系統是否能強制限制操作，仍取決於工具權限與執行環境。

## 相關文件

- [Tokenization & Context Window](../02-Large-Language-Models/tokenization-context.md)

