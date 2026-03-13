---
name: os-detector
description: 自動判斷當前作業系統環境 (Windows/macOS)
---

# OS 偵測器

此技能用於自動檢測當前的作業系統環境（Windows 或 macOS）。

## 功能

- 判斷執行環境是 Windows、macOS 還是 Linux。
- 提供詳細的環境資訊（Platform、Release）。

## 使用方法

執行以下指令：

```bash
node os-detector/scripts/detect_os.js
```

## 輸出範例

```json
{
  "os": "Windows",
  "platform": "win32",
  "release": "10.0.22631"
}
```

## 報告
在向使用者報告作業系統詳細資訊時，您**必須**使用**繁體中文**。
