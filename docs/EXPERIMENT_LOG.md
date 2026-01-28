# 實驗記錄表

> 詳細記錄每次模型訓練的超參數、結果與分析

---

## 實驗總覽表

| 實驗編號 | 日期 | 資料版本 | 模型 | 主要改動 | Val Acc | Val AUC | 備註 |
|----------|------|----------|------|----------|---------|---------|------|
| EXP-001_example | 2025-01-25 | v1.0_baseline_example | ResNet18 | Baseline | 85.7% | 0.72 | ⚠️ 範例 |
| EXP-001 | 2025-01-26 | raw/hvwc23 | ResNet18 | Baseline + 類別加權 | 85.12% | 0.7986 | ✅ 基準模型 |
| EXP-002 | | | | | | | （待執行）|

---

## 詳細實驗記錄

---

### EXP-001_example：Baseline 基準模型 ⚠️ 範例

> **注意**：這是範例記錄，展示應如何填寫。實際實驗請複製此格式並修改內容。

**日期**：2025-01-25
**執行者**：（範例）王小明
**目標**：建立基準效能，作為後續改進的比較基準

#### 使用資料版本

| 項目 | 值 |
|------|-----|
| **資料版本** | `v1.0_baseline_example` |
| **資料路徑** | `data/processed/v1.0_baseline_example/` |
| **訓練樣本** | 672 張 (Class 0: 573, Class 1: 99) |
| **驗證樣本** | 168 張 (Class 0: 143, Class 1: 25) |
| **處理記錄** | 見 [DATA_VERSION_LOG.md](DATA_VERSION_LOG.md#v10_baseline_example) |

#### 實驗配置

```yaml
# 模型架構
model:
  name: ResNet18
  pretrained: true  # ImageNet 預訓練權重
  num_classes: 2

# 資料來源（重要！）
data:
  version: v1.0_baseline_example  # 範例
  path: data/processed/v1.0_baseline_example/
  train_samples: 672
  val_samples: 168
  image_size: 256
  augmentation:
    - RandomHorizontalFlip(p=0.5)
    - RandomRotation(degrees=15)
    - Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225])

# 訓練參數
training:
  batch_size: 32
  epochs: 50
  optimizer: Adam
  learning_rate: 0.001
  weight_decay: 1e-4
  scheduler: StepLR(step_size=10, gamma=0.1)
  criterion: CrossEntropyLoss

# 處理類別不平衡
imbalance_strategy: none  # 基準模型不做處理
```

#### 實驗結果

| 指標 | 訓練集 | 驗證集 |
|------|--------|--------|
| Accuracy | 92.3% | 85.7% |
| AUC-ROC | 0.89 | 0.72 |
| Precision (class 1) | 0.85 | 0.45 |
| Recall (class 1) | 0.78 | 0.36 |
| F1-Score (class 1) | 0.81 | 0.40 |

#### 混淆矩陣

```
驗證集 (168 張)：
              Predicted
              0      1
Actual  0   135     8
        1    16     9

TN=135, FP=8, FN=16, TP=9
```

#### 訓練曲線

- Loss 曲線：訓練 loss 持續下降，驗證 loss 在 epoch 20 後趨於平穩
- Accuracy 曲線：驗證 accuracy 在 epoch 15 達到最高，之後略有震盪
- 圖表位置：`outputs/figures/EXP-001_training_curves.png`

#### 分析與觀察

1. **類別不平衡問題嚴重**：模型傾向預測多數類 (Class 0)，導致 Class 1 的 Recall 只有 36%
2. **過擬合跡象**：訓練集 AUC (0.89) 遠高於驗證集 (0.72)
3. **Precision-Recall 權衡**：Class 1 的 Precision 和 Recall 都偏低，需要改進

#### 下一步改進方向

- [ ] 嘗試 Weighted CrossEntropyLoss 處理類別不平衡
- [ ] 嘗試資料增強（更強的增強策略）
- [ ] 嘗試 Oversampling 少數類
- [ ] 調整決策閾值（threshold tuning）

---

### EXP-001：Baseline 基準模型（實際執行）

**日期**：2025-01-26
**執行者**：（請填寫）
**目標**：建立基準效能，了解資料特性
**執行環境**：Google Colab (T4 GPU)

#### 使用資料版本

| 項目 | 值 |
|------|-----|
| **資料版本** | 原始資料 (raw/hvwc23) |
| **資料路徑** | Google Drive: /MyDrive/hvwc23/ |
| **訓練樣本** | 672 張 (80%) |
| **驗證樣本** | 168 張 (20%) |
| **分割方式** | Stratified split (類別比例一致) |

#### 實驗配置

```yaml
# 模型架構
model:
  name: ResNet18
  pretrained: true  # ImageNet 預訓練權重
  num_classes: 2

# 資料設定
data:
  image_size: 224
  train_samples: 672
  val_samples: 168
  augmentation:
    - RandomHorizontalFlip(p=0.5)
    - RandomRotation(degrees=15)
    - ColorJitter(brightness=0.1, contrast=0.1)
    - Normalize(mean=[0.485, 0.456, 0.406], std=[0.229, 0.224, 0.225])

# 訓練參數
training:
  batch_size: 32
  epochs: 20
  optimizer: Adam
  learning_rate: 0.001
  weight_decay: 1e-4
  scheduler: StepLR(step_size=10, gamma=0.1)
  criterion: CrossEntropyLoss(weight=class_weights)  # 類別加權

# 處理類別不平衡
imbalance_strategy: weighted_loss
```

#### 實驗結果

| 指標 | 驗證集 |
|------|--------|
| **Accuracy** | 85.12% |
| **AUC-ROC** | **0.7986** |
| **Precision (class 1)** | 50.00% |
| **Recall (class 1)** | **76.00%** |
| **F1-Score (class 1)** | 60.32% |

#### 結果分析

**✅ 優點：**
1. **Recall 達到 76%**：會著床的胚胎，模型能猜對大部分
2. **AUC 接近 0.8**：模型有一定的分辨能力
3. **類別加權有效**：比不加權時 Recall 更高

**⚠️ 待改進：**
1. **Precision 只有 50%**：預測「會著床」的胚胎中，一半是誤報
2. **可能有改進空間**：AUC 還未達到 0.85+

#### 下一步改進方向

- [ ] 調整判斷閾值（降低閾值可能提高 Recall，但會降低 Precision）
- [ ] 嘗試更強的資料增強
- [ ] 嘗試其他模型架構（EfficientNet, ConvNeXt）
- [ ] 嘗試 Focal Loss 處理類別不平衡

---

### EXP-002：（待執行）

**日期**：
**執行者**：
**目標**：

#### 相較於 EXP-001 的改動

-

#### 實驗配置

```yaml
# (填寫與前次不同的配置)
```

#### 實驗結果

| 指標 | 驗證集 | vs EXP-001 |
|------|--------|------------|
| Accuracy | | |
| AUC-ROC | | |
| Recall (class 1) | | |

#### 分析與觀察

1.
2.

---

## 最佳模型記錄

| 排名 | 實驗編號 | Val AUC | Val Recall | 模型檔案 | 備註 |
|------|----------|---------|------------|----------|------|
| 1 | EXP-001 | 0.7986 | 76.00% | best_model.pth (Colab) | 目前最佳 |
| 2 | | | | | |
| 3 | | | | | |

---

## 實驗心得與教訓

### 有效的策略
- ✅ 類別加權 (Weighted CrossEntropyLoss)：有效提高少數類的 Recall

### 無效的策略
- （待補充）

### 注意事項
- 資料嚴重不平衡（85% vs 15%），單看 Accuracy 會被誤導
- 醫療情境下 **Recall 比 Precision 重要**（漏掉好胚胎比誤報更嚴重）
