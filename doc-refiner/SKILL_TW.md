---
name: doc-refiner
description: 自動化將 Obsidian Markdown 筆記精煉並組織成標準化格式的流程。
---

# 文件精煉器

## 描述
此技能自動化清理和組織原始 Markdown 筆記（通常來自收件匣），轉換為適合 Obsidian 儲存庫中 `20_Technical` 或其他永久資料夾的標準化格式。

## 使用方式
當使用者要求「組織」、「精煉」或「修正」筆記，或使用指令 `/refine` 時使用此技能。

## 工作流程
1.  **分析內容**：讀取目標檔案。
2.  **生成 YAML**：建立或更新 YAML 前置元數據。
    -   `tags`：根據內容新增相關標籤（例如：`#technical`、`#grafana`、`#git`）。
    -   `status`：設定為 `done` 或 `processing`。
    -   `date`：`YYYY-MM-DD` 格式的目前日期。
3.  **格式化內容**：
    -   將 "Note:"、"Warning:"、"Tip:" 文字區塊轉換為 Obsidian Callouts（`> [!NOTE]`、`> [!WARNING]`）。
    -   確保標題結構正確（H1 用於標題，H2+ 用於章節）。
    -   修復損壞或非標準的格式。
4.  **重新命名檔案**（可選）：如果目前檔案名稱混亂或為中文，建議使用英文 `snake_case` 或 `PascalCase` 的新檔案名稱，如果存在 `GEMINI.md` 慣例則匹配之。
5.  **輸出**：使用多重取代或覆寫檔案的方式輸出精煉後的內容。

## 指令
### `/refine [file_path]`
精煉指定的檔案。

## 報告
在解釋採取的行動或向使用者提供回饋時，您**必須**使用**繁體中文**。
