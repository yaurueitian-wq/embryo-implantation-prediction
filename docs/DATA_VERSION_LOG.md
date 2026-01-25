# 資料版本記錄

> 追蹤每次資料清洗與處理的版本，確保資料可溯源

---

## 資料夾結構說明

```
data/
├── raw/                      # 原始資料（絕對不可修改）
│   └── hvwc23/               # 原始競賽資料集
│       ├── train/            # 原始訓練影像
│       ├── test/             # 原始測試影像
│       ├── train.csv
│       ├── test.csv
│       └── sample_submission.csv
│
├── interim/                  # 中間處理資料（可選）
│   └── v1.0_去除模糊/        # 處理過程中的暫存
│
└── processed/                # 最終處理後的資料版本
    ├── v1.0_baseline/        # 版本 1.0
    ├── v1.1_augmented/       # 版本 1.1
    └── v2.0_cleaned/         # 版本 2.0
```

---

## 版本命名規則

```
v{主版本}.{次版本}_{簡短描述}

範例：
- v1.0_baseline        → 基礎處理（resize、normalize）
- v1.1_augmented       → 加入資料增強
- v2.0_cleaned         → 移除低品質影像（重大變更）
- v2.1_balanced        → 處理類別不平衡
```

**主版本**：資料有重大變更（如移除/新增樣本）
**次版本**：處理方式微調（如增強策略調整）

---

## 資料版本總覽

| 版本 | 日期 | 處理內容 | 樣本數 | 使用於實驗 | 備註 |
|------|------|----------|--------|------------|------|
| raw | - | 原始資料 | Train: 840, Test: 180 | - | 不可修改 |
| v1.0_baseline_example | 2025-01-25 | 基礎分割 (80/20) | Train: 672, Val: 168, Test: 180 | EXP-001_example | ⚠️ 範例 |
| v1.0_baseline | | | | | （實際版本待建立）|

---

## 詳細版本記錄

---

### v1.0_baseline_example ⚠️ 範例

> **注意**：這是範例記錄，展示應如何填寫。實際版本請複製此格式並修改內容。

**建立日期**：2025-01-25
**建立者**：（範例）王小明
**資料夾路徑**：`data/processed/v1.0_baseline_example/`

#### 處理步驟

1. **影像處理**
   - [x] Resize: 維持原始 256×256（不調整）
   - [x] Normalize: 無（訓練時處理）
   - [x] Color space: RGB

2. **資料分割**
   - 訓練集：672 張（80%）
   - 驗證集：168 張（20%）
   - 測試集：180 張（不變）
   - 分割方式：分層抽樣 (stratified)，確保類別比例一致
   - Random seed: 42

3. **類別分佈**
   | 資料集 | Class 0 | Class 1 | Class 1 比例 |
   |--------|---------|---------|--------------|
   | 訓練集 | 573 | 99 | 14.7% |
   | 驗證集 | 143 | 25 | 14.9% |

#### 處理程式碼

```python
# Notebook: notebooks/02_data_preprocessing_v1.0.ipynb

from sklearn.model_selection import train_test_split

# 分層抽樣分割
train_df, val_df = train_test_split(
    raw_df,
    test_size=0.2,
    random_state=42,
    stratify=raw_df['Class']
)
```

#### 輸出檔案

```
data/processed/v1.0_baseline/
├── train/              # 672 張影像
├── val/                # 168 張影像
├── test/               # 180 張影像
├── train.csv           # 訓練集標籤
├── val.csv             # 驗證集標籤
├── test.csv            # 測試集列表
└── metadata.json       # 完整處理參數記錄
```

#### 備註

- ⚠️ 這是範例記錄，展示應如何填寫
- 實際版本僅做資料分割，不做額外處理
- 後續版本會基於此進行改進（如資料增強、處理類別不平衡）
- **使用於實驗**：EXP-001_example（範例）

---

### v1.1_augmented

**建立日期**：YYYY-MM-DD
**建立者**：
**基於版本**：v1.0_baseline

#### 相較於上一版本的改動

-

#### 資料增強策略

```python
transforms = [
    # 列出使用的增強方法
]
```

---

## 資料品質檢查清單

每個新版本建立前，請確認：

- [ ] 原始資料 (raw) 未被修改
- [ ] 處理步驟已記錄在此文件
- [ ] 處理程式碼已保存（notebook 或 script）
- [ ] metadata.json 已建立
- [ ] 樣本數量正確
- [ ] 影像可正常讀取

---

## metadata.json 範本

每個處理後的資料夾應包含 `metadata.json`：

```json
{
  "version": "v1.0_baseline",
  "created_date": "2025-01-25",
  "created_by": "姓名",
  "base_version": "raw",
  "description": "基礎資料處理",
  "processing_steps": [
    {
      "step": 1,
      "action": "resize",
      "params": {"size": [224, 224]}
    },
    {
      "step": 2,
      "action": "normalize",
      "params": {"mean": [0.485, 0.456, 0.406], "std": [0.229, 0.224, 0.225]}
    }
  ],
  "statistics": {
    "train_samples": 672,
    "val_samples": 168,
    "test_samples": 180,
    "class_distribution": {
      "train": {"0": 573, "1": 99},
      "val": {"0": 143, "1": 25}
    }
  },
  "source_notebook": "notebooks/01_data_preprocessing_v1.0.ipynb"
}
```

---

## 注意事項

1. **永遠不要修改 `data/raw/` 中的資料**
2. 每次處理都建立新版本資料夾
3. 實驗記錄中要註明使用的資料版本
4. 定期清理不再使用的 interim 資料（但保留 processed）
