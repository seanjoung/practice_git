# practice_git


windows file system duplicate control

### 1)DeleteFromTxt.ps1
####최종 삭제 스크립트 deletefromtext

# 삭제 대상 파일 목록 경로
$deleteListFile = "C:\Scripts\deletelist.txt"

# 로그 파일 경로
$logFile = "C:\Scripts\delete_log.txt"

# 삭제 대상 리스트 읽기 (CP949 인코딩)
if (Test-Path -Path $deleteListFile) {
    $filesToDelete = Get-Content -Path $deleteListFile -Encoding OEM | ForEach-Object { $_.Trim('"', ',') }
    Write-Host "삭제 대상 파일 목록을 $deleteListFile 에서 불러왔습니다. 총 파일 수: $($filesToDelete.Count)" -ForegroundColor Cyan
} else {
    Write-Host "삭제 대상 파일 목록($deleteListFile)을 찾을 수 없습니다." -ForegroundColor Red
    exit
}

# 삭제 실행 및 로그 기록
Write-Host "파일 삭제 프로세스를 시작합니다..." -ForegroundColor Yellow
$logEntries = @()

foreach ($filePath in $filesToDelete) {
    Write-Host "경로 확인: $filePath" -ForegroundColor Cyan
    if (Test-Path -Path $filePath) {
        Write-Host "파일이 존재합니다." -ForegroundColor Green
    } else {
        Write-Host "파일을 찾을 수 없습니다." -ForegroundColor Yellow
        $logEntries += "파일 없음: $filePath - $(Get-Date)"
        continue
    }

    # 사용자에게 삭제 여부 확인
    $response = Read-Host "Would you delete this file? (y/n)"
    if ($response -eq 'y' -or $response -eq 'Y') {
        try {
            Remove-Item -Path $filePath -Force
            Write-Host "삭제됨: $filePath" -ForegroundColor Green
            $logEntries += "삭제됨: $filePath - $(Get-Date)"
        } catch {
            Write-Host "삭제 실패: $filePath - 오류: $_" -ForegroundColor Red
            $logEntries += "삭제 실패: $filePath - 오류: $_ - $(Get-Date)"
        }
    } else {
        Write-Host "삭제 취소됨: $filePath" -ForegroundColor Yellow
        $logEntries += "삭제 취소됨: $filePath - $(Get-Date)"
    }
}

# 로그 파일 저장 (CP949 인코딩)
$logEntries | Out-File -FilePath $logFile -Encoding OEM
Write-Host "삭제 로그가 $logFile 에 저장되었습니다." -ForegroundColor Green
### 2) deletelist.txt
$deleteList = @"
"H:\test - test.mov",
"@
$deleteList | Out-File -FilePath "C:\Scripts\deletelist.txt" -Encoding utf8BOM


PS C:\Scripts> cd C:\Scripts
>> Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy Bypass -Force
>> .\DeleteFromTxt.ps1

