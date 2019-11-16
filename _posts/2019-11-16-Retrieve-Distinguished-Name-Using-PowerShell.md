---
title:  "Retrieve Distinguished Name using PowerShell"
date:   2019-11-16 14:00:00
categories: 
- Blog
tags:
  - PowerShell
  - Active Directory
  - PowerShell function
  - DistinguishedName
---

### Retrieve Distinguished Name(s) with the help of PowerShell

In the past few days, I've been spending some time in Visual Studio 2019.
I am working on gaining some more knowledge in programming using C#.
A side project of mine includes authenticating/communicating with Active Directory, using the LDAP API. When you walk down this road, you can`t miss beeing exposed to a LDAP object named **distinguished name (DN)**.

Below you can see what the Microsoft Docs describes it as: 

>The LDAP API references an LDAP object by its distinguished name (DN). A DN is a sequence of relative distinguished names (RDN) connected by commas. An RDN is an attribute with an associated value in the form attribute=value; normally expressed in a UTF-8 string format.

*You can read more about it [here](https://docs.microsoft.com/en-us/previous-versions/windows/desktop/ldap/distinguished-names).*


When I have time to sit down and really focus on learning new things, I dont wanna spend too much time collecting the input data I need to get the code working as the way I want. 

I caught myself going back and forth in Visual Studio and my Active Directory Lab Domain Controller retrieving the DNs needed to complete my C#-code. **What`s a waste of time!**

So, what do you think I did? - Created a PowerShell function of course! :blush:

**Get-ADDistinguishedName**


````powershell
        function Get-ADDistinguishedName {

        [CmdletBinding()]

        param(
  
        [Parameter(Mandatory = $False,
        ValueFromPipeline = $False,
        Position = 3,
        HelpMessage = "Enter your ADUser account)")]
        [Alias('UserName')]
        [string[]]$ADUser,

        [Parameter(Mandatory = $False,
        ValueFromPipeline = $False,
        HelpMessage = "Enter your ADGroup account")]
        [Alias('Group')]
        [string[]]$ADGroup,

        [Parameter(Mandatory = $False,
        ValueFromPipeline = $False,
        HelpMessage = "Enter your ADComputer account")]
        [Alias('Comp')]
        [string[]]$ADComputer,

        
        [Parameter(Mandatory = $False,
        ValueFromPipeline = $False,
        HelpMessage = "Enter your ADOrganizationalUnit account")]
        [Alias('OrgU')]
        [string[]]$ADOrgUnit

        )


If ( ! (Get-module Activedirectory )) {

Import-Module ActiveDirectory

}    


try {

if ($ADUser -ne $null) {(Get-ADUser -Identity "$ADUser").DistinguishedName}

if ($ADGroup -ne $null) {(Get-ADGroup -Identity "$ADGroup").DistinguishedName}

if ($ADComputer -ne $null) {(Get-ADComputer -Identity "$ADComputer").DistinguishedName}

if ($ADOrgUnit -ne $null) {(Get-ADOrganizationalUnit -Filter "Name -like '$ADOrgUnit'").DistinguishedName}

}


catch {


Write-Host "$PSItem" 'Please check the spelling and try again.' -ForegroundColor Yellow

}

}
````

**Installing the module:**

Copy the entire text in the code block above.
Open PowerShell ISE, Visual Studio Code or your editor of choice.
Paste the code and save it as a .psm1-file named *Get-ADDistinguishedName*.

Create a new folder named *Get-ADDistinguishedName* (matching your .psm1-file) and then, move your
*Get-ADDistinguishedName* .psm1-file into the **folder** with the same name.

Then, move the folder containing your .psm1-file to your desired ````PSModulePath````.

See my selected path below: 

![ModulePath](/assets/images/DNs/MODULEPATH.PNG)

I also recommend reading the following article on how to install [PowerShell modules correctly](https://docs.microsoft.com/en-us/powershell/scripting/developer/module/installing-a-powershell-module?view=powershell-6).


You have now successfully installed the function and have it available in your PowerShell console:
![CMDLET](/assets/images/DNs/CMDLET.PNG)

**The following parameters is available for use:**
![PARAMS](/assets/images/DNs/PARAMS.PNG)

**Get the DN of a ADUser:**
````powershell
Get-ADDistinguishedName -ADUSer <UserName>
````
![ADUSER](/assets/images/DNs/ADUSER.PNG)

**Get the DN of a ADComputer:**
````powershell
Get-ADDistinguishedName -ADComputer <ComputerName>
````
![ADCOMP](/assets/images/DNs/ADCOMP.PNG)

**Get the DN of a ADGroup:**
````powershell
Get-ADDistinguishedName -ADGroup <ADGroupName>
````
![ADGROUP](/assets/images/DNs/ADGROUP.PNG)

**Get the DN of a AD Organization Unit:**
````powershell
Get-ADDistinguishedName -ADOrg <ADGroupName>
````
![ADOU](/assets/images/DNs/ADOUs.PNG)

**Checking multiple items at once? No problem!**

You start off by creating a simple array containing your items, and then, pipe the array into a ForEach-Object loop like shown below:
![ARRAY](/assets/images/DNs/ARRAY.PNG)

The result is written to the console:
![ARRAY2](/assets/images/DNs/ARRAY2.PNG)

The example is using the ````-ADOrgUnit```` parameter, but any available parameter can be used.

**PowerTip!** 

Instead of copying the DN from the console, you can edit the if statements so it pipes the output into clip.exe.

What is clip.exe?

>Redirects command output from the command line to the Windows clipboard. You can then paste this text output into other programs.

*Taken from [here](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/clip).*

*Example:*
````powershell
if ($ADComputer -ne $null) {(Get-ADComputer -Identity "$ADComputer").DistinguishedName | clip}
````

Please note that even nothing is written to the console, the function is working as intended. If you, for example, open Notepad and press Ctrl + V (or right-click and select *Paste*), the corresponding DN comes to show. No more waiting time by highlighting the text and selecting *copy* :blush:

![CLIP](/assets/images/DNs/CLIP.PNG)
















