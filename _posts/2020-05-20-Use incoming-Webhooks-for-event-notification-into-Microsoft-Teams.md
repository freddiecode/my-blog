---
title:  "Use incoming Webhooks for event notification into Microsoft Teams"
date:   2020-05-20 10:00:00
categories: 
- Blog
tags:
  - PowerShell
  - Microsoft Teams
---

### Use incoming Webhooks for event notification into Microsoft Teams

A cool feature in Microsoft Teams is the opportunity to use incoming Webhooks.

>:information_source: Incoming webhooks are special type of Connector in Teams that provide a simple way for an external app to share content in team channels and are often used as tracking and notification tools. Teams provides a unique URL to which you send a JSON payload with the message that you want to POST, typically in a card format. Cards are user-interface (UI) containers that contain content and actions related to a single topic and are a way to present message data in a consistent way.

To add an incoming webhook to your Teams channel - please follow the instructions posted on Microsoft Docs found [here](https://docs.microsoft.com/en-us/microsoftteams/platform/webhooks-and-connectors/how-to/add-incoming-webhook#add-an-incoming-webhook-to-a-teams-channel). 

These webhooks could contain information about pretty much anything you like - for instance, a importent event on one of your servers.

**Let me show you a litte example:**

````powershell
if ((Get-Service -Name BITS).Status -eq 'Stopped') {

$uri = "https://outlook.office.com/webhook/<the-rest-of-your-uri>"
$body = ConvertTo-Json @{
	title = "$((Get-Date).ToString("dd.MM-yy HH:mm:ss")) | Service alert on: $(hostname)"
	text  = "**BITS service has stopped**!"
}

Invoke-RestMethod -uri $uri -Method Post -body $body -ContentType application/json | Out-Null

}
````

**Resulting in the following message in one of my Teams channel**

![PATH](/assets/images/WEBHOOKS/IMAGE01.PNG)

This little script could be a part of a series of monitoring script's that you run on your server on a regular basis - maybe with the help of task scheduler?

Hope this little blog post will inspire you to see the power and the endless possibilities with Webhooks :blush:
