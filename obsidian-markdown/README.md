# Obsidian Markdown Skill

這是一個專為 Obsidian 使用者設計的 Markdown 語法技能包。它定義了如何使用 Obsidian 特色的擴展語法，確保 AI 助理在協作時能生成完全符合 Obsidian 規範的內容。

## 什麼是 Obsidian Markdown Skill？

這份技能包主要涵蓋了 **Obsidian Flavored Markdown (OFM)** 的核心語法。當你在處理 `.md` 檔案，或者在筆記中提到 Obsidian 特有功能（如雙向連結、提示框、屬性）時，AI 助理會自動調用此技能以提供精準的支援。

## 核心功能介紹

### 1. 雙向連結 (Internal Links / Wikilinks)
利用雙方括號將筆記串聯起來，這是建構知識圖譜的基礎。
- `[[筆記名稱]]`
- `[[筆記名稱#標題]]`
- `[[筆記名稱|顯示文字]]`

### 2. 繽紛提示框 (Callouts)
使用提示框來強調重點或整理資訊，支援多種預設樣式與折疊功能。
```markdown
> [!info] 標題
> 內容文字
```

### 3. 嵌入內容 (Embeds)
直接將其他檔案（筆記、圖片、PDF）的內容嵌入到當前頁面。
- `![[筆記名稱]]`
- `![[圖片.png|300]]`

### 4. 屬性管理 (Properties / Frontmatter)
在筆記開頭使用 YAML 格式定義標籤、日期、別名等中繼資料。
```yaml
---
tags: [project, finished]
date: 2024-01-17
---
```

### 5. 進階圖表與公式
- **Mermaid**: 直接用文字繪製流程圖、序列圖。
- **LaTeX Math**: 支援科學計算與數學公式顯示。

## 怎麼使用？

### 作為參考手冊
你可以隨時查閱同目錄下的 `SKILL.md` 檔案，裡面包含了所有詳細的語法範例與代碼區塊。

### 協作模式
當你與 AI 助理（如 Antigravity）對話時，你可以直接要求：
- 「幫我把這段文字整理成 Obsidian 的 Callout 格式。」
- 「幫這篇筆記加上 Properties 並設定 tags。」
- 「建立一個連結到『工作週報』的 Wikilink。」

助理會自動參考此技能規範，產出最完美的 Markdown 代碼。

---

*相關參考資料：[Obsidian 官方說明文件](https://help.obsidian.md/)*
