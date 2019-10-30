---
title:  "Use splatting, hashtables and variables to keep your scripts clean"
date:   2019-10-30 21:03:00
categories: 
- Blog
tags:
  - PowerShell
  - Hashtables
  - Splatting
  - Invoke-WebRequest
---

### Use splatting, hashtables and variables to keep your scripts clean


In the past few weeks, I have spend a lot of time working with the ````Invoke-WebRequest```` cmdlet.  
Laying down some time and effort to keep your code as clean as possible will help you see errors, typos and correct them much quicker, then working with *messy code*.

**Say Hello To My Little Friend - Splatting**

>*Splatting is a method of passing a collection of parameter values to a command as unit. PowerShell associates each value in the collection with a command parameter. Splatted parameter values are stored in named splatting variables, which look like standard variables, but begin with an At symbol (@) instead of a dollar sign ($). The At symbol tells PowerShell that you are passing a collection of values, instead of a single value.*

Taken from [PowerShell Docs](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_splatting?view=powershell-6).

**Example:**  

````powershell
Invoke-WebRequest -Uri "https://app.melcloud.com/Mitsubishi.Wifi.Client/Login/ClientLogin" -Method "POST" -Headers @{"Accept"="application/json, text/javascript, */*; q=0.01"; "Referer"="https://app.melcloud.com/"; "Origin"="https://app.melcloud.com"; "X-Requested-With"="XMLHttpRequest"; "User-Agent"="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.120 Safari/537.36"; "Sec-Fetch-Mode"="cors"} -ContentType "application/json; charset=UTF-8" -Body "{`"Email`":`"mail@domain.com`",`"Password`":`"MySecretPassword`",`"Language`":13,`"AppVersion`":`"1.18.5.1`",`"Persist`":true,`"CaptchaResponse`":null}"
````

Please don`t mind the code, just have a look at the formatting. The code is taken from a project I am currently working on,  so it's only meant as an example in this post.
More about that project later :nerd_face:

*-Messy right?*

When you encounter some syntax error (and I promise you will :wink:), you can just imagine how much time you need to spend just to trying to read through and correcting your code. When writing so much text on a single line, it will take up the entire width of your screen, or wrap to more lines in your console. Where is the fun in that?

**Another example:**  

`````powershell
$Params = @{

    Uri = "https://app.melcloud.com/Mitsubishi.Wifi.Client/Login/ClientLogin"

    Method = "POST"

    Headers = @{

           
         "Accept" = "application/json, text/javascript, */*; q=0.01"
         "Referer" = "https://app.melcloud.com/"
         "Origin" = "https://app.melcloud.com"
         "X-Requested-With" = "XMLHttpRequest"
         "User-Agent" = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/77.0.3865.120  Safari/537.36"
         "Sec-Fetch-Mode" = "cors"


        }
   

    ContentType = "application/json; charset=UTF-8"


    Body = @{
    
       Email = 'mail@domain.com'
       Password = 'MySecretPassword'
       Language = '13'
       AppVersion = '1.18.5.1'
       Persist = 'True'
       CaptchaResponse = ''


      } | ConvertTo-Json
       
  
    }
   

Invoke-WebRequest @Params
``````  
*Wow, isn't that some clean, sexy PowerShell?*  

You see the difference right? So much easier to read, much quicker to identify a *typo* and you can find and even change some text input so much faster. No more time needed to scroll the entire width of your monitor trying to find that silly value that didn`t work out as you thought.



Until next time,

Freddie



