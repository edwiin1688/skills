---
name: bubblewrap_troubleshooter
description: "Diagnose and fix common Bubblewrap/Gradle build errors, specifically JVM Header memory issues and JAVA_HOME misconfigurations."
---

# Bubblewrap Build Troubleshooter

This skill helps resolve failures when running `bubblewrap build`, particularly errors related to JVM memory allocation (`Could not reserve enough space`) or `JAVA_HOME` configuration.

## Common Error Symptoms

1.  **JVM Heap Size Error**:
    ```
    Error occurred during initialization of VM
    Could not reserve enough space for 1572864KB object heap
    ```
2.  **Daemon Start Failure**:
    ```
    org.gradle.api.GradleException: Unable to start the daemon process.
    ```
3.  **Invalid JAVA_HOME**:
    ```
    ERROR: JAVA_HOME is set to an invalid directory: ...
    ```

## Diagnosis Process

1.  **Check `JAVA_HOME`**:
    - Verify if the system `JAVA_HOME` points to a valid JDK.
    - Bubblewrap typically installs its own JDK in `~/.bubblewrap/jdk/`. It is often safer to use this internal JDK to avoid version conflicts (e.g., Gradle 8.x often requires JDK 17).

2.  **Check Memory Settings**:
    - The default Gradle daemon often requests 1.5GB+ (`-Xmx1536m`). On systems with limited free RAM (or specific Windows configurations), this fails.
    - Limiting the heap size to 1024MB (`-Xmx1024m`) usually resolves this without impacting build performance for this size of project.

## Resolution Steps (PowerShell)

Run the following commands in the terminal before attempting `bubblewrap build` again.

### 1. Identify Bubblewrap JDK Path
Find the valid JDK path. Usually:
`C:\Users\<User>\.bubblewrap\jdk\jdk-17.x.x`

### 2. Configure Environment Variables
Set the environment variables **temporarily** for the current session:

```powershell
# 1. Set JAVA_HOME to the Bubblewrap internal JDK (adjust path version if needed)
# Use Get-ChildItem to find the exact version folder if unsure
$jdkPath = Get-ChildItem "C:\Users\$env:USERNAME\.bubblewrap\jdk\jdk-*" | Select-Object -First 1 -ExpandProperty FullName
$env:JAVA_HOME = $jdkPath

# 2. Limit Gradle Memory
$env:GRADLE_OPTS = "-Dorg.gradle.jvmargs=-Xmx1024m"

# 3. Verify
Write-Host "Java Home Set To: $env:JAVA_HOME"
Write-Host "Gradle Opts Set To: $env:GRADLE_OPTS"
```

### 3. Run Build
```powershell
bubblewrap build
```

## Permanent Fix (Optional)
If this happens frequently, add the environment variables to the system permanently or update `bubblewrap` configuration to force these flags.
