# Home Lab 2: PowerShell Active Directory Automation

## Overview

Building on my Active Directory home lab, I used PowerShell to automate user account creation in Active Directory. Instead of creating accounts one at a time in the GUI, I wrote a script that reads a CSV file of users and creates them all at once. This is the kind of repetitive-task automation that IT teams rely on to save time and reduce errors.

## Environment

- Windows Server 2022 Domain Controller (domain: homelab.local)
- VirtualBox VM on a Beelink EQi13 Pro mini PC
- Windows PowerShell with the ActiveDirectory module

## Steps

1. Opened PowerShell as Administrator on the Domain Controller and confirmed the ActiveDirectory module was working with `Get-ADDomain`.
2. Queried the existing accounts with `Get-ADUser` to see the built-in defaults (Administrator, Guest, krbtgt).
3. Created a single user with `New-ADUser` and verified the account was created and enabled.
4. Built a CSV file (`newusers.csv`) with the first name, last name, username, and department for several users.
5. Wrote a script (`bulk-create-users.ps1`) that imports the CSV and loops through each row with `ForEach-Object`, creating one Active Directory user per row.
6. Ran the script and verified all the new users were created, with their departments, using `Get-ADUser`.
7. Confirmed the new users also appeared in Active Directory Users and Computers (the GUI).

## The Script

```powershell
$password = ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force

Import-Csv "C:\newusers.csv" | ForEach-Object {
    New-ADUser -Name "$($_.FirstName) $($_.LastName)" -GivenName $_.FirstName -Surname $_.LastName -SamAccountName $_.Username -UserPrincipalName "$($_.Username)@homelab.local" -Department $_.Department -AccountPassword $password -Enabled $true -Path "CN=Users,DC=homelab,DC=local"
}
```

## What I Learned

- PowerShell commands follow a readable Verb-Noun pattern (Get-ADUser, New-ADUser), so you can often tell what a command does just by reading it.
- Get-ADUser only returns a default set of properties unless you specifically request more with the -Properties parameter.
- Precise syntax matters. A single misplaced space (for example "- Enabled" instead of "-Enabled") breaks the entire command.
- Automating a repetitive task with a script that reads from a CSV is far faster and less error-prone than doing it by hand in the GUI.
- Reviewing a script before running it catches mistakes early, which beats debugging after the fact.

## Screenshots














                                                    
