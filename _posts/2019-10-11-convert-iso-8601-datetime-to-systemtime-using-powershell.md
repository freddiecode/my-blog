---
title:  "Convert ISO 8601 datetime to systemtime using PowerShell (and .NET)"
date:   2019-10-11 21:25:00
categories: 
- Blog
tags:
  - PowerShell
  - ISO 8601
  - .NET
---

### Quick Tip: Convert ISO 8601 datetime to systemtime using PowerShell (and .NET)

This very first blogpost I will dedicate to demonstate how you easily can convert from ISO 8601 datetime format to current systemtime using PowerShell. Let's get going!

After spending almost 3 hours seaching the Internet and reading multiple articles and blogpost on the similar subject, I finally found a method that meet my requirements (..and it was surprising simple!).

**First, a litte facts about the ISO 8601 format:**

>*ISO 8601 Data elements and interchange formats – Information interchange – Representation of dates and times is an international standard covering the exchange of date- and time-related data. It was issued by the International Organization for Standardization (ISO) and was first published in 1988. The purpose of this standard is to provide an unambiguous and well-defined method of representing dates and times, so as to avoid misinterpretation of numeric representations of dates and times, particularly when data are transferred between countries with different conventions for writing numeric dates and times.*

You can read more about the ISO 8601 format on ![Wikipedia](https://en.wikipedia.org/wiki/ISO_8601)

**Now, down to the fun stuff:**

In the follwing example below, I make a ````$date```` variable containing my ISO 8604-timestamp.  
I then call the ```[datetime]::Parse()``` method  containing my variable (*$date*)


````powershell
$date = '2019-10-09T14:06:14.9374615Z'

[datetime]::Parse($date)
````

The following output is written to the console:

```powershell
onsdag 9. oktober 2019 16:06:14
```
Note the example output is written in Norwegian.
The cool stuff is that this metod will convert the ISO 8601 to your current system settings.


**You can also create a hashtable and use a ```foreach```-loop:**

```powershell
$time = @('2019-11-08T14:06:14.9374615Z','2019-10-09T14:06:14.9374615Z','1988-08-08T22:02:00.9374615Z')

foreach ($time in $time.GetEnumerator()) {

[datetime]::Parse($time)

}
````
The following output is then written to the console:
````powershell
fredag 8. november 2019 15:06:14
onsdag 9. oktober 2019 16:06:14
tirsdag 9. august 1988 00:02:00
````
Yet again in Norwegian :blush:


**Hope this little tip will help someone spending much less time troubleshooting this subject then I did.**


Freddie



