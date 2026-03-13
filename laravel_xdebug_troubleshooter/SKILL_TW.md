---
name: laravel_xdebug_troubleshooter
description: 診斷並修復 Laravel Sail Xdebug 中斷點問題。
---

# Laravel Sail Xdebug 除錯工具

> **注意：** 關於此技能的所有互動和回應都必須使用**繁體中文**。

當使用者回報 Xdebug 中斷點在 Laravel Sail 環境中無法正常工作時，使用此技能。

## 1. 環境配置檢查（`.env`）

檢查 `.env` 檔案中的以下配置：

-   `SAIL_XDEBUG_MODE`：應包含 `debug`（例如：`develop,debug`）。
-   `SAIL_XDEBUG_CONFIG`：應包含 `client_host=host.docker.internal` 和 `start_with_request=yes`。

**操作：** 如果缺少或設定錯誤，請更新 `.env`。

## 2. Docker Compose 配置檢查（`compose.yaml`）

檢查 `compose.yaml`（或 `docker-compose.yml`）中 `laravel.test` 服務的環境變數：

-   `XDEBUG_SESSION`：確保此環境變數設定為 `1` 以強制啟用 Xdebug。
    ```yaml
            environment:
                # ...
                XDEBUG_SESSION: 1
    ```

**操作：** 如果缺少 `XDEBUG_SESSION`，請將其新增到 `compose.yaml` 並通知使用者需要重新啟動（`sail down && sail up -d`）。

## 3. VS Code 配置檢查（`.vscode/launch.json`）

檢查 `.vscode/launch.json` 中的監聽器配置：

-   `port`：應為 `9003`。
-   `hostname`：應為 `"0.0.0.0"` 以監聽所有介面。
-   `pathMappings`：確保容器和主機之間的正確映射。
    ```json
    "pathMappings": {
        "/var/www/html": "${workspaceFolder}"
    }
    ```

**操作：** 如果 `hostname` 缺少或設定錯誤，請更新 `launch.json`。

## 4. 驗證

修復任何配置後，引導使用者：
1.  重新啟動 Sail：`./vendor/bin/sail down && ./vendor/bin/sail up -d`
2.  在 VS Code 中開始除錯（F5）。
3.  設定中斷點。
4.  觸發請求。

## 5. 進階診斷（如果仍然失敗）

如果問題持續存在：
1.  在 `.env` 中暫時啟用 Xdebug 本地日誌記錄：
    `SAIL_XDEBUG_CONFIG="... log=/tmp/xdebug.log"`
2.  重新啟動 Sail。
3.  監看日誌：`./vendor/bin/sail exec laravel.test cat /tmp/xdebug.log`。
4.  尋找「Time-out connecting to client」（網路問題）或「Step Debugger: enabled」（連線成功但錯過中斷點）。
