# 婦產科胚胎著床預測專案

> 北醫 醫療 AI 實戰力養成班 - 小組報告

## 專案目標

利用深度學習模型，根據胚胎顯微影像預測 IVF（試管嬰兒）胚胎是否能成功著床。

---

## 資料夾結構

```
小組報告：婦產科胚胎預測/
│
├── README.md                 # 專案說明（本文件）
│
├── data/                     # 資料管理（版本化）
│   ├── raw/                  # 原始資料（不可修改）
│   │   └── hvwc23/
│   │       ├── train/        # 原始訓練影像 (840張)
│   │       ├── test/         # 原始測試影像 (180張)
│   │       ├── train.csv
│   │       ├── test.csv
│   │       └── sample_submission.csv
│   │
│   ├── interim/              # 中間處理資料（暫存）
│   │
│   └── processed/            # 處理後資料（版本化保存）
│       ├── v1.0_baseline/
│       ├── v1.1_augmented/
│       └── ...
│
├── docs/                     # 文件記錄
│   ├── PROJECT_LOG.md        # 對話與進度記錄
│   ├── EXPERIMENT_LOG.md     # 實驗詳細記錄
│   └── DATA_VERSION_LOG.md   # 資料版本記錄 ⭐
│
├── notebooks/                # Jupyter Notebooks
│   ├── 01_EDA.ipynb          # 探索性資料分析
│   ├── 02_data_preprocessing_v1.0.ipynb
│   ├── 03_baseline.ipynb     # 基準模型
│   └── ...
│
├── src/                      # Python 程式碼
│   ├── data/                 # 資料處理模組
│   │   ├── dataset.py        # Dataset 類別
│   │   └── transforms.py     # 資料增強
│   ├── models/               # 模型定義
│   │   └── model.py
│   ├── train.py              # 訓練腳本
│   ├── evaluate.py           # 評估腳本
│   └── utils.py              # 工具函數
│
├── configs/                  # 配置檔案
│   └── config.yaml           # 超參數配置
│
├── models/                   # 儲存訓練好的模型
│   └── best_model.pth
│
└── outputs/                  # 輸出結果
    ├── figures/              # 圖表
    ├── logs/                 # 訓練日誌
    └── submissions/          # 提交檔案
```

---

## 資料集概述

| 項目 | 數值 |
|------|------|
| 訓練影像 | 840 張 |
| 測試影像 | 180 張 |
| 影像尺寸 | 256 × 256 |
| 類別 | 0 (未著床), 1 (著床成功) |
| 類別分佈 | 0: 85.2%, 1: 14.8% |

**胚胎發育階段**：
- D3：第 3 天胚胎（卵裂期）
- D5：第 5 天胚胎（囊胚期）

---

## 快速開始

### 1. 取得資料集

> **注意**：影像資料未包含在此 Git 倉庫中，需自行取得。

請將 hvwc23 資料集放置於 `data/raw/` 目錄下：

```
data/raw/
└── hvwc23/
    ├── train/           # 840 張訓練影像
    ├── test/            # 180 張測試影像
    ├── train.csv
    ├── test.csv
    └── sample_submission.csv
```

資料集來源：（請填入資料集來源連結或說明）

### 2. 環境設定

```bash
# 建立虛擬環境（建議）
python -m venv venv
source venv/bin/activate  # macOS/Linux

# 安裝套件
pip install -r requirements.txt
```

### 3. 執行訓練

```bash
python src/train.py --config configs/config.yaml
```

---

## 團隊成員

| 姓名 | 負責項目 |
|------|----------|
| | |
| | |
| | |

---

## 協作規範

### 文件更新
1. 每次與 AI 對話後，更新 `docs/PROJECT_LOG.md`
2. 每次實驗後，更新 `docs/EXPERIMENT_LOG.md`
3. 每次資料處理後，更新 `docs/DATA_VERSION_LOG.md`
4. 重要決策需記錄在文件中

### 資料版本管理
1. **絕對不要修改 `data/raw/` 中的原始資料**
2. 每次資料清洗建立新版本：`data/processed/vX.X_描述/`
3. 每個版本資料夾需包含 `metadata.json`
4. 實驗記錄中註明使用的資料版本

### 命名規範
- Notebook：`XX_描述.ipynb`（XX 為序號）
- 資料版本：`vX.X_描述`（如 v1.0_baseline）
- 模型檔案：`model_EXPXXX_日期.pth`
- 提交檔案：`submission_EXPXXX_日期.csv`

### 程式碼風格
- 使用有意義的變數名稱
- 加入必要的註解
- 函數需有 docstring

---

## 更新日誌

| 日期 | 更新內容 | 更新者 |
|------|----------|--------|
| 2025-01-25 | 專案初始化 | |
| | | |
