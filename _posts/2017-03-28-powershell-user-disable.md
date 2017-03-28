---
layout: post
title: Disabling and Moving Active Directory Users with Powershell
subtitle: or 'Posh' as some call it 
---
## The Problem

Sometimes when you're busy it's easy to forget a step in a process.

The usual process for unboarding a system user involved the following:

1. Disable user in active directory (AD)
2. Remove the user from AD groups 
3. Move the user to the 'Disabled' OU
4. Login to exchange server, set the property to only allow email from their own address (authenticate), so no one
   else is able to email this address without a bounceback.  

What I want to do is just type in a username and have Powershell take care of this.  Besides typing in credentials for the 
optional exchange server connection, it does just that.
  
So this is pretty straight forward, when you run it it will ask you to input their ad username, then 
when it tries to reach the exchange server it will prompt you for your exchange server credentials.
Once it's done it should close out and you should be all set.  At the time it only accepts a single username, but I wonder if it
may be possible to pass multiple usernames into an array, might have to come back to that one.
   
 ## Code
 
```powershell

Set-ExecutionPolicy Bypass -Scope Process
Import-Module activedirectory

# User to remove
"This moves desired user account to the disabled AD OU, removes their groups
 and sets the proper exchange permissions"

#declares entry to variable
$username = Read-Host 'Username of person to be disabled?'

# Disabled the account

Disable-ADAccount -Identity $username

# Move user to disabled accounts ou
Get-ADUser $username | Move-ADObject -TargetPath 'OU=TargetOUName,DC=mydomain,DC=com'

# Retrieve the user object and MemberOf property
	$user = Get-ADUser -Identity $username -Properties MemberOf
  
# Remove all group memberships (will leave Domain Users as this is NOT in the MemberOf property returned by Get-ADUser)
	foreach ($group in ($user | Select-Object -ExpandProperty MemberOf))
	{
		Remove-ADGroupMember -Identity $group -Members $user -Confirm:$false
	}


# Begin new session to exchange server

$Login = Get-Credential

$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri http://servername.domain.com/PowerShell/ -Authentication Kerberos -Credential $Login
Import-PSSession $Session

# Set the user in exchange to only allow messages from themselves
Set-Mailbox -Identity $username -AcceptMessagesOnlyFrom $username -RequireSenderAuthenticationEnabled $true

```
