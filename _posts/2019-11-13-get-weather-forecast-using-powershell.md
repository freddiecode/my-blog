---
title:  "Get weather forecast using PowerShell"
date:   2019-11-13 21:00:00
categories: 
- Blog
tags:
  - PowerShell
  - Invoke-WebRequest
  - PowerShell function
  - Weather forecast
---

### Get weather forecast using PowerShell

Today I came across the cool [wttr.in](<https://github.com/chubin/wttr.in>) project on GitHub.

>wttr.in - the right way to check the weather!
wttr.in is a console-oriented weather forecast service that supports various information representation methods like terminal-oriented ANSI-sequences for console HTTP clients (curl, httpie, or wget), HTML for web browsers, or PNG for graphical viewers. wttr.in uses wego for visualization and various data sources for weather forecast information.


**Based on the information available on the GitHub-page, I decided to put together a small PowerShell function for quick and easy use:**



````powershell
function Get-WeatherForecast {

[CmdletBinding()]

param(
  
    [Parameter(Mandatory = $True, 
    ValueFromPipeline = $False, 
    HelpMessage = "Enter location")]
    [string[]]$Location

)

try {

$Headers = @{"Accept-Language" = "nb"}

$Uri = "http://wttr.in/~$Location"


(Invoke-WebRequest -Uri $Uri -Headers $Headers).ParsedHtml.body.outerText

}

catch {

Write-Host "Could not find location. Please try another location" -ForegroundColor Yellow

}


}
````
Not the most advanced function out there, but hey, it does the trick.

To use it, you could do it so easy as copying the text above, and saving it as a .PS1-file by using PowerShell ISE or Visual Studio Code (or another editor of your choice).
Then, navigate to the saved location of your PS1-file and *dotsource* it using this technique:

<img src= "/assets/images/WeatherForecast/DOT.PNG" width="1335" height="91"/>


Now you have the function avaiable in your console, and ready to use.

<img src= "/assets/images/WeatherForecast/READY.PNG" width="1362" height="93"/>

Select your desired location using the ````Location```` parameter and get ready to be exited!

Below you can see the final result. The weather forecast of the city Hamar the next couple of days :blush:

*Note the example output is written in Norwegian.*

<img src= "/assets/images/WeatherForecast/FINAL.PNG" width="1356" height="684"/>


You could alternatively change the desired language output if you like. This could be done by changing the country code  in the ````Accept-Language```` value of the ````$Headers```` variable. See all available country codes [here](http://wttr.in/:translation).




























