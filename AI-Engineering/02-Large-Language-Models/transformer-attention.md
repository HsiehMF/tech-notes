# Transformer & Attention

Transformer 是處理 Token 向量的神經網路架構。Attention 是其中整合跨Positional Information的重要機制；完整架構還包含Positional Information、Feed-Forward Network、Residual Connection 及 Normalization 等組件。

[回到本章](README.md) · [AI Engineering 索引](../README.md)

## Self-Attention

Self-Attention 在同一序列中，計算某個位置與可參考位置之間的關聯，再依權重整合資訊，形成帶有上下文的向量表示。

模型參考哪些位置，會受遮罩及架構限制。自回歸語言模型會遮住尚未出現的後續位置。

## Query、Key 與 Value

輸入向量經不同的投影，得到 Q、K、V。

| 向量 | 角色 |
|---|---|
| Query（Q） | 用來查詢相關資訊 |
| Key（K） | 與 Query 匹配並計算分數 |
| Value（V） | 提供被加權整合的資訊 |

Q 與 K 用於計算關聯分數，V 參與後續的資訊整合。**V 不等於 Attention Output**；Output 是多個 Value 依權重加總後的結果。

## Scaled Dot-Product Attention

計算流程：

1. 計算 Q 與 K 的點積，取得匹配分數。
2. 依 Key 維度的平方根縮放分數。
3. 在需要時套用遮罩，排除不可參考的位置。
4. 經 Softmax 將分數轉成權重。
5. 將權重乘上 V，得到加權整合的結果。

不含遮罩的標準表示：

```text
Attention(Q, K, V) = softmax(QKᵀ / √d_k)V
```

其中 d_k 是 Key 的維度。

## Multi-Head Attention

Multi-Head Attention 使用多組不同的 Q、K、V 投影，在不同表示空間計算 Attention，再將結果串接與投影。

不同 Head 提供更彈性的表示方式。Head 是模型內部的運算組件，不是獨立 LLM，也不是 Subagent。不能直接假定某個 Head 固定負責某種語意。

## Causal Mask

Causal Mask 遮住後續位置，使自回歸模型的每個位置只能利用當前與之前的 Token 來預測下一個 Token。

即使訓練資料包含完整句子，也不能讓某個位置直接參考未來 Token，否則會破壞下一 Token 預測的訓練目標。

## Positional Information

Attention 需要Positional Information來利用順序差異。例如「我喜歡你」與「你喜歡我」使用相同的字，排列不同，意思也不同。

不同 Transformer 架構提供Positional Information的方法可能不同；關鍵是讓運算能反映 Token 的位置或相對順序。

## FFN、Residual 與 Normalization

| 組件 | 作用 |
|---|---|
| Attention | 跨位置整合資訊 |
| Feed-Forward Network（FFN） | 對每個位置的向量進行轉換 |
| Residual Connection | 保留輸入的直接路徑，將輸入與轉換結果相加 |
| Layer Normalization | 對表示進行正規化，支持穩定的深層網路運算與訓練 |

Residual Connection 可簡化表示為：

```text
Output = X + F(X)
```

X 是原始輸入，F(X) 是某個子層的轉換結果。相加後的表示由兩條路徑共同貢獻；不是只保留其中一條。具體 Normalization 的位置取決於模型架構。

## 常見混淆

- Transformer 是完整架構，Attention 是其中的機制。
- Value 是供整合的資訊向量，Attention Output 是加權整合的結果。
- Causal Mask 限制可參考的位置，Positional Information提供排列及順序訊息，兩者作用不同。
- 教學中假設的 Attention 權重不能當成真實模型的解讀證據。

## 相關文件

- [Tokenization & Context Window](tokenization-context.md)

