---
title:  "Microsoft Graph PowerShell SDK"
classes: wide
date:   2022-01-11 15:30:00
categories: 
- Blog
tags:
  - PowerShell
  - Microsoft Graph API
  - Microsoft Graph PowerShell SDK
  - Microsoft Graph Explorer
  - Microsoft 365
---
## Microsoft Graph PowerShell SDK

I have spend a lot of time interacting with the Microsoft Graph API over the years.
Normally I have written a bunch of code with the help of `Invoke-RestMethod` to get the job done.
Sometimes I've also used `Microsoft Graph Explorer` when I need to test something out, or when I need to do a simple `GET request` to get my hands of some data.

In this blog post, I thought I could dive a little deeper into the available SDK for PowerShell against Microsoft Graph.

Let's start with a little introduction.

### What is Microsoft Graph?
I go for the short answer: 
*A RESTful, single API endpoint (https://graph.microsoft.com) for interacting with your data in your Microsoft 365 subscription.*

![Overview of Microsoft Graph](https://docs.microsoft.com/en-us/graph/images/microsoft-graph.png)

### Microsoft Graph documentation

- Found here: aka.ms/graphapiref
 - API docs will show the correct endpoint, HTTP request, required permissions (*least to most privileged*), request headers, request body, response messages and a lot of examples for you to play around with.
- You can copy/paste examples found in docs and re-use them in [Microsoft Graph Explorer](https://aka.ms/ge) with a sample tenant. 

### What is Microsoft Graph Explorer?
>"...[Graph Explorer](https://developer.microsoft.com/graph/graph-explorer/) is a developer tool that lets you conveniently make Microsoft Graph REST API requests and view corresponding responses. Use Graph Explorer to try APIs on the default sample tenant to explore capabilities, or sign in to your own tenant and use it as a prototyping tool to fulfill your app scenarios...."

Use Graph Explorer to:

-   Make Microsoft Graph API requests (GET, POST, PUT, PATCH, and DELETE) and see responses including response code and any headers and bodies.
-   Consent to permissions. Graph Explorer supports only  [delegated permissions](https://docs.microsoft.com/en-us/graph/auth/auth-concepts#delegated-and-application-permissions).
-   Add a request body and request header to your query.
-   View and copy the access token.
-   View sample queries for different services in Microsoft Graph.
-   View, download, or delete the queries you ran in the last 30 days.
-   View and copy code snippets of each query you run in C#, Java, JavaScript, and Objective C.
-   Access Microsoft Graph Toolkit components and adaptive cards for some sample queries.
-   Share queries, including the request body and request headers.

#### Get Started
- Graph Explorer is a web application found [here](https://aka.ms/ge).
- If you are new to this great tool provided by Microsoft, I recommend starting out by reading [this](https://docs.microsoft.com/en-us/graph/graph-explorer/graph-explorer-overview#get-started) article found on Microsoft Docs.

### Microsoft Graph PowerShell SDK
* Provides a well known PowerShell-style way of making calls to the Microsoft Graph API
* Cmdlets have a prefix of *MG* , a safety mechanism for preventing the cmdlets to crash against other PowerShell cmdlets in different modules (example: *Connect-MgGraph*, *Get-MgUser*, *Add-MgTeamMember*)
* Cmdlets follows the PowerShell convention of *Verb-Noun* format


### Installing the Microsoft Graph PowerShell SDK
>***Note***
>Installing the main module of the SDK will install all 38 sub modules. You might consider only installing the necessary modules, including `Microsoft.Graph.Authentication`.

The Microsoft Graph PowerShell SDK is published on the [PowerShell Gallery](https://www.powershellgallery.com/packages/Microsoft.Graph). You can install the SDK in PowerShell Core or Windows PowerShell using the following command:
````powershell
Install-Module Microsoft.Graph -Scope CurrentUser
````
Optionally, you can change the scope of the installation using the `-Scope` parameter. This requires admin permissions.
````powershell
Install-Module Microsoft.Graph -Scope AllUsers
````
>***Important***
>Installing the SDK in one version of PowerShell does not install it for the other. Be sure to run the installation command inside the version of PowerShell (Core or Windows) you intend to use it in.

#### Verify installation
After the installation completes, you can verify the installed version with the following command:
````powershell
Get-InstalledModule Microsoft.Graph
````

#### Updating the SDK
You can update the SDK and all of its dependencies using the following command:
````powershell
Update-Module Microsoft.Graph
````
>***Current version***
>At the time of this writing, the current version of the Microsoft Graph PowerShell SDK is **1.9.1**.  The version history can be found [here](https://www.powershellgallery.com/packages/Microsoft.Graph/1.9.1#).

#### Uninstalling the SDK
First, use the following command to uninstall the main module:
````powershell
Uninstall-Module Microsoft.Graph
````
Then, remove all of the dependency modules by running the following commands:
````powershell
Get-InstalledModule Microsoft.Graph.* | ForEach-Object { if($_.Name -ne "Microsoft.Graph.Authentication"){ Uninstall-Module $_.Name } }
Uninstall-Module Microsoft.Graph.Authentication
````

### Examples of use
As you can see below, at the time of this writing, there are thousands of available cmdlets available in this module.
````powershell
#Get Command count from module
$Commands = Get-Command -Module Microsoft.Graph*
$Commands.Count
6978
````

### Finding available commands
Due to the amount of available commands and the complexity of this module, there can be hard to find the right command to use. In this case, you can use the `Get-Command` to search for available commands in the SDK. For example, if you are looking for commands related to `Microsoft Teams`, you can run the following command:
````powershell
Get-Command -Module Microsoft.Graph* *team*
````

#### API version
By default, the SDK uses the [Microsoft Graph REST API v1.0](https://docs.microsoft.com/en-us/graph/api/overview?view=graph-rest-1.0&preserve-view=true). You can change this by using the `Select-MgProfile` command:

````powershell
#Change to the Beta API
Select-MgProfile -Name "beta"
````

>***Microsoft Graph Beta API***
> Please note that this API is subject for change, and should **not** be used in production.


````powershell
#Change back to the version 1.0
Select-MgProfile -Name "v1.0"
````

#### Authentication
The PowerShell SDK supports two types of authentication: delegated access, and app-only access. In this guide, you will use delegated access to login as a user, grant consent to the SDK to act on your behalf, and call the Microsoft Graph.

#### Determine required permission scopes

Each API in the Microsoft Graph is protected by one or more permission scopes. The user logging in must consent to one of the required scopes for the APIs you plan to use. In this example, we'll use the following APIs.

-   [List users](https://docs.microsoft.com/en-us/graph/api/user-list?view=graph-rest-1.0&preserve-view=true)  to find the user ID of the logged-in user
-   [List joinedTeams](https://docs.microsoft.com/en-us/graph/api/user-list-joinedteams?view=graph-rest-1.0&preserve-view=true)  to get the Teams the user is a member of.
-   [List channels](https://docs.microsoft.com/en-us/graph/api/channel-list?view=graph-rest-1.0&preserve-view=true)  to get the channels in a Team.
-   [Send message](https://docs.microsoft.com/en-us/graph/api/channel-post-messages?view=graph-rest-1.0&preserve-view=true)  to send a message to a Team channel.

The  `User.Read.All`  permission scope will enable the first two calls, and the  `Group.ReadWrite.All`  scope will enable the rest. These permissions require an admin account.

#### Sign in
Use the `Connect-MgGraph` command to sign in with the required scopes. You'll need to sign in with an admin account to consent to the required scopes.
````powershell
Connect-MgGraph -Scopes "User.Read.All","Group.ReadWrite.All"
````
The command prompts you to go to a web page to sign in using a device code. Once you've done that, the command indicates success with a `Welcome To Microsoft Graph!` message. You only need to do this once per session.
>***Note***
>You can add additional permissions by repeating the `Connect-MgGraph` command with the new permission scopes.

Example: If you are going to create a new team, head over to the Microsoft Graph documentation on [create team](https://docs.microsoft.com/en-us/graph/api/team-post?view=graph-rest-1.0&tabs=http), and look up the required permissons table:

![Create new team](/assets/images/MSGraphSDK/CreateTeamPermissions.png)

Like shown here, you need to change (or add) the permissions to include (from least to most privileged):

 - *Team.Create*
 - *Group.ReadWrite.All** (deprecated and should not be used)
 - *Directory.ReadWrite.All* (deprecated and should not be used)

````powershell
Connect-MgGraph -Scopes "User.Read.All", "Group.ReadWrite.All", "Team.Create"
````

After you have executed the cmdlet shown above, a web page are opened and requires you to accept the newly added permissions (see that `Create teams` are added to the list) :

![Accept permissions](/assets/images/MSGraphSDK/NewPermissions.png)

Accept the required permissions to complete the connection to Microsoft Graph.


#### Call Microsoft Graph
Now that you're signed in, you can start making calls to Microsoft Graph.

#### Get the signed-in user
````powershell
Get-MgUser
````

This outputs a listing of users in your Microsoft 365 subscription:
````powershell
Id                                   DisplayName                                           Mail                                        UserPrincipalName                                             UserType
--                                   -----------                                           ----                                        -----------------                                             --------
73b8a426-22b1-49d1-a181-ae931495f6f4 Adele Vance                                           adelev@cloudpilotlabs.xyz                   adelev@cloudpilotlabs.xyz
42d7a7a0-e6d0-46f1-b570-fae50ae12899 Admin                                                 admin@cloudpilotlabs.xyz                    admin@cloudpilotlabs.xyz
2a7887cd-c2aa-4b81-9a6b-e8fa42f1cddf Alex Wilber                                           AlexW@cloudpilotdev.onmicrosoft.com         AlexW@cloudpilotdev.onmicrosoft.com
3e3ef4b2-20fa-41fa-b764-3dac0bbdcc42 Berit Nordmann                                        berit@cloudpilotdev.onmicrosoft.com         berit@cloudpilotdev.onmicrosoft.com
2996d76a-a2db-426b-a1ec-93c6e6c92725 Bodil Hansen                                          bodil.hansen@cloudpilotlabs.xyz             bodil.hansen@cloudpilotlabs.xyz
````

### Filtering 
The PowerShell SDK supports [OData](https://docs.microsoft.com/en-us/graph/query-parameters#filter-parameter) filtering:
````powershell
#Get user by filtering by DisplayName
$User = Get-MgUser -Filter "displayName eq 'Alex Wilber'"
````

Verify that worked by entering the following:
````powershell
#Return DisplayName
$User.DisplayName
````
`Alex Wilber` should be returned in the console.

### Using Query Parameter
You can use the `-Property` parameter to only return some properties from your query:
````powershell
#Get user by Id and return DisplayName and Mail 
Get-MgUser -UserId $User.Id -Property DisplayName, Mail
````
The following is then returned:
````
Id DisplayName Mail                                UserPrincipalName UserType
-- ----------- ----                                ----------------- --------
   Alex Wilber AlexW@cloudpilotlabs.xyz
````

### List the user's joined teams
Use the user's ID as a parameter to the `Get-MgUserJoinedTeam` command:
````powershell
#List all Joined Team for spesific user by Id
Get-MgUserJoinedTeam -UserId $User.Id
````

A list of joined teams is returned (see example below:
````powershell
Id                                   DisplayName   Description                                          IsArchived
--                                   -----------   -----------                                          ----------
c0b599d5-480d-43a4-9f7a-f366ae39850a cloudpilotdev                                                      False
c550e12e-9647-49a8-9e1a-37e3f1cc690e External Team A team used for allowing and testing external access False
````

### List team channels
Let's use the team's Id as a parameter to the `Get-MgTeamChannel` command:
````powershell
#1. Save the "External Team's Id into a variable for future use
$externalTeam = "c550e12e-9647-49a8-9e1a-37e3f1cc690e"

#2. Save the 'General' channel Id into a variable for future use
$general = Get-MgTeamChannel -TeamId $externalTeam -Filter "displayName eq 'General'"
````

### Send a message to teams channel
Now let's use our `$externalTeam` variable and our `$general` variable to post a message to our `External Team`  *general* channel using the following command:

````powershell
#New Team Channel Massage using Microsoft Graph SDK
New-MgTeamChannelMessage -TeamId $externalTeam -ChannelId $general.Id -Body @{ Content="Hello World" }
````

![New teams message](/assets/images/MSGraphSDK/TeamMessage.png)

If you want to send an `urgent` message, add the `-Importance` parameter as shown below:
````powershell
#New Urgent Team Channel Message using Microsoft Graph SDK
New-MgTeamChannelMessage -TeamId $externalTeam -ChannelId $general.Id -Body @{ Content="Please bring me a coffee!" } -Importance "urgent"
````

![New urgent teams message](/assets/images/MSGraphSDK/TeamUrgentMessage.png)

### Create a new user
Example 1:
Uses the traditional format:
````powershell
#Create a new user account using Microsoft Graph SDK for PowerShell
$PasswordProfile = @{ Password = 'P@ssW0rd123'; ForceChangePasswordNextSignIn = $true }
New-MgUser -DisplayName "Ola Nordmann" -PasswordProfile $PasswordProfile -AccountEnabled -MailNickName "OlaNor" -UserPrincipalName "Ola.Nordmann@cloudpilotlabs.xyz"
````
Example 2:
Use `splatting` and  `hash tables` for easier readability:
````powershell
$Parameters = @{
		PasswordProfile = @{
			Password = 'P@ssW0rd123'
			ForceChangePasswordNextSignIn = $true
			}
		DisplayName = "Ola Nordmann"
		AccountEnabled = $true
		MailNickName = "OlaNor"
		UserPrincipalName = "Ola.Nordmann@cloudpilotlabs.xyz"
	}
		
New-MgUser @Parameters
````

### Remove a user
````powershell
#Remove a user account using Microsoft Graph SDK for PowerShell

#Assign the User's Id into a variable named $DeleteUser
$DeleteUser = Get-MgUser -Filter "displayName eq 'Ola Nordmann'"

#Confirm the Id
$DeleteUser.Id
97b1eb01-a366-4023-b737-8d82b2877a82

#Delete the user
Remove-MgUser -UserId $DeleteUser.Id
````

### Create new team
````powershell
#Create a new team using splatting
$Splat  =  @{
	DisplayName  =  "My PowerShell SDK Team"
	Description  =  "My awesome PowerShell SDK made Team!"
	AdditionalProperties = @{"template@odata.bind"="https://graph.microsoft.com/v1.0/teamsTemplates('standard')"}
}
New-MgTeam  @Splat
````

### Lookup team
````powershell
#Get new Team by searching by displayName
$Team  =  Get-MgGroup  -Filter  "displayName eq 'My PowerShell SDK Team'"
$Team

Id                                   DisplayName            Description                          GroupTypes AccessType
--                                   -----------            -----------                          ---------- ----------
01f5ff67-6174-4c9b-b142-a423ee94dbe8 My PowerShell SDK Team My awesome PowerShell SDK made Team! {Unified}  
````

### Add me as team owner
````powershell
#1. Put user account with displayName of 'Admin' into a variable
$Me = Get-Mguser -Filter "displayName eq 'Admin'"

#2. Put newly created team into a $Team variable
$Team  =  Get-MgGroup  -Filter  "displayName eq 'My PowerShell SDK Team'"

#3. Add 'Admin' as a 'Owner' of the newly created team using the 'Id' of both user and team
New-MgTeamMember -Id $Me.Id -TeamId $Team.Id -Roles "Owner" -AdditionalProperties @{ "@odata.type" = "#microsoft.graph.aadUserConversationMember"; "user@odata.bind" = "https://graph.microsoft.com/v1.0/users/" + $Me.Id }
````





### Need help with a cmdlet or required syntax?
Did you know that PowerShell has a great build-in help system? Write `help`  (*help* is an alias for `Get-Help`) in front of any cmdlet to access this system:
````powershell
help New-MgUser
````
Not a fan of reading documentation in a terminal window? No worries! :blush:
````powershell
#Navigates to Microsoft Docs in browser 
help New-MgUser -Online

#Show help in pop-up window (also provides a handy search bar)
help New-MgUser -ShowWindow
````
To keep the help documention up to date on your system, write:
````powershell
#Updates the help system
Update-Help
````

### Sign out of Microsoft Graph
It is a good practice to sign out when you are finished
````powershell
Disconnect-MgGraph
````

I hope this blog post has inspired you to having a go against Microsoft Graph, and I have managed to show how powerful this module is when you want to manage data in Microsoft 365 using PowerShell :blush:

-Freddie













