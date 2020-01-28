---
title:  "Sending Push Notifications to mobile devices from App Center using PowerShell"
date:   2020-01-28 18:48:00
categories: 
- Blog
tags:
  - PowerShell
  - Microsoft App Center
  - Xamarin.iOS
  - Send-PushNotification
---

### Sending Push Notifications to mobile devices from Microsoft App Center using PowerShell

So I made an app...

Last weekend I has able to spend a whole lot of hours deepdiving into the world of App Development.
With the registration as an Apple Developer done, I dived right in. The goal was (at the end of the weekend), to create an app that successfully included the ability to receive Push Notifications from [**Microsoft App Center**](https://appcenter.ms).

Please read the official Microsoft Docs found [here](https://docs.microsoft.com/en-us/appcenter/push/rest-api) for more detailed information about using their REST API.

>:information_source: **Hey you!** As described in the documentation, there are two different URLs used against App Center. One to use if the app belongs to a username, and one if the app belongs to an organization. 
This function is targeted against the **username** version.


````Send-PushNotification````

````powershell
<#
.Synopsis
   Use this custom function to send Push Notifications with the help of the Microsoft Visual Studio App Center REST API.
.EXAMPLE
   Send-PushNotification -Title "This is the title> -Body "Enter some text here"
   #>
function Send-PushNotification
{
    [CmdletBinding()]
    [Alias()]
    [OutputType([int])]
    Param
    (
        [Parameter(Mandatory = $True,
        ValueFromPipeline = $False,
        Position=0,
        HelpMessage = "Enter desired title")]
        [string[]]$Title,

        [Parameter(Mandatory = $True,
        ValueFromPipeline = $False,
        Position=1,
        HelpMessage = "Enter body text")]
        [string[]]$Body


    )

    Begin
    {}

    Process
    {

       try {

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", "application/json")
        $headers.Add("X-API-Token", "ENTER-YOUR-API-TOKEN-HERE")

$json = @"
            {
            "notification_content": {
            "name": "PushFromPowerShell",
            "title": "$Title",
            "body": "$Body",
            "custom_data": {"sound": "default"}
            },
            "notification_target": null
            }
"@

$response = Invoke-RestMethod 'https://appcenter.ms/api/v0.1/apps/<Username>/<AppName>/push/notifications' -Method 'POST' -Headers $headers -Body ([System.Text.Encoding]::UTF8.GetBytes($json))


            }


       catch {

        Write-Host $_ -ForegroundColor Red }

    }

    End
    {}

}
````


*Please note that this function is made for sending Push Notifications to [all](https://docs.microsoft.com/en-us/appcenter/push/rest-api#send-a-notification-to-all-registered-devices-broadcast) registered devices (broadcast).
If you require a different functionality, you'll have to edit the function to match your needs.*


### Installing the module:

Copy the entire text in the code block above (or grab a copy in my GitHub repo). Open PowerShell ISE, Visual Studio Code or your editor of choice. Paste in the code and save it as a .psm1-file named  *Send-PushNotification*.

Create a new **folder** named *Send-PushNotification* (matching your .psm1-file) and then, move your *Send-PushNotifcation* .psm1-file into the  **folder**  with the same name.

Then, move the folder containing your .psm1-file to your desired  `PSModulePath`.

See my selected path below:

![PATH](/assets/images/PUSH/PATH.PNG)

### Example:

The syntax is very simple. Is requires only two parameters, a ````Title```` and a ````Body````.

![Syntax](/assets/images/PUSH/Syntax.PNG)

````powershell
Send-PushNotification -Title "Hello World" -Body "Sending Push Notification using PowerShell"
````
I have choosen to *hardcode* the ````name```` of the push in the ````HTTP body````. Please note that this is *NOT* a text displayed in the actual Push Notification. It's only a name to keep track of different Push Notifications sendt via the App Center. You could of course change this if you like to. See the screenshot below for a example of this behavior:

![APPCENTER](/assets/images/PUSH/APPCENTER.PNG)


A beautiful, *Powered by PowerShell* Push Notification will appear on the devices with your app installed after a few seconds :blush:

![iPhone](/assets/images/PUSH/iOS.PNG)



Hope someone found this little function useful.

Freddie



 









   















