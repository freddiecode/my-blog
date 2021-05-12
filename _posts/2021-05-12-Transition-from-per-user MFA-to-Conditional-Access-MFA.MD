---
title:  "Transition from per-user MFA to Conditional Access MFA"
date:   2021-05-12 21:00:00
categories: 
- Blog
tags:
  - PowerShell
  - Microsoft Azure
  - Conditional Access
  
---
### Transition from per-user MFA to Conditional Access MFA



>**What is Conditional Access?**
>"..The modern security perimeter now extends beyond an organization's network to include user and device identity. Organizations can utilize these identity signals as part of their access control decisions..."

### Check Azure AD Premium license

Check first if your organization has either Azure AD Premium Plan 1 og 2. Log into [Microsoft Azure](https://portal.azure.com). In the portal, navigate to *Azure Active Directory > Overview*. In my example below, Azure AD Premium P2 is shown.

Bilde 01.

### Check per-user MFA status

Log into [Microsoft 365 admin center](https://admin.microsoft.com). Navigate to *Users > Active Users > Multi-factor authentication*. 

Bilde 02.

A new page will open, showing all the users, and their *multi-factor autentication status*. In the example below (in norwegian this one..), we have a couple of users who have MFA enabled, some enforced and some others in disabled state.

Bilde 03.

### Connect to Azure AD using PowerShell

To start our transition from per-user MFA to Conditional Access, you'll need to start Windows PowerShell as an administrator and connect to Azure AD using the following in cmdlet:
````powershell
Connect-MsolService
````
This cmdlet is part of the *MSOnline module*. If you haven't got this module installed in your system, you can install it by typing the following in your elevated PowerShell console:
````powershell
Install-Module MSOnline
```` 


### Convert per-user MFA to Conditional Access based MFA with PowerShell

When we are connected to Azure AD with PowerShell, Microsoft has luckily provided the community with a great script, that we could use for helping us with the convertion.

````powershell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
````

Run the script to disable MFA for all users. You'll find both the script and some great documentation by visiting [this site](https://docs.microsoft.com/en-us/azure/active-directory/authentication/howto-mfa-userstates#convert-users-from-per-user-mfa-to-conditional-access-based-mfa) on Microsoft Docs.

### Create MFA Conditional Access policy

In the previous step, you'll disabled MFA for all users when running the PowerShell script. If some user logs in with their credentials at this time, they will not be asked for MFA. It's time to create our new *Conditional Access MFA Policy*.

Log in to the [Azure portal](https://portal.azure.com) . Open the menu and browse to *Azure Active Directory > Security > Conditional Access*. Click on *New policy*.

Bilde 04.

Name your policy. In my example, I went for *MFA All Users*. Select *All Users* and *All Cloud Apps*.
Under *Access control > Grant*, select *Grant access*, and enable *Require multi-factor autentication*.
Enable the policy and click *Save*.

Bilde 05.

In a few minutes our new Conditional policy will take affect.
At this time, you'll successfully moved from per-user MFA to Conditional Access based MFA.
The last step is to verify the changes and confirm that it's in working order.

### Verify the transition

All users show the MFA status *disabled* on the Microsoft 365 Multi-Factor Autentication page.

Bilde 06.

When logging into, for example [portal.office.com](https://portal.office.com), with an account that already had MFA configured, it will work again without need of completing the MFA setup again.

Bilde 07.

Any user that did not have MFA enabled, or any new users created in the future will be asked to go through the MFA setup before they will be able to log in again, for example into [portal.office.com](https://portal.office.com) as mentioned earlier.

Bilde 08.












