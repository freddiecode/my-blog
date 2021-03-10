---
title:  "List all your syncronized libraries in OneDrive for Business using PowerShell"
date:   2021-03-10 20:05:00
categories: 
- Blog
tags:
  - PowerShell
  - Microsoft 365
  - OneDrive for Business
---
### List all your syncronized libraries in OneDrive for Business using PowerShell

Sometimes it can be useful to be able to quickly get an overview of which libraries you have set up synchronization via OneDrive for Business for.

To my knowledge, Microsoft 365 does not yet have the ability to store this information somewere in the cloud, which would have made for example a replacement of your computer very easy. 

I found a UserVoice on the subject [here](https://onedrive.uservoice.com/forums/913522-onedrive-on-windows/suggestions/35131279-export-import-sharepoint-document-libraries-list-f), so feel free to leave a vote if you find a solution like this helpful for you and your organization, so maybe it will come in the future.

Yes, there are the [AutoMountTeamSites](https://docs.microsoft.com/en-us/onedrive/use-group-policy#configure-team-site-libraries-to-sync-automatically), which allows you as a IT-administrator to configure specific SharePoint Team Site Libraries your end-users to sync automatically the next time they log-in.

Back to my function. Information about synced libraries in OneDrive for Business is stored in the registry, under the following path:
````
HKEY_CURRENT_USER\SOFTWARE\SyncEngines\Providers\OneDrive
````
Here you'll see (based on number of syncronized libraries), some keys/folders with some wierd looking names. All of these contains information about each of your current syncronized library. The function will simply get all registry entries contained in the OneDrive registry subkey, and store each of them in a ````PSCustomObject````.

To use the function,  import or dot-source the script into your PowerShell session, and then type ````Get-ODfBSyncLibrary```` .
*Result:*
````powershell
PS C:\WINDOWS\system32> Get-ODfBSyncLibrary
Url
---
https://contoso-my.sharepoint.com/personal/john_doe_contoso_com/Documents/
https://fabrikam.sharepoint.com/sites/Projects/Shared documents/
````


>**Hint:** If you apply the ````-Detailed```` switch, the function will return more detailed information about each entry.

````powershell
PS C:\WINDOWS\system32> Get-ODfBSyncLibrary -Detailed | Format-List

Url              : https://contoso-my.sharepoint.com/personal/john_doe_contoso_com/Documents/
MountPoint       : C:\Users\John Doe\OneDrive - Contoso Ltd
LibraryType      : Mysite
LastModifiedTime : 10.03.2021 17:45:45

Url              : https://fabrikam.sharepoint.com/sites/Projects/Shared documents/
MountPoint       : C:\Users\John Doe\OneDrive - Fabrikam Inc
LibraryType      : Teamsite
LastModifiedTime : 10.03.2021 17:45:48
````

As you can see in the example above, the function will return the Url, MountPoint, LibraryType, and the LastModifiedTime from each of your syncronized library. 

From this point on, the possibilities (as with PowerShell in general:blush: ) of further use is endless. You can easily export this information into a text file, send it out via an email or maybe use PSexec to run this function on multiple remote computers to collect this information from different users throughout your organization?
 
 The function is found on my GitHub repo:  
 https://github.com/freddiecode/Get-ODfBSyncLibrary



*My function does not return any information about the sync status of the different libraries, so if this is functionality you are looking for, you could check [this](https://techcommunity.microsoft.com/t5/onedrive-for-business/is-there-any-way-to-get-the-sync-status-using-powershell-script/m-p/699065) article out on the Microsoft Tech Community.*

Freddie







