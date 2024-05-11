# 3-2-1_sync
One-way sync from SSD to HDD and HDD to GD. Windows only.

# Architecture
![ssd-hdd-gd](https://github.com/ikhwanperwira/3-2-1_sync/assets/72451078/5a630720-af67-4d41-b6b6-d8d31a1ca26e)

# Prerequisites
* oogle Drive Desktop for Windows.
* SSD with drive letter `D:`, add empty folder `D:\ssd-sync` and `D:\ssd-sync\robocopy-autosync`.
* VHD (HDD) with drive letter `E:`, add empty folder `E:\vhd-sync`.

# SSD to HDD
Sync script from SSD to HDD, save as `D:\ssd-sync\robocopy-autosync\ssd-to-vhd-robocopy-script.bat`:
```cmd
robocopy D:\ssd-sync E:\vhd-sync /LOG+:D:\ssd-sync\robocopy-autosync\ssd-to-vhd-robocopy-log.txt /V /DST /COPYALL /E /MIR /XO /XC /FFT ^
/SECFIX /TIMFIX ^
/W:1 /R:2 ^
/NP /NFL /NDL ^
/XF *tmp* *temp* *robocopy-log* ^
/XD *tmp* *temp* ^
/XF *.pyc *.exe *.log *.debug .idea *.sublime* *.dll *.o Thumbs.db .DS_Store *.bak *.old thumbs.db *.cache *.msi *.exe *.dmg *.obj *.class *.~* *.thumbnail *.thumb ^
/XD .venv node_modules .pio .git .vscode .idea *.sublime* *.dll *.o Thumbs.db .DS_Store *.bak *.old thumbs.db *.cache *.msi *.exe *.dmg *.obj *.class *.~* *.thumbnail *.thumb *build* *test* *tests* *logs* *output* *cache* *cached* *temporary* *thumbnails* *thumbs* *dependencies* *libs* *lib*  *.sublime*


rem Source and archive file paths
set "file=D:\ssd-sync\robocopy-autosync\ssd-to-vhd-robocopy-log.txt"
set "archive=A:\robocopy-log-archive\ssd-to-vhd-log"

rem File size limit (10 MB)
set "limit=10485760"

rem Get current date and time
for /f "tokens=2 delims==" %%a in ('wmic os get localdatetime /value') do set "dt=%%a"
set "YYYY=%dt:~0,4%"
set "MM=%dt:~4,2%"
set "DD=%dt:~6,2%"
set "HH=%dt:~8,2%"
set "MIN=%dt:~10,2%"
set "SS=%dt:~12,2%"

rem Check if the archive folder exists, if not create it
if not exist "%archive%" (
    mkdir "%archive%"
    echo Archive folder created.
)

rem Get size of the file
for %%A in ("%file%") do set "size=%%~zA"

rem Check if file size is greater than the limit
if %size% GTR %limit% (
    rem Move the file to the archive with new filename
    move "%file%" "%archive%\ssd-to-vhd-robocopy-log-%YYYY%_%MM%_%DD%-%HH%_%MIN%_%SS%.txt"
    echo File moved to archive.
) else (
    echo File size is not greater than 10MB.
)
```

# Task Scheduler for Batch Script
## General
* Run whether user is logged or not (Do not store password).
* Run with highest previlege
* Hidden
## Triggers
* Begin the task at startup, repeat task every 5 minutes for a duration of indefinetely.
* Delay task 3 seconds before start.
* Enabled
## Actions
Start a program `cmd`, with arguments `/C "D:\ssd-sync\robocopy-autosync\ssd-to-vhd-robocopy-script.bat"`.
## Conditions
* No conditions
## Settings
* Allow task to be run on demands
* If the task already running, do not start a new instance (do nothing).
## Configuration File (Importable)
Save it as `robocopy-autosync.xml`:
```
<?xml version="1.0" encoding="UTF-16"?>
<Task version="1.4" xmlns="http://schemas.microsoft.com/windows/2004/02/mit/task">
  <RegistrationInfo>
    <Date>2024-05-10T05:13:09.3042459</Date>
    <Author>LAPTOP-DJH6CVKH\User</Author>
    <URI>\Robocopy\ssd-to-hdd-scheduler</URI>
  </RegistrationInfo>
  <Triggers>
    <BootTrigger>
      <Repetition>
        <Interval>PT5M</Interval>
        <StopAtDurationEnd>false</StopAtDurationEnd>
      </Repetition>
      <Enabled>true</Enabled>
      <Delay>PT3S</Delay>
    </BootTrigger>
  </Triggers>
  <Principals>
    <Principal id="Author">
      <UserId>S-1-5-21-170095007-379780964-48905099-1001</UserId>
      <LogonType>S4U</LogonType>
      <RunLevel>HighestAvailable</RunLevel>
    </Principal>
  </Principals>
  <Settings>
    <MultipleInstancesPolicy>IgnoreNew</MultipleInstancesPolicy>
    <DisallowStartIfOnBatteries>false</DisallowStartIfOnBatteries>
    <StopIfGoingOnBatteries>false</StopIfGoingOnBatteries>
    <AllowHardTerminate>false</AllowHardTerminate>
    <StartWhenAvailable>false</StartWhenAvailable>
    <RunOnlyIfNetworkAvailable>false</RunOnlyIfNetworkAvailable>
    <IdleSettings>
      <StopOnIdleEnd>false</StopOnIdleEnd>
      <RestartOnIdle>false</RestartOnIdle>
    </IdleSettings>
    <AllowStartOnDemand>true</AllowStartOnDemand>
    <Enabled>true</Enabled>
    <Hidden>true</Hidden>
    <RunOnlyIfIdle>false</RunOnlyIfIdle>
    <DisallowStartOnRemoteAppSession>false</DisallowStartOnRemoteAppSession>
    <UseUnifiedSchedulingEngine>true</UseUnifiedSchedulingEngine>
    <WakeToRun>false</WakeToRun>
    <ExecutionTimeLimit>PT0S</ExecutionTimeLimit>
    <Priority>7</Priority>
  </Settings>
  <Actions Context="Author">
    <Exec>
      <Command>cmd</Command>
      <Arguments>/C "D:\ssd-sync\robocopy-autosync\ssd-to-vhd-robocopy-script.bat"</Arguments>
    </Exec>
  </Actions>
</Task>
```

# HDD to GD
Just install official Google Drive for desktop, then add `E:\vhd-sync` as sync folder. Note that this is two-way sync, no configuration in Google Drive app to make one way sync, therefore it's not intended as two-way. So, never upload any files from Google Drive sites, your PC will tries download and store it in `E:\vhd-sync` which useless, it will be overriden by SSD to HDD one-way script.
