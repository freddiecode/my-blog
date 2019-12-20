---
title:  "Some of my PowerShell GUIs created in 2019"
date:   2019-11-20 20:00:00
categories: 
- Blog
tags:
  - PowerShell
  - WPF
  - Windows Presentation Foundation
  - GUIs
  - XAML
---

### PowerShell GUIs with the help of WPF

As 2019 comes to an end, I wanted to write a blog post about some of my PowerShell GUI creations this year. Earlier this year, then I discovered that you could combine your own PowerShell script's with some interactive graphical user interfaces, and take your script's to *the next level*, I wanted to sit down and gain some knowledge about PowerShell and WPF working together :blush:
This could also be very helpful if you have some colleagues who has less knowledge of PowerShell than you, but you want them to use your script's in a more secure and easy way.
 

**Below is a short description of what WPF is:** 

>*Windows Presentation Foundation (WPF) is a graphical subsystem (similar to WinForms) originally developed by Microsoft for rendering user interfaces in Windows-based applications. WPF, previously known as "Avalon", was initially released as part of . NET Framework 3.0 in 2006.*

*You can read more about it [here](https://en.wikipedia.org/wiki/Windows_Presentation_Foundation).*

WPF uses XAML - *E**X**tensible **A**pplication **M**arkup **L**anguage* to define the visual elements. If you have some experience working with XML-files, you'll quickly see the similarities.

**What is XAML?**
>*XAML is a declarative markup language. As applied to the .NET Core programming model, XAML simplifies creating a UI for a .NET Core app. You can create visible UI elements in the declarative XAML markup, and then separate the UI definition from the run-time logic by using code-behind files that are joined to the markup through partial class definitions. XAML directly represents the instantiation of objects in a specific set of backing types defined in assemblies. This is unlike most other markup languages, which are typically an interpreted language without such a direct tie to a backing type system. XAML enables a workflow where separate parties can work on the UI and the logic of an app, using potentially different tools.*


### Some of my projects in 2019:


1. **Send-SMSMessage**

A simple SMS GUI for sending text messages. Integrated with the [Twilio SMS API](https://www.twilio.com/sms)

   ![SMS](/assets/images/WPF/SMS.PNG)

2. **Copy-ADMemberShip**

As the title hopefylly explains, a simple view for copying Active Directory membership from one user to another user.
Enter *parent* username and view search results in the texbox below. Then enter username who will get the same Active Directoy groups membership. Complete with lookup in Active Directory and error handling. Please note that the text on the following screenshot has been translated into Norwegian.

   ![COPYAD](/assets/images/WPF/COPYAD.PNG)

3. **ADCSV**

   A helpful GUI for easy creation of multiple AD-users. Just import a CSV-file containing your new users (firstname, lastname and disired username is mandatory). Select an OU-path, an expiration date, a one-time password and just like    that, you'll created a whole lot of new users in seconds. Please note that the text on the following screenshot has been translated into Norwegian.

   ![ADCSV](/assets/images/WPF/ADCSV.PNG)

4. **Get-ADComputerInformation**

   Get a simple overview of your AD-computers. Lists ````ComputerName````, ````OperatingSystem````, ````OSVersion````, ````OU-path````, ````IPv4Address````, ````LastLogonDate```` and ````Enabled-state```` in a easy readable gridview.

   ![ADCOMP](/assets/images/WPF/ADCOMP.png)

5. **Get-Inventory**
 
   This was actual my first (*..and eventually become my biggest PowerShell + WPF project in 2019*). Current version of the script is over **550** lines long.
   Complete with a login window and SQL-database connection for storing and reading data. Features include registration of a ````new device````, ````search function````, and ````export into a report-capable CSV-file (Excel?)````. Please note that the text on the following screenshot has been translated into Norwegian.

* **Login window**

   ![GETINV](/assets/images/WPF/GETINV.PNG)

* **Main window**

![GETINV2](/assets/images/WPF/GETINV2.png)

 * **Register new device**

![GETINV4](/assets/images/WPF/GETINV4.png)

* **Export and save list to CSV-file**

![GETINV3](/assets/images/WPF/GETINV3.png)


So, with a little peak of my previous projects, I'll hope you have been inspired of what combining PowerShell and WPF together can do.  

If you have any questions regarding some of the code behind any of my projects, don't hesitate of dropping me an e-mail at ````freddie@cloudpilot.no````.

**Merry Christmas and happy scripting!**






   















