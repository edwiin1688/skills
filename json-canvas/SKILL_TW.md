---
name: json-canvas
description: 建立和編輯包含節點、邊緣、群組和連線的 JSON Canvas 檔案（.canvas）。在處理 .canvas 檔案、建立視覺畫布、心智圖、流程圖或當使用者提及 Obsidian 中的 Canvas 檔案時使用。
---

# JSON Canvas 技能

此技能讓相容的代理程式能夠建立和編輯在 Obsidian 和其他應用程式中使用的有效 JSON Canvas 檔案（`.canvas`）。

## 概述

JSON Canvas 是一個用於無限畫布資料的開放檔案格式。Canvas 檔案使用 `.canvas` 副檔名，並包含遵循 [JSON Canvas Spec 1.0](https://jsoncanvas.org/spec/1.0/) 的有效 JSON。

## 檔案結構

Canvas 檔案包含兩個頂層陣列：

```json
{
  "nodes": [],
  "edges": []
}
```

- `nodes`（可選）：節點物件的陣列
- `edges`（可選）：連接節點的邊緣物件陣列

## 節點

節點是放置在畫布上的物件。有四種節點類型：
- `text` - 包含 Markdown 的文字內容
- `file` - 檔案/附件的參照
- `link` - 外部 URL
- `group` - 其他節點的視覺容器

### Z 軸順序

節點在陣列中按 z 軸順序排列：
- 第一個節點 = 底層（顯示在其他節點下方）
- 最後一個節點 = 頂層（顯示在其他節點上方）

### 通用節點屬性

所有節點都共享這些屬性：

| 屬性 | 必需 | 類型 | 描述 |
|-----------|----------|------|-------------|
| `id` | 是 | 字串 | 節點的唯一識別符 |
| `type` | 是 | 字串 | 節點類型：`text`、`file`、`link` 或 `group` |
| `x` | 是 | 整數 | X 座標（像素） |
| `y` | 是 | 整數 | Y 座標（像素） |
| `width` | 是 | 整數 | 寬度（像素） |
| `height` | 是 | 整數 | 高度（像素） |
| `color` | 否 | canvasColor | 節點顏色（參見顏色部分） |

### 文字節點

文字節點包含 Markdown 內容。

```json
{
  "id": "6f0ad84f44ce9c17",
  "type": "text",
  "x": 0,
  "y": 0,
  "width": 400,
  "height": 200,
  "text": "# Hello World\n\nThis is **Markdown** content."
}
```

| 屬性 | 必需 | 類型 | 描述 |
|-----------|----------|------|-------------|
| `text` | 是 | 字串 | 包含 Markdown 語法的純文字 |

### 檔案節點

檔案節點參照檔案或附件（圖片、影片、PDF、筆記等）。

```json
{
  "id": "a1b2c3d4e5f67890",
  "type": "file",
  "x": 500,
  "y": 0,
  "width": 400,
  "height": 300,
  "file": "Attachments/diagram.png"
}
```

```json
{
  "id": "b2c3d4e5f6789012",
  "type": "file",
  "x": 500,
  "y": 400,
  "width": 400,
  "height": 300,
  "file": "Notes/Project Overview.md",
  "subpath": "#Implementation"
}
```

| 屬性 | 必需 | 類型 | 描述 |
|-----------|----------|------|-------------|
| `file` | 是 | 字串 | 系統內檔案的路徑 |
| `subpath` | 否 | 字串 | 連結到標題或區塊（以 `#` 開頭） |

### 連結節點

連結節點顯示外部 URL。

```json
{
  "id": "c3d4e5f678901234",
  "type": "link",
  "x": 1000,
  "y": 0,
  "width": 400,
  "height": 200,
  "url": "https://obsidian.md"
}
```

| 屬性 | 必需 | 類型 | 描述 |
|-----------|----------|------|-------------|
| `url` | 是 | 字串 | 外部 URL |

### 群組節點

群組節點是用於組織其他節點的視覺容器。

```json
{
  "id": "d4e5f6789012345a",
  "type": "group",
  "x": -50,
  "y": -50,
  "width": 1000,
  "height": 600,
  "label": "Project Overview",
  "color": "4"
}
```

```json
{
  "id": "e5f67890123456ab",
  "type": "group",
  "x": 0,
  "y": 700,
  "width": 800,
  "height": 500,
  "label": "Resources",
  "background": "Attachments/background.png",
  "backgroundStyle": "cover"
}
```

| 屬性 | 必需 | 類型 | 描述 |
|-----------|----------|------|-------------|
| `label` | 否 | 字串 | 群組的文字標籤 |
| `background` | 否 | 字串 | 背景圖片的路徑 |
| `backgroundStyle` | 否 | 字串 | 背景渲染樣式 |

#### 背景樣式

| 值 | 描述 |
|-------|-------------|
| `cover` | 填滿節點的整個寬度和高度 |
| `ratio` | 保持背景圖片的寬高比 |
| `repeat` | 在兩個方向重複圖案式重複圖片 |

## 邊緣

邊緣是連接節點的線條。

```json
{
  "id": "f67890123456789a",
  "fromNode": "6f0ad84f44ce9c17",
  "toNode": "a1b2c3d4e5f67890"
}
```

```json
{
  "id": "0123456789abcdef",
  "fromNode": "6f0ad84f44ce9c17",
  "fromSide": "right",
  "fromEnd": "none",
  "toNode": "b2c3d4e5f6789012",
  "toSide": "left",
  "toEnd": "arrow",
  "color": "1",
  "label": "leads to"
}
```

| 屬性 | 必需 | 類型 | 預設值 | 描述 |
|-----------|----------|------|---------|-------------|
| `id` | 是 | 字串 | - | 邊緣的唯一識別符 |
| `fromNode` | 是 | 字串 | - | 連線起始的節點 ID |
| `fromSide` | 否 | 字串 | - | 邊緣起始的側邊 |
| `fromEnd` | 否 | 字串 | `none` | 邊緣起始的形狀 |
| `toNode` | 是 | 字串 | - | 連線結束的節點 ID |
| `toSide` | 否 | 字串 | - | 邊緣結束的側邊 |
| `toEnd` | 否 | 字串 | `arrow` | 邊緣結束的形狀 |
| `color` | 否 | canvasColor | - | 線條顏色 |
| `label` | 否 | 字串 | - | 邊緣的文字標籤 |

### 側邊值

| 值 | 描述 |
|-------|-------------|
| `top` | 節點的上邊緣 |
| `right` | 節點的右邊緣 |
| `bottom` | 節點的下邊緣 |
| `left` | 節點的左邊緣 |

### 端點形狀

| 值 | 描述 |
|-------|-------------|
| `none` | 無端點形狀 |
| `arrow` | 箭頭端點 |

## 顏色

`canvasColor` 類型可以用兩種方式指定：

### 十六進位顏色

```json
{
  "color": "#FF0000"
}
```

### 預設顏色

```json
{
  "color": "1"
}
```

| 預設 | 顏色 |
|--------|-------|
| `"1"` | 紅色 |
| `"2"` | 橙色 |
| `"3"` | 黃色 |
| `"4"` | 綠色 |
| `"5"` | 青色 |
| `"6"` | 紫色 |

注意：預設顏色的具體顏色值是故意未定義的，允許應用程式使用自己的品牌顏色。

## 完整範例

### 包含文字和連線的簡單畫布

```json
{
  "nodes": [
    {
      "id": "8a9b0c1d2e3f4a5b",
      "type": "text",
      "x": 0,
      "y": 0,
      "width": 300,
      "height": 150,
      "text": "# Main Idea\n\nThis is the central concept."
    },
    {
      "id": "1a2b3c4d5e6f7a8b",
      "type": "text",
      "x": 400,
      "y": -100,
      "width": 250,
      "height": 100,
      "text": "## Supporting Point A\n\nDetails here."
    },
    {
      "id": "2b3c4d5e6f7a8b9c",
      "type": "text",
      "x": 400,
      "y": 100,
      "width": 250,
      "height": 100,
      "text": "## Supporting Point B\n\nMore details."
    }
  ],
  "edges": [
    {
      "id": "3c4d5e6f7a8b9c0d",
      "fromNode": "8a9b0c1d2e3f4a5b",
      "fromSide": "right",
      "toNode": "1a2b3c4d5e6f7a8b",
      "toSide": "left"
    },
    {
      "id": "4d5e6f7a8b9c0d1e",
      "fromNode": "8a9b0c1d2e3f4a5b",
      "fromSide": "right",
      "toNode": "2b3c4d5e6f7a8b9c",
      "toSide": "left"
    }
  ]
}
```

### 包含群組的專案看板

```json
{
  "nodes": [
    {
      "id": "5e6f7a8b9c0d1e2f",
      "type": "group",
      "x": 0,
      "y": 0,
      "width": 300,
      "height": 500,
      "label": "To Do",
      "color": "1"
    },
    {
      "id": "6f7a8b9c0d1e2f3a",
      "type": "group",
      "x": 350,
      "y": 0,
      "width": 300,
      "height": 500,
      "label": "In Progress",
      "color": "3"
    },
    {
      "id": "7a8b9c0d1e2f3a4b",
      "type": "group",
      "x": 700,
      "y": 0,
      "width": 300,
      "height": 500,
      "label": "Done",
      "color": "4"
    },
    {
      "id": "8b9c0d1e2f3a4b5c",
      "type": "text",
      "x": 20,
      "y": 50,
      "width": 260,
      "height": 80,
      "text": "## Task 1\n\nImplement feature X"
    },
    {
      "id": "9c0d1e2f3a4b5c6d",
      "type": "text",
      "x": 370,
      "y": 50,
      "width": 260,
      "height": 80,
      "text": "## Task 2\n\nReview PR #123",
      "color": "2"
    },
    {
      "id": "0d1e2f3a4b5c6d7e",
      "type": "text",
      "x": 720,
      "y": 50,
      "width": 260,
      "height": 80,
      "text": "## Task 3\n\n~~Setup CI/CD~~"
    }
  ],
  "edges": []
}
```

### 包含檔案和連結的研究畫布

```json
{
  "nodes": [
    {
      "id": "1e2f3a4b5c6d7e8f",
      "type": "text",
      "x": 300,
      "y": 200,
      "width": 400,
      "height": 200,
      "text": "# Research Topic\n\n## Key Questions\n\n- How does X affect Y?\n- What are the implications?",
      "color": "5"
    },
    {
      "id": "2f3a4b5c6d7e8f9a",
      "type": "file",
      "x": 0,
      "y": 0,
      "width": 250,
      "height": 150,
      "file": "Literature/Paper A.pdf"
    },
    {
      "id": "3a4b5c6d7e8f9a0b",
      "type": "file",
      "x": 0,
      "y": 200,
      "width": 250,
      "height": 150,
      "file": "Notes/Meeting Notes.md",
      "subpath": "#Key Insights"
    },
    {
      "id": "4b5c6d7e8f9a0b1c",
      "type": "link",
      "x": 0,
      "y": 400,
      "width": 250,
      "height": 100,
      "url": "https://example.com/research"
    },
    {
      "id": "5c6d7e8f9a0b1c2d",
      "type": "file",
      "x": 750,
      "y": 150,
      "width": 300,
      "height": 250,
      "file": "Attachments/diagram.png"
    }
  ],
  "edges": [
    {
      "id": "6d7e8f9a0b1c2d3e",
      "fromNode": "2f3a4b5c6d7e8f9a",
      "fromSide": "right",
      "toNode": "1e2f3a4b5c6d7e8f",
      "toSide": "left",
      "label": "supports"
    },
    {
      "id": "7e8f9a0b1c2d3e4f",
      "fromNode": "3a4b5c6d7e8f9a0b",
      "fromSide": "right",
      "toNode": "1e2f3a4b5c6d7e8f",
      "toSide": "left",
      "label": "informs"
    },
    {
      "id": "8f9a0b1c2d3e4f5a",
      "fromNode": "4b5c6d7e8f9a0b1c",
      "fromSide": "right",
      "toNode": "1e2f3a4b5c6d7e8f",
      "toSide": "left",
      "toEnd": "arrow",
      "color": "6"
    },
    {
      "id": "9a0b1c2d3e4f5a6b",
      "fromNode": "1e2f3a4b5c6d7e8f",
      "fromSide": "right",
      "toNode": "5c6d7e8f9a0b1c2d",
      "toSide": "left",
      "label": "visualized by"
    }
  ]
}
```

### 流程圖

```json
{
  "nodes": [
    {
      "id": "a0b1c2d3e4f5a6b7",
      "type": "text",
      "x": 200,
      "y": 0,
      "width": 150,
      "height": 60,
      "text": "**Start**",
      "color": "4"
    },
    {
      "id": "b1c2d3e4f5a6b7c8",
      "type": "text",
      "x": 200,
      "y": 100,
      "width": 150,
      "height": 60,
      "text": "Step 1:\nGather data"
    },
    {
      "id": "c2d3e4f5a6b7c8d9",
      "type": "text",
      "x": 200,
      "y": 200,
      "width": 150,
      "height": 80,
      "text": "**Decision**\n\nIs data valid?",
      "color": "3"
    },
    {
      "id": "d3e4f5a6b7c8d9e0",
      "type": "text",
      "x": 400,
      "y": 200,
      "width": 150,
      "height": 60,
      "text": "Process data"
    },
    {
      "id": "e4f5a6b7c8d9e0f1",
      "type": "text",
      "x": 0,
      "y": 200,
      "width": 150,
      "height": 60,
      "text": "Request new data",
      "color": "1"
    },
    {
      "id": "f5a6b7c8d9e0f1a2",
      "type": "text",
      "x": 400,
      "y": 320,
      "width": 150,
      "height": 60,
      "text": "**End**",
      "color": "4"
    }
  ],
  "edges": [
    {
      "id": "a6b7c8d9e0f1a2b3",
      "fromNode": "a0b1c2d3e4f5a6b7",
      "fromSide": "bottom",
      "toNode": "b1c2d3e4f5a6b7c8",
      "toSide": "top"
    },
    {
      "id": "b7c8d9e0f1a2b3c4",
      "fromNode": "b1c2d3e4f5a6b7c8",
      "fromSide": "bottom",
      "toNode": "c2d3e4f5a6b7c8d9",
      "toSide": "top"
    },
    {
      "id": "c8d9e0f1a2b3c4d5",
      "fromNode": "c2d3e4f5a6b7c8d9",
      "fromSide": "right",
      "toNode": "d3e4f5a6b7c8d9e0",
      "toSide": "left",
      "label": "Yes",
      "color": "4"
    },
    {
      "id": "d9e0f1a2b3c4d5e6",
      "fromNode": "c2d3e4f5a6b7c8d9",
      "fromSide": "left",
      "toNode": "e4f5a6b7c8d9e0f1",
      "toSide": "right",
      "label": "No",
      "color": "1"
    },
    {
      "id": "e0f1a2b3c4d5e6f7",
      "fromNode": "e4f5a6b7c8d9e0f1",
      "fromSide": "top",
      "fromEnd": "none",
      "toNode": "b1c2d3e4f5a6b7c8",
      "toSide": "left",
      "toEnd": "arrow"
    },
    {
      "id": "f1a2b3c4d5e6f7a8",
      "fromNode": "d3e4f5a6b7c8d9e0",
      "fromSide": "bottom",
      "toNode": "f5a6b7c8d9e0f1a2",
      "toSide": "top"
    }
  ]
}
```

## ID 生成

節點和邊緣 ID 必須是唯一的字串。Obsidian 生成 16 字元的十六進位 ID：

```json
"id": "6f0ad84f44ce9c17"
"id": "a3b2c1d0e9f8g7h6"
"id": "1234567890abcdef"
```

此格式為 16 字元的小寫十六進位字串（64 位元隨機值）。

## 佈局指南

### 定位

- 座標可以是負數（畫布無限延伸）
- `x` 向右增加
- `y` 向下增加
- 位置指節點的左上角

### 建議尺寸

| 節點類型 | 建議寬度 | 建議高度 |
|-----------|-----------------|------------------|
| 小文字 | 200-300 | 80-150 |
| 中等文字 | 300-450 | 150-300 |
| 大文字 | 400-600 | 300-500 |
| 檔案預覽 | 300-500 | 200-400 |
| 連結預覽 | 250-400 | 100-200 |
| 群組 | 可變 | 可變 |

### 間距

- 在群組內留 20-50px 的內邊距
- 節點間距 50-100px 以提高可讀性
- 將節點對齊到網格（10 或 20 的倍數）以獲得更乾淨的佈局

## 驗證規則

1. 所有 `id` 值在節點和邊緣間必須是唯一的
2. `fromNode` 和 `toNode` 必須參照現有的節點 ID
3. 每種節點類型的必需欄位必須存在
4. `type` 必須是以下之一：`text`、`file`、`link`、`group`
5. `backgroundStyle` 必須是以下之一：`cover`、`ratio`、`repeat`
6. `fromSide`、`toSide` 必須是以下之一：`top`、`right`、`bottom`、`left`
7. `fromEnd`、`toEnd` 必須是以下之一：`none`、`arrow`
8. 顏色預設必須是 `"1"` 到 `"6"` 或有效的十六進位顏色

## 參考資料

- [JSON Canvas Spec 1.0](https://jsoncanvas.org/spec/1.0/)
- [JSON Canvas GitHub](https://github.com/obsidianmd/jsoncanvas)

## 報告
在解釋畫布結構、驗證結果或向使用者提供回饋時，您**必須**使用**繁體中文**。
