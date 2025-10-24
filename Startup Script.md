```powershell
# User and Disk Management Script
# Note: Run this script as Administrator

# ===================================
# 1. Display All Local User Accounts
# ===================================
Write-Host "`n========== LOCAL USER ACCOUNTS ==========" -ForegroundColor Cyan
Write-Host "Listing all local users on this system:`n" -ForegroundColor Yellow

# Get all local users and display their names and status
Get-LocalUser | Format-Table Name, Enabled, Description -AutoSize

# ===================================
# 2. Show All Drives and Free Space
# ===================================
Write-Host "`n========== DISK DRIVE INFORMATION ==========" -ForegroundColor Cyan
Write-Host "Showing all drives and their free space:`n" -ForegroundColor Yellow

# Get all drives and display relevant information
Get-PSDrive -PSProvider FileSystem | Where-Object { $_.Used -ne $null } | 
    Select-Object Name, 
                  @{Name="Used (GB)"; Expression={[math]::Round($_.Used/1GB, 2)}},
                  @{Name="Free (GB)"; Expression={[math]::Round($_.Free/1GB, 2)}},
                  @{Name="Total (GB)"; Expression={[math]::Round(($_.Used + $_.Free)/1GB, 2)}} |
    Format-Table -AutoSize

# ===================================
# 3. Create New Local User
# ===================================
Write-Host "`n========== CREATE NEW USER ==========" -ForegroundColor Cyan
Write-Host "Creating a new local user account named 'TestUser'`n" -ForegroundColor Yellow

# Prompt for a secure password
$Password = Read-Host "Enter password for TestUser" -AsSecureString

# Create the new user
try {
    New-LocalUser "TestUser" -Password $Password -FullName "Test User" -Description "Test account created by script"
    Write-Host "`nSuccess! User 'TestUser' has been created." -ForegroundColor Green
} catch {
    Write-Host "`nError creating user: $_" -ForegroundColor Red
}

Write-Host "`n========== SCRIPT COMPLETE ==========" -ForegroundColor Cyan
