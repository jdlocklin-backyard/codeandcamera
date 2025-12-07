---
title: "Automated Backup Script with PowerShell"
description: "Create a scheduled backup solution for your important files"
tags:
  - automation
  - powershell
  - project
  - beginner
status: new
---

# Automated Backup Script with PowerShell

Never lose important files again with this automated backup solution.

## Overview

This project creates a PowerShell script that automatically backs up specified folders to a destination of your choice—local drive, network share, or cloud-synced folder. Set it and forget it with Windows Task Scheduler.

---

## Project Details

| Detail | Information |
|--------|-------------|
| **Difficulty** | Beginner |
| **Time Required** | 30-45 minutes |
| **Category** | Automation |
| **Last Updated** | December 2025 |

---

## What You'll Learn

- PowerShell scripting basics
- File operations and error handling
- Logging and notifications
- Windows Task Scheduler automation
- Incremental vs full backup strategies

---

## Prerequisites

- Windows 10/11 or Windows Server
- PowerShell 5.1+ (included with Windows)
- Administrator access for Task Scheduler

---

## Step-by-Step Guide

### Step 1: Create the Backup Script

Create a new file called `backup-script.ps1`:

```powershell title="backup-script.ps1"
#Requires -Version 5.1
<#
.SYNOPSIS
    Automated backup script for important files and folders.
.DESCRIPTION
    Creates timestamped backups with logging and optional email notifications.
.AUTHOR
    Code and Camera
.VERSION
    1.0.0
#>

# ============================================
# CONFIGURATION - Modify these settings
# ============================================

$Config = @{
    # Folders to backup (add your paths here)
    SourceFolders = @(
        "$env:USERPROFILE\Documents"
        "$env:USERPROFILE\Pictures"
        # "$env:USERPROFILE\Desktop"
        # "D:\Projects"
    )
    
    # Backup destination (local, network share, or cloud-synced folder)
    DestinationRoot = "E:\Backups"
    
    # Backup retention (days) - older backups will be deleted
    RetentionDays = 30
    
    # Log file location
    LogPath = "$env:USERPROFILE\Logs\backup-log.txt"
    
    # Email notification settings (optional)
    EnableEmail = $false
    SmtpServer = "smtp.gmail.com"
    SmtpPort = 587
    EmailFrom = "your-email@gmail.com"
    EmailTo = "your-email@gmail.com"
    # Store password securely: Read-Host -AsSecureString | ConvertFrom-SecureString
    EmailPasswordFile = "$env:USERPROFILE\.email-cred"
}

# ============================================
# FUNCTIONS
# ============================================

function Write-Log {
    param([string]$Message, [string]$Level = "INFO")
    
    $Timestamp = Get-Date -Format "yyyy-MM-dd HH:mm:ss"
    $LogMessage = "[$Timestamp] [$Level] $Message"
    
    # Ensure log directory exists
    $LogDir = Split-Path $Config.LogPath -Parent
    if (!(Test-Path $LogDir)) {
        New-Item -ItemType Directory -Path $LogDir -Force | Out-Null
    }
    
    # Write to log file and console
    Add-Content -Path $Config.LogPath -Value $LogMessage
    
    switch ($Level) {
        "ERROR"   { Write-Host $LogMessage -ForegroundColor Red }
        "WARNING" { Write-Host $LogMessage -ForegroundColor Yellow }
        "SUCCESS" { Write-Host $LogMessage -ForegroundColor Green }
        default   { Write-Host $LogMessage }
    }
}

function Start-Backup {
    param([string]$SourcePath, [string]$DestPath)
    
    try {
        # Use Robocopy for reliable copying
        $RobocopyArgs = @(
            $SourcePath
            $DestPath
            "/MIR"          # Mirror directory tree
            "/R:3"          # Retry 3 times
            "/W:5"          # Wait 5 seconds between retries
            "/MT:8"         # 8 threads for faster copying
            "/NP"           # No progress (cleaner logs)
            "/NDL"          # No directory list
            "/NFL"          # No file list (comment out for verbose)
            "/XA:SH"        # Exclude system and hidden files
            "/XD", "`$RECYCLE.BIN", "System Volume Information"
        )
        
        $Result = & robocopy @RobocopyArgs
        
        # Robocopy exit codes: 0-7 are success, 8+ are errors
        if ($LASTEXITCODE -lt 8) {
            return $true
        } else {
            Write-Log "Robocopy failed with exit code: $LASTEXITCODE" "ERROR"
            return $false
        }
    }
    catch {
        Write-Log "Backup error: $_" "ERROR"
        return $false
    }
}

