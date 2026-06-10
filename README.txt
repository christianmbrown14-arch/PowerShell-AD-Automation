# PowerShell AD Automation

Automating Active Directory user provisioning with PowerShell, built on a Windows Server 2022 home lab.

## Overview

This project uses PowerShell to bulk-create Active Directory user accounts from a CSV file. The same task done by hand in the GUI would take an afternoon of clicking. This script does it in seconds, which is the kind of repetitive-task automation IT teams rely on.

## Environment

- Windows Server 2022 domain controller (home lab, domain: homelab.local)
- VirtualBox VM on a Beelink mini PC
- Windows PowerShell with the ActiveDirectory module

## What it does

1. Reads a CSV file of new users (first name, last name, username, department).
2. Loops through each row and creates an enabled AD user account with New-ADUser.
3. Populates each user's display name, login (SamAccountName), UPN, department, and password.

## Files

- `bulk-create-users.ps1` : the automation script
- `newusers.csv` : sample input data
- `screenshots/` : verification screenshots

## The script

```powershell
# bulk-create-users.ps1
# Creates Active Directory users in bulk from a CSV file.

# Lab password only. In production you would prompt for this or pull it from a secret store.
$password = ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force

# Read the CSV and create one user per row.
Import-Csv "C:\newusers.csv" | ForEach-Object {
    New-ADUser -Name "$($_.FirstName) $($_.LastName)" -GivenName $_.FirstName -Surname $_.LastName -SamAccountName $_.Username -UserPrincipalName "$($_.Username)@homelab.local" -Department $_.Department -AccountPassword $password -Enabled $true -Path "CN=Users,DC=homelab,DC=local"
}
```

## How it works

- `Import-Csv` reads the CSV into objects, one per row.
- The pipe (`|`) passes each row down to the next command.
- `ForEach-Object { ... }` runs New-ADUser once for each row.
- `$_` refers to the current row, so `$_.FirstName` pulls that user's first name.
- String interpolation, like `"$($_.FirstName) $($_.LastName)"`, builds the full display name from two separate columns.

## Verification

After running the script, Get-ADUser confirms the new users exist with their attributes:

```powershell
Get-ADUser -Filter * -Properties Department | Select-Object Name, SamAccountName, Department, Enabled
```

See the `screenshots/` folder for the results.

## Skills demonstrated

- PowerShell scripting: variables, the pipeline, loops, and string interpolation
- Active Directory administration: user provisioning with New-ADUser, querying with Get-ADUser
- Using CSV data as a source for automation
- Task automation and clear documentation

## What I learned

- PowerShell cmdlets follow a readable Verb-Noun pattern (Get-ADUser, New-ADUser), so you can often read what a command does before running it.
- Get-ADUser only returns a default set of properties unless you ask for more with the -Properties parameter.
- Precise syntax matters. A single misplaced space (for example, typing a parameter as "- Enabled" instead of "-Enabled") breaks the whole command.
- Reviewing a script before running it catches errors early, which is faster than debugging after the fact.

