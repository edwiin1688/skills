# Bubblewrap 建置故障排除 (Bubblewrap Build Troubleshooter)

這份文件說明了 `bubblewrap_troubleshooter` 技能的目的與使用方式。此技能旨在協助解決執行 `bubblewrap build` 時常見的建置失敗問題，特別是針對 JVM 記憶體分配失敗與 JAVA_HOME 設定錯誤。

## 🛠️ 功能說明

當 Android 應用程式封裝工具 Bubblewrap 執行失敗並出現以下錯誤時，此技能提供標準的修復流程：

1.  **JVM Heap Memory Error (記憶體不足)**
    *   錯誤訊息: `Could not reserve enough space for ... object heap`
    *   原因: Gradle Daemon 預設請求的記憶體 (通常約 1.5GB) 超過系統當前可分配的連續空間。
2.  **JAVA_HOME Configuration Error (JDK 路徑錯誤)**
    *   錯誤訊息: `ERROR: JAVA_HOME is set to an invalid directory`
    *   原因: 系統環境變數指向了錯誤或不相容的 Java 版本，而非 Bubblewrap 內建的 JDK。

## 🔍 診斷與修復流程

此技能會引導 Agent 或使用者執行以下步驟：

### 1. 驗證與設定 JDK
Bubblewrap 通常會下載專用的 JDK (位於 `~/.bubblewrap/jdk/`)。為了確保相容性，我們應優先使用此版本，而非系統內全域安裝的 Java。

### 2. 限制 Gradle 記憶體用量
透過設定環境變數 `GRADLE_OPTS` 加入 `-Dorg.gradle.jvmargs=-Xmx1024m`，將最大堆疊記憶體限制在 1024MB。這通常足以應付 TWA (Trusted Web Activity) 專案的建置，同時避免記憶體不足的錯誤。

## 💻 使用範例 (PowerShell)

若您需要手動執行修復，請在 PowerShell 終端機中執行以下命令，然後再重新嘗試建置：

```powershell
# 1. 自動尋找 Bubblewrap 內建的 JDK 路徑
$jdkPath = Get-ChildItem "C:\Users\$env:USERNAME\.bubblewrap\jdk\jdk-*" | Select-Object -First 1 -ExpandProperty FullName

# 2. 設定當前 Session 的 JAVA_HOME
$env:JAVA_HOME = $jdkPath

# 3. 限制 Gradle 記憶體上限為 1024MB
$env:GRADLE_OPTS = "-Dorg.gradle.jvmargs=-Xmx1024m"

# 4. 驗證設定
Write-Host "設定 JDK 路徑: $env:JAVA_HOME"
Write-Host "設定 Gradle 參數: $env:GRADLE_OPTS"

# 5. 再次執行建置
bubblewrap build
```

## 📂 檔案結構
- `SKILL.md`: 給 Agent 閱讀的詳細指令與邏輯定義。
- `README.md`: 本說明文件。