function Remove-OldBackups {
    param([string]$BackupRoot, [int]$RetentionDays)
    
    $CutoffDate = (Get-Date).AddDays(-$RetentionDays)
    
    Get-ChildItem -Path $BackupRoot -Directory | 
        Where-Object { $_.CreationTime -lt $CutoffDate } |
        ForEach-Object {
            Write-Log "Removing old backup: $($_.Name)" "WARNING"
            Remove-Item $_.FullName -Recurse -Force
        }
}

function Send-NotificationEmail {
    param([string]$Subject, [string]$Body, [bool]$IsError = $false)
    
    if (!$Config.EnableEmail) { return }
    
    try {
        $Password = Get-Content $Config.EmailPasswordFile | ConvertTo-SecureString
        $Credential = New-Object PSCredential($Config.EmailFrom, $Password)
        
        $EmailParams = @{
            From = $Config.EmailFrom
            To = $Config.EmailTo
            Subject = $Subject
            Body = $Body
            SmtpServer = $Config.SmtpServer
            Port = $Config.SmtpPort
            UseSsl = $true
            Credential = $Credential
        }
        
        Send-MailMessage @EmailParams
        Write-Log "Email notification sent" "INFO"
    }
    catch {
        Write-Log "Failed to send email: $_" "ERROR"
    }
}

# ============================================
# MAIN EXECUTION
# ============================================

$StartTime = Get-Date
$BackupDate = Get-Date -Format "yyyy-MM-dd_HHmmss"
$TotalSize = 0
$SuccessCount = 0
$FailCount = 0

Write-Log "========================================" "INFO"
Write-Log "BACKUP STARTED" "INFO"
Write-Log "========================================" "INFO"

# Create timestamped backup folder
$BackupDestination = Join-Path $Config.DestinationRoot $BackupDate

# Verify destination is accessible
if (!(Test-Path $Config.DestinationRoot)) {
    try {
        New-Item -ItemType Directory -Path $Config.DestinationRoot -Force | Out-Null
        Write-Log "Created destination root: $($Config.DestinationRoot)" "INFO"
    }
    catch {
        Write-Log "Cannot access destination: $($Config.DestinationRoot)" "ERROR"
        Send-NotificationEmail "Backup FAILED" "Cannot access backup destination" $true
        exit 1
    }
}

# Backup each source folder
foreach ($Source in $Config.SourceFolders) {
    if (!(Test-Path $Source)) {
        Write-Log "Source not found, skipping: $Source" "WARNING"
        continue
    }
    
    $FolderName = Split-Path $Source -Leaf
    $Dest = Join-Path $BackupDestination $FolderName
    
    Write-Log "Backing up: $Source -> $Dest" "INFO"
    
    # Calculate source size
    $Size = (Get-ChildItem $Source -Recurse -ErrorAction SilentlyContinue | 
             Measure-Object -Property Length -Sum).Sum / 1GB
    
    if (Start-Backup -SourcePath $Source -DestPath $Dest) {
        Write-Log "Completed: $FolderName ({0:N2} GB)" -f $Size "SUCCESS"
        $TotalSize += $Size
        $SuccessCount++
    } else {
        Write-Log "Failed: $FolderName" "ERROR"
        $FailCount++
    }
}

# Clean up old backups
Write-Log "Checking for old backups to remove..." "INFO"
Remove-OldBackups -BackupRoot $Config.DestinationRoot -RetentionDays $Config.RetentionDays

# Calculate duration
$Duration = (Get-Date) - $StartTime

