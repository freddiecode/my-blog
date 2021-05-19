---
title:  "Scoping Azure AD Application permissions to specific Exchange Online mailboxes"
date:   2021-05-19 21:00:00
categories: 
- Blog
tags:
  - PowerShell
  - Microsoft Graph API
  - Microsoft Azure
  - Exchange Online
  
---
### Scoping Azure AD Application permissions to specific Exchange Online mailboxes

When working with application permissions via Microsoft Graph, some IT administrators may want to limit the app access to a specific set of mailboxes. For example, the *Mail.Read* application permission allows apps to read mail in all mailboxes without a signed-in user. This can be unfortunate in some contexts. Using this approach will allow granular access control to your organization's user data. 

>Please note - at the time of this writing, only specific permissions against the *Outlook REST APIs* are supported. See the list of supported permissions copied from [Microsoft Docs](https://docs.microsoft.com/en-us/graph/auth-limit-mailbox-access) below:


-   *Mail.Read*
-   *Mail.ReadBasic*
-   *Mail.ReadBasic.All*
-   *Mail.ReadWrite*
-   *Mail.Send*
-   *MailboxSettings.Read*
-   *MailboxSettings.ReadWrite*
-   *Calendars.Read*
-   *Calendars.ReadWrite*
-   *Contacts.Read*
-   *Contacts.ReadWrite*


## Gather prerequisites

1. Connect to Azure AD using PowerShell:
```powershell
C:\WINDOWS\system32> Connect-AzureAD
```

2. Get details of your Azure AD application. Make a note of the returned *AppId* for future use.

````powershell
C:\WINDOWS\system32> Get-AzureADApplication -Filter "DisplayName eq 'MySampleApplication'"

ObjectId                             AppId                                DisplayName
--------                             -----                                -----------
abb699b3-537f-4516-81bf-2fc4edb22990 5050e2d9-1797-4207-a012-27bf174ab2a7 MySampleApplication

````

1. Unfortunately, it is not possible to create a new mail-enabled security group using PowerShell, so we will need to create it via the EAC (Exchange Admin Center):

	1.  In the  [EAC](https://admin.exchange.microsoft.com/), navigate to  **Recipients**  >  **Groups**  >  **Mail-enabled security**.
    
	2.  Click  **Add a group**  and follow the instructions in the details pane.
    
	    -   Under  **Choose a group type**  section, select  **Mail-enabled security**  and click  **Next**.
        
	    -   Under  **Set up the basics**  section, enter the details and click  **Next**.
        
	3.  In  **Assign owners**  section, click  **+ Assign owners**, select the group owner from the list, and click  **Next**.
    
	4.  Under  **Add members**, click  **+ Add members**, select the group members from the list, and click  **Next**.
    
	5.  In  **Edit settings**  section, enter the group email address (this you will use when creating a new application policy) and then click  **Next**:
        
	6.  In  **Review and finish adding group**  section, verify all the details, click  **Create group**, and then click  **Close**.


## Configure a new Application Access Policy ( 2 examples )

### Example 1. Create a new *Deny Access* Application Policy

Creating this app policy will **deny access** to **all** Microsoft Graph APIs for Outlook resources mentioned above to **all** members of the selected security group.

1. Connect to Exchange Online using PowerShell:
````powershell
Connect-ExchangeOnline

````

2. Create a new *deny* access policy:
````powershell
New-ApplicationAccessPolicy -AccessRight DenyAccess -AppId "<Your Azure AD Application Id>" -PolicyScopeGroupID "<Display Name of your mail-enabled security group>" -Description "<An appropriate description>"

````

Real world example:
````powershell
New-ApplicationAccessPolicy -AccessRight RestrictAccess -AppId "720062ad-3b7f-4e0a-85b3-2b2c1fce5a4c" -PolicyScopeGroupId "MyRestrictedSecGroup" -Description "Restrict this app to members of the security group MyRestrictedUsersGroup"

````

To view details of your newly created app policy (or all others) , you could use the following cmdlet:
````powershell
# Return a list of application access policies
Get-ApplicationAccessPolicy

````


````powershell
PS C:\WINDOWS\system32> Get-ApplicationAccessPolicy


RunspaceId       : 21903e33-af08-41c2-b7bc-3b45bf99fb96
ScopeName        : MyRestrictedSecGroup
ScopeIdentity    : MyRestrictedSecGroup20210509121159
Identity         : 0218a6c0-f384-4e87-8313-2f1b0da4ce33\5050e2d9-1797-4207-a012-27bf174ab2a7:S-1-5-21-3169798578-2053692951-2481737396-4642945;3c976d04-866b-40ce-8825-5fb08d71d9bd
AppId            : 5050e2d9-1797-4207-a012-27bf174ab2a7
ScopeIdentityRaw : S-1-5-21-3169798578-2053692951-2481737396-4642945;3c976d04-866b-40ce-8825-5fb08d71d9bd
Description      : Restrict this app to members of the security group MyRestrictedUsersGroup
AccessRight      : RestrictAccess
ShardType        : All
IsValid          : True
ObjectState      : Unchanged

````

### Example 2. Create a new *Restrict Access* Application Policy

Creating this app policy will **restrict access** to **all** Microsoft Graph APIs for Outlook resources mentioned above to **only** the members of the selected security group.

1. Connect to Exchange Online if not already connected:
````powershell
Connect-ExchangeOnline

````

2. Create a new *restrict* access policy:
````powershell
New-ApplicationAccessPolicy -AccessRight RestrictAccess -AppId "<Your Azure AD Application Id>" -PolicyScopeGroupID "<Display Name of your mail-enabled security group>" -Description "<An appropriate description>"

````

### (Optional) Testing and verification

The following is targeted against a policy of scope *RestrictAccess*, but the workflow will also be similar agaist testing a policy of scope *DenyAccess*.

1. Get a list of all members of out selected security group:
````powershell
# List members of our 'MyRestrictedSecGroup'

PS C:\WINDOWS\system32> (Get-AzureADGroup | Where-Object {$_.DisplayName -eq 'MyRestrictedSecGroup'}).ObjectId | Get-AzureADGroupMember

ObjectId                             DisplayName UserPrincipalName                    UserType
--------                             ----------- -----------------                    --------
73b8a426-22b1-49d1-a181-ae931495f6f4 Adele Vance AdeleV@cloudpilotdev.onmicrosoft.com Member
2a7887cd-c2aa-4b81-9a6b-e8fa42f1cddf Alex Wilber AlexW@cloudpilotdev.onmicrosoft.com  Member

````
Now you have some user accounts to test out new newly created app policy.

2. When sending a GET request to Microsoft Graph API Endpoint to list calendars to a users who is member of the selected security group, you'll get a normal and expected response in return:
````
GET https://graph.microsoft.com/v1.0/users/AdeleV@cloudpilotdev.onmicrosoft.com/calendar

````

````json
{
"@odata.context": "https://graph.microsoft.com/v1.0/$metadata#users('AdeleV%40cloudpilotdev.onmicrosoft.com')/calendar/$entity",
"id": "AQMkADg0MThmNGVkLTdmMjQtNGY5OC1iZGUwLWQzZjk5ADFhNTAxYmEARgAAA4gfPgDAnWtGvewW3CeyKRcHAOxt54xXh55Lgicv6QBaDbIAAAIBBgAAAOxt54xXh55Lgicv6QBaDbIAAAJyUgAAAA==",
"name": "Calendar",
"color": "auto",
"hexColor": "",
"isDefaultCalendar": true,
"changeKey": "7G3njFeHnkuCJy/pAFoNsgAAAAAGQQ==",
"canShare": true,
"canViewPrivateItems": true,
"canEdit": true,
"allowedOnlineMeetingProviders": [
"teamsForBusiness"
],
"defaultOnlineMeetingProvider": "teamsForBusiness",
"isTallyingResponses": true,
"isRemovable": false,
"owner": {
"name": "Adele Vance",
"address": "AdeleV@cloudpilotdev.onmicrosoft.com"
}
}

````

3. But when trying to do the same thing against a user who is **not** a part of the selected security group, you'll recieve an *access denied* response back. It works as expected.
````
GET https://graph.microsoft.com/v1.0/users/HenriettaM@cloudpilotdev.onmicrosoft.com/calendar

````


````json
{
"error": {
"code": "ErrorAccessDenied",
"message": "Access to OData is disabled."
}
}

````

**ProTip!**

If you prefer, it is also possible to test access via PowerShell :blush:
You could use the *Test-ApplicationAccessPolicy* cmdlet to test access rights of an application to a specific user/mailbox.

Testing a user account who **is** a part of the selected security group:
````powershell
PS C:\WINDOWS\system32> Test-ApplicationAccessPolicy -AppId 5050e2d9-1797-4207-a012-27bf174ab2a7 -Identity AdeleV@cloudpilotdev.onmicrosoft.com


RunspaceId        : ec48c12d-4d40-4ae3-ad0d-9d8d347a0c2a
AppId             : 5050e2d9-1797-4207-a012-27bf174ab2a7
Mailbox           : AdeleV
MailboxId         : 73b8a426-22b1-49d1-a181-ae931495f6f4
MailboxSid        : S-1-5-21-3169798578-2053692951-2481737396-4020940
AccessCheckResult : Granted

````
As you can see, *AccessCheckResult* returns *Granted*.

Testing a user account who is **not** a part of the selected security group:
````powershell
PS C:\WINDOWS\system32> Test-ApplicationAccessPolicy -AppId 5050e2d9-1797-4207-a012-27bf174ab2a7 -Identity LeeG@cloudpilotdev.onmicrosoft.com


RunspaceId        : ec48c12d-4d40-4ae3-ad0d-9d8d347a0c2a
AppId             : 5050e2d9-1797-4207-a012-27bf174ab2a7
Mailbox           : LeeG
MailboxId         : b9806fa0-42c9-4175-a052-8cd70bceb1f2
MailboxSid        : S-1-5-21-3169798578-2053692951-2481737396-4020925
AccessCheckResult : Denied

````

In this example, *AccessCheckResult* returns *Denied*. It works :smile:


**Additional resources found at Microsoft Docs:**

 - [New-ApplicationAccessPolicy](https://docs.microsoft.com/en-us/powershell/module/exchange/new-applicationaccesspolicy?view=exchange-ps)
   
 - [Get-ApplicationAccessPolicy](https://docs.microsoft.com/en-us/powershell/module/exchange/get-applicationaccesspolicy?view=exchange-ps)
   
  - [Test-ApplicationPolicy](https://docs.microsoft.com/en-us/powershell/module/exchange/test-applicationaccesspolicy?view=exchange-ps)



  
