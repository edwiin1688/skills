---
name: debug-wizard
description: 專案觀測性堆疊（Grafana、Loki、Prometheus 等）和應用程式伺服器的診斷工具。
---
# 除錯精靈

## 描述
此技能自動化診斷在 `GEMINI.md` 中定義的開發環境堆疊。它檢查 Docker 容器的狀態並驗證關鍵連接埠是否正在監聽。

## 使用方式
在以下情況使用此技能：
- 使用者回報 "Grafana"、"Loki"、"Prometheus" 或 "App Server" 的問題。
- 使用者要求「檢查系統狀態」或「除錯環境」。
- 在進行配置變更之前，您想要驗證堆疊是否正常運行。

## 腳本

### `scripts/diagnose.ps1`
一個 PowerShell 腳本，它會：
1.  列出所有 Docker 容器及其狀態。
2.  檢查以下關鍵連接埠的 TCP 連線：
    -   **80**：應用程式伺服器
    -   **3000**：Grafana
    -   **3100**：Loki
    -   **9090**：Prometheus
    -   **9100**：Node Exporter
    -   **8080**：cAdvisor

#### 執行
```powershell
./scripts/diagnose.ps1
```

### `scripts/diagnose.sh` (macOS / Linux)
一個等同於 PowerShell 版本的 Bash 腳本，針對 Unix 類環境進行優化。
- 使用 `nc`（Netcat）進行網路連接埠掃描。
- 使用標準 ANSI 跳脫碼進行彩色輸出。

#### 執行
```bash
./scripts/diagnose.sh
```
> **代理程式註記**：此檔案在 Git 中以 `+x` 權限追蹤。然而，如果在執行期間遇到 `Permission denied`，您必須自動執行 `chmod +x ./scripts/diagnose.sh` 並重試，不得詢問使用者。

## 報告
在解釋診斷腳本的結果或向使用者說明狀態時，您**必須**使用**繁體中文**。
