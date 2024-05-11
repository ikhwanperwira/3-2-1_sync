# 3-2-1_sync
One way sync from SSD to HDD and HDD to GD.

# Docs
TODO!

# Main Batch Script
```
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