# Summary
Write-Log "========================================" "INFO"
Write-Log "BACKUP COMPLETED" "INFO"
Write-Log "Duration: $($Duration.ToString('hh\:mm\:ss'))" "INFO"
Write-Log "Total Size: {0:N2} GB" -f $TotalSize "INFO"
Write-Log "Successful: $SuccessCount | Failed: $FailCount" "INFO"
Write-Log "========================================" "INFO"

# Send notification
$Status = if ($FailCount -eq 0) { "SUCCESS" } else { "COMPLETED WITH ERRORS" }
$EmailBody = @"
Backup $Status

Duration: $($Duration.ToString('hh\:mm\:ss'))
Total Size: $([math]::Round($TotalSize, 2)) GB
Successful: $SuccessCount
Failed: $FailCount
Destination: $BackupDestination
"@

Send-NotificationEmail "Backup $Status - $BackupDate" $EmailBody ($FailCount -gt 0)
```

### Step 2: Test the Script

```powershell
# Run from PowerShell (as Administrator for best results)
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
.\backup-script.ps1
```

### Step 3: Schedule with Task Scheduler

1. Open **Task Scheduler** (search in Start menu)
2. Click **Create Task** (not Basic Task)
3. **General tab:**
   - Name: `Automated Backup`
   - Check: "Run whether user is logged on or not"
   - Check: "Run with highest privileges"

4. **Triggers tab:**
   - New → Daily → Set your preferred time
   - Recommended: 2:00 AM or during low-usage hours

5. **Actions tab:**
   - New → Start a program
   - Program: `powershell.exe`
   - Arguments: `-ExecutionPolicy Bypass -File "C:\Scripts\backup-script.ps1"`

6. **Conditions tab:**
   - Uncheck: "Start only if on AC power" (for laptops)
   - Check: "Wake the computer to run this task" (optional)

7. Click **OK** and enter your password

---

## Configuration Options

### Backup to Network Share

```powershell
$Config = @{
    DestinationRoot = "\\NAS\Backups\MyPC"
    # ... rest of config
}
```

### Backup to Cloud-Synced Folder

```powershell
$Config = @{
    # OneDrive
    DestinationRoot = "$env:USERPROFILE\OneDrive\Backups"
    
    # Dropbox
    # DestinationRoot = "$env:USERPROFILE\Dropbox\Backups"
    
    # Google Drive
    # DestinationRoot = "G:\My Drive\Backups"
}
```

### Enable Email Notifications

```powershell
# First, securely store your email password
Read-Host "Enter email password" -AsSecureString | 
    ConvertFrom-SecureString | 
    Out-File "$env:USERPROFILE\.email-cred"

# Then enable in config
$Config = @{
    EnableEmail = $true
    SmtpServer = "smtp.gmail.com"
    SmtpPort = 587
    EmailFrom = "your-email@gmail.com"
    EmailTo = "your-email@gmail.com"
    EmailPasswordFile = "$env:USERPROFILE\.email-cred"
}
```

!!! tip "Gmail Users"
    You'll need to use an [App Password](https://support.google.com/accounts/answer/185833) instead of your regular password.

---

## Common Issues & Troubleshooting

### Script won't run
- Check execution policy: `Get-ExecutionPolicy`
- Run: `Set-ExecutionPolicy RemoteSigned -Scope CurrentUser`

### Access denied errors
- Run PowerShell as Administrator
- Check folder permissions
- Verify network share credentials are cached

### Task Scheduler not running
- Check "Run whether user is logged on or not"
- Verify account has "Log on as batch job" rights
- Check task history for errors

---

## Next Steps

- [ ] Add compression (zip) for archival backups
- [ ] Implement incremental backups with snapshots
- [ ] Add Telegram/Discord notifications
- [ ] Create restore script for easy recovery
- [ ] Add pre/post backup hooks for databases

---

## Resources

- [Robocopy Documentation](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/robocopy)
- [Task Scheduler Guide](https://docs.microsoft.com/en-us/windows/win32/taskschd/task-scheduler-start-page)
- [PowerShell Best Practices](https://docs.microsoft.com/en-us/powershell/scripting/developer/cmdlet/cmdlet-development-guidelines)
