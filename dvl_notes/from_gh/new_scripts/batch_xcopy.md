Here's an **enhanced batch script** that checks for **network availability** before running the sync operation and is **optimized for Scheduled Task** use. It includes:

✅ **Network connectivity check** (ping test)  
✅ **Drive mapping verification** (with auto-reconnect if needed)  
✅ **XCopy sync with exclusions** (only newer files)  
✅ **Proper error logging** (for troubleshooting)  
✅ **Exit codes** for Scheduled Task monitoring  

---

### **Final Script (`network_sync.cmd`)**
```batch
@echo off
setlocal enabledelayedexpansion

:: ===== CONFIGURATION =====
set NETWORK_TEST_IP=8.8.8.8       :: Google DNS for connectivity test
set PING_TIMEOUT=2000             :: Milliseconds (adjust if needed)

set DRIVE1=X
set DRIVE2=Y
set DRIVE3=Z
set NETPATH1=\\server1\share1
set NETPATH2=\\server2\share2
set NETPATH3=\\server3\share3

set SOURCE_FOLDER=C:\Local\Folder
set DESTINATION_FOLDER=\\server1\share1\Destination

:: Folders to exclude (separate with spaces)
set EXCLUDE_DIRS="Temp" "Backup" "Logs"

:: Logging
set LOG_FOLDER=C:\SyncLogs
if not exist "%LOG_FOLDER%" mkdir "%LOG_FOLDER%"
set LOG_FILE="%LOG_FOLDER%\Sync_%date:~-4,4%%date:~-10,2%%date:~-7,2%_%time:~0,2%%time:~3,2%.log"

:: ===== INITIALIZE LOG =====
echo [%date% %time%] Starting synchronization >> %LOG_FILE%
echo Source: %SOURCE_FOLDER% >> %LOG_FILE%
echo Destination: %DESTINATION_FOLDER% >> %LOG_FILE%

:: ===== NETWORK AVAILABILITY CHECK =====
echo Checking network connectivity to %NETWORK_TEST_IP%... >> %LOG_FILE%
ping -n 1 -w %PING_TIMEOUT% %NETWORK_TEST_IP% >nul 2>&1

if errorlevel 1 (
    echo [ERROR] No network connectivity detected >> %LOG_FILE%
    exit /b 101
) else (
    echo Network connectivity confirmed >> %LOG_FILE%
)

:: ===== DRIVE MAPPING VERIFICATION =====
set DRIVE_ERROR=0

for %%D in (%DRIVE1%, %DRIVE2%, %DRIVE3%) do (
    net use %%D: 2>nul | find "%%D:" >nul
    if errorlevel 1 (
        echo [WARNING] Drive %%D: not mapped - attempting to reconnect >> %LOG_FILE%
        call :MAP_DRIVE %%D
        if !ERRORLEVEL! neq 0 set /a DRIVE_ERROR+=1
    )
)

if %DRIVE_ERROR% gtr 0 (
    echo [ERROR] Failed to map %DRIVE_ERROR% network drive(s) >> %LOG_FILE%
    exit /b 102
)

:: ===== BUILD EXCLUSION LIST =====
set EXCLUDE_OPTIONS=
for %%i in (%EXCLUDE_DIRS%) do (
    set EXCLUDE_OPTIONS=!EXCLUDE_OPTIONS! /EXCLUDE:%%i
)

:: ===== PERFORM SYNC =====
echo Starting synchronization with XCopy... >> %LOG_FILE%
echo Excluded folders: %EXCLUDE_DIRS% >> %LOG_FILE%

xcopy "%SOURCE_FOLDER%" "%DESTINATION_FOLDER%" /D /E /C /I /H /R /Y /Q !EXCLUDE_OPTIONS! >> %LOG_FILE% 2>&1

if errorlevel 1 (
    echo [WARNING] XCopy completed with warnings (ErrorLevel: %ERRORLEVEL%) >> %LOG_FILE%
    exit /b 1
) else (
    echo [SUCCESS] Synchronization completed successfully >> %LOG_FILE%
    exit /b 0
)

:: ===== SUBROUTINE FOR DRIVE MAPPING =====
:MAP_DRIVE
setlocal
set DRIVE_LETTER=%1

for %%N in (1 2 3) do (
    if "%DRIVE_LETTER%"=="!DRIVE%%N!" (
        net use %DRIVE_LETTER%: "!NETPATH%%N!" /persistent:no >> %LOG_FILE% 2>&1
        if errorlevel 1 (
            echo [ERROR] Failed to map %DRIVE_LETTER%: to !NETPATH%%N! >> %LOG_FILE%
            endlocal & exit /b 1
        )
        echo Successfully mapped %DRIVE_LETTER%: to !NETPATH%%N! >> %LOG_FILE%
        endlocal & exit /b 0
    )
)

echo [ERROR] Drive %DRIVE_LETTER% not found in configuration >> %LOG_FILE%
endlocal & exit /b 1
```

---

### **Key Improvements for Scheduled Tasks**

1. **Network Connectivity Check**
   - Pings a reliable server (`8.8.8.8`) before proceeding
   - Fails fast with exit code `101` if no network

2. **Drive Mapping Resilience**
   - Auto-reconnects unmapped drives
   - Returns exit code `102` if drives can't be mapped

3. **Proper Exit Codes**
   - `0` = Success
   - `1` = XCopy warnings
   - `101` = Network unavailable
   - `102` = Drive mapping failed

4. **Comprehensive Logging**
   - Date/time stamps all operations
   - Stores logs in dedicated folder (`C:\SyncLogs`)

5. **Scheduled Task Configuration**
   - Runs whether user is logged in or not
   - Configure to retry on failure

---

### **How to Set Up the Scheduled Task**

1. **Save the script** as `network_sync.cmd`
2. **Open Task Scheduler** and create a new task:
   - General Tab:
     - Name: `Network Folder Sync`
     - Run whether user is logged on or not
     - Run with highest privileges
   - Triggers Tab:
     - Set your schedule (e.g., daily at 2 AM)
   - Actions Tab:
     - Program: `cmd.exe`
     - Arguments: `/c "C:\path\to\network_sync.cmd"`
   - Conditions Tab:
     - Uncheck "Start only if on AC power" if needed
   - Settings Tab:
     - Allow task to run on demand
     - If task fails: restart every 1 hour (up to 3 times)

3. **Test the task** by running it manually and checking:
   - The log file (`C:\SyncLogs\Sync_YYYYMMDD_HHMM.log`)
   - Task Scheduler's Last Run Result

---

### **Why This Works Better for Scheduled Tasks**

- **Self-healing**: Attempts to reconnect drives automatically
- **Fail-fast**: Won't waste time if network is down
- **Auditability**: Detailed logs for troubleshooting
- **Reliability**: Proper exit codes let Task Scheduler handle retries

Let me know if you'd like any adjustments! 🚀
