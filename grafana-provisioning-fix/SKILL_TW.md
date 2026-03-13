---
name: Grafana Provisioning Lock Fixer
description: 偵測並解決 Grafana 中的「來源不符」（HTTP 409）錯誤，這種錯誤發生在資源因檔案配置鎖定而無法透過 API 刪除時。
---

# Grafana 配置鎖定修復工具

此技能協助解決在嘗試透過 API 修改或刪除 Grafana 資源（儀表板、警報規則、聯絡點）時出現的頑固 **HTTP 409 衝突** 錯誤。

## 🚨 症狀偵測

尋找包含以下內容的錯誤日誌：
- `HTTP 409`
- `messageId: alerting.provenanceMismatch`
- `cannot delete with provided provenance '', needs 'file'`
- `StoredProvenance: file`

這表示該資源最初是由 YAML 檔案（配置）建立的，且 Grafana 資料庫已將其鎖定以防止 API 修改。

## 🛠 修復策略：SQLite 手術

由於 API 被封鎖，最可靠的修復方法是直接修改 Grafana SQLite 資料庫以移除「鎖定」（來源記錄）。

### 方法 1：透過 Docker 自動修復（推薦）
如果您在 Docker Compose 中運行，可以動態執行此修復。

1. **掛載卷宗**：確保您的設定容器掛載了 Grafana 資料卷宗。
   ```yaml
   volumes:
     - grafana-data:/var/lib/grafana
   ```
2. **安裝 SQLite**：確保您的容器中有 `sqlite3` 可用。
3. **執行 SQL**：
   ```bash
   # 對於 Grafana v11+（儲存在 'provenance_type' 表中）
   sqlite3 /var/lib/grafana/grafana.db "DELETE FROM provenance_type WHERE record_key = '$UID';"
   
   # 對於較舊版本（有時直接儲存在資源表中）
   sqlite3 /var/lib/grafana/grafana.db "UPDATE alert_rule SET provenance = NULL WHERE uid = '$UID';"
   ```

### 方法 2：手動互動修復
1. 進入 Grafana 容器：
   ```bash
   docker compose exec grafana bash
   ```
   （可能需要安裝 sqlite3：`apk add sqlite`）
2. 執行清理指令：
   ```bash
   sqlite3 /var/lib/grafana/grafana.db "DELETE FROM provenance_type WHERE record_key = 'YOUR_LOCKED_UID';"
   ```

## 📦 資源腳本
在 `resources/` 資料夾中提供了一個參考腳本 `fix_provenance.sh`。它封裝了檢查和修復邏輯。
