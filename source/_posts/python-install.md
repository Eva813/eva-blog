---
title: Python 安裝與環境
date: 2024-11-15 07:29:29
tags: ["Python"]
categories: Python
---

## Python 的安裝步驟

### 為什麼要學習 Python？
最近發現 GitHub 上許多新專案都適用 Python 或是要使用的 AI 工具也會是使用 Python 開發，於是決定也來學學 Python！

### 如何安裝 Python？
- 前往 Python 的官方網站 下載安裝程式。
- 按照安裝程式的指示完成安裝。
- 安裝完成後，打開終端機（或命令提示字元），輸入以下指令確認安裝是否成功：
  ```bash
  python --version
  ```
- 若您有多個專案需使用不同的 Python 版本，建議使用版本管理工具，例如 pyenv。

<!-- more -->

## pyenv 入門：環境與基本安裝
在開發過程中，不同專案可能需要不同版本的 Python，或是需要測試程式碼在不同版本的相容性。pyenv 能有效隔離環境，避免版本衝突，讓管理變得更加靈活。
### 安裝 pyenv
至 [pyenv GitHub](https://github.com/pyenv/pyenv?tab=readme-ov-file#homebrew-in-macos) 頁面，根據你的作業系統進行安裝。
安裝完成後，重啟終端機，並輸入以下指令確認安裝：
```bash
pyenv -v
```

## pyenv 的常見指令

### 列出已安裝的 Python 版本
```bash
pyenv versions
```

### 查看可安裝的 Python 版本
```bash
pyenv install --list
```

### 安裝特定版本的 Python

```bash
pyenv install 3.12.7
```

### 切換 Python 版本
- 臨時切換：
```bash
pyenv shell 3.12.7
```
- 永久切換（全域預設版本）：
```bash
pyenv global 3.12.7
```

### 移除已安裝的 Python 版本
```bash
pyenv uninstall 3.12.7
```

## 虛擬環境
虛擬環境是 Python 開發中用來隔離專案依賴的工具，雖然它對於剛開始學習 Python 的新手不是必要的，但當專案規模逐漸擴大、需要處理多個專案或套件版本時，它的重要性會逐漸浮現。
### 什麼是虛擬環境？
虛擬環境是 Python 提供的一種工具，讓你可以在同一台電腦上建立多個彼此獨立的 Python 環境，每個環境可以擁有不同的：
- Python 版本
- 已安裝的套件及其版本

虛擬環境可以避免多個專案之間因套件衝突而導致問題。

以下幾種情境下特別建議使用：
1.需要管理不同的專案依賴：
假設專案 A 使用 Django 4.0，而專案 B 使用 Django 5.0，虛擬環境可以讓兩者共存，互不干擾。
2.避免污染全域環境：
在系統的 Python 安裝目錄安裝套件可能會對其他專案造成影響，虛擬環境能避免這種問題。

常見的虛擬環境工具
1. 內建工具 venv
Python 3.3 之後內建的工具，無需額外安裝。
適合大多數基本需求。
2. 第三方工具 virtualenv
功能更完整，支援更多選項。
比 venv 更適合舊版 Python（如 Python 2.x）。
3. 進階工具 Poetry
不僅管理虛擬環境，還能管理依賴套件和版本。
提供「一站式」解決方案，是目前非常流行的工具。

以下我將以 `venv`, `Poetry` 介紹

## 使用內建的 venv 工具

1. 建立虛擬環境：
- 注意：要在專案目錄下執行以下命令
- myenv 是虛擬環境的名稱，可以替換成其他名字。

```Python
 python -m venv myenv
```

2. 啟動虛擬環境 (macOS)

```Python
source myenv/bin/activate
```

- 啟動後，命令列會顯示 (myenv)，代表已進入虛擬環境。

接續在虛擬環境，可以執行專案內需要的套件安裝

```Python
pip install requests beautifulsoup4
```

3. 退出虛擬環境

```
deactivate
```

## 使用 Poetry 

1. 安裝 Poetry（建議用 pipx 安裝）：

```bash
pipx install poetry

```

2. 建立新專案：

```bash
poetry new myproject
cd myproject
```
- 預設會生成 pyproject.toml 作為專案配置檔案。

3. 啟動虛擬環境：

```bash
poetry shell
```

或直接在虛擬環境中運行程式：

```bash
poetry run python my_script.py
```
4. 添加 dependency：

```bash
poetry add django
```
- poetry.lock 和 pyproject.toml 會自動更新，鎖定依賴版本。

5. 群組化依賴（開發用套件）：

```bash
poetry add --group dev pytest
```

6. 安裝全部依賴： 當其他人下載專案時，只需執行：

```bash
poetry install
```