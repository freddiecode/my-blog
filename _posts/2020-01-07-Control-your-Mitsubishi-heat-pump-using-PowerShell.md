---
title:  "Control your Mitsubishi  heat pump using PowerShell"
date:   2020-01-07 21:00:00
categories: 
- Blog
tags:
  - PowerShell
  - MELCloud
  - Mitsubishi Electric
---

### Control your Mitsubishi heat pump using PowerShell

This very first blog post in 2020, I will dedicate to presenting my very first publicly available PowerShell module! :blush:

I present to you (*..imagine a drum roll*): **Control-MELCloudDevice**.

When we decided to purchase a heat pump last fall, my only criteria was that it had the opportunity of being controlled via Wi-Fi. We decided on a **Mitsubishi Kaiteki 6600** with an official **Mitsubishi Electric Wi-Fi Interface**, allowing the heat pump to be connected to Mitsubishi Electric's own management platform called *MELCloud*.

**Here's what Mitsubishi describes MELCloud as:** 

>*MELCloud is the new generation of Cloud based control for Mitsubishi Electric Air Conditioning, Heating and Heat Recovery Ventilation products. MELCloud provides users with effortless control of their devices whether they are out or are just lying on their sofa at home.  Accessing and controlling your Mitsubishi Electric products has never been simpler and is now possible via a wide range of PC, Tablets and Smartphones.*

*You can read more about MELCoud on their official found [here](https://www.melcloud.com/).*
*Their official apps for Android and Apple can be downloaded [here for Android](https://play.google.com/store/apps/details?id=mitsubishi.wifi.android.mitsubishiwifiapp) and [here for Apple](https://apps.apple.com/gb/app/melcloud/id667989366)*.

For the average Joe, their apps will be more than sufficient enough. It allows you to turn on/off the heat pump, set desired temperature, mode selection (heating or cooling), fan speed and more. Great for controlling your heat pump when you are away from home, for example when you are at work, or on vacation.

This got the thinking, is it possible to do the same thing using PowerShell? Who needs their smartphones anyway? :laughing: I spun up a virtual machine, installed [Fiddler](https://www.telerik.com/fiddler), folded up my sleeves and set to work.

**After countless hours, I finally feel that I have something useful to present to my readers.**

*If you haven't already connected your device to your Wi-Fi network and signed up to use the MELCloud service, now would be a good time to do this. I will not cover how you do this in this post, so I recommend reading the instructions that came with your device.*

### Get the module:
You'll find the complete module by visiting my GitHub repo [here](<INSERT_GITHUB_REPO_HERE!>)  


### Import the module:

Open a new PowerShell session, navigate to the folder containing the manifest and then import (dotsource) the module:
````powershell
Import-Module .\Control-MELCloudDevice.psd1 -Force
````

You can then use the ````Get-Command```` cmdlet to verify the available commands in this module:
````powershell
Get-Command -Module Control-MELCloudDevice
````

````powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Function        Get-MELCloudContextKey                             1.0        Control-MELCloudDevice
Function        Get-MELCloudDevice                                 1.0        Control-MELCloudDevice
Function        Get-MELCloudDeviceInfo                             1.0        Control-MELCloudDevice
Function        Set-MELCloudDevice                                 1.0        Control-MELCloudDevice
Function        Start-MELCloudDevice                               1.0        Control-MELCloudDevice
Function        Stop-MELCloudDevice                                1.0        Control-MELCloudDevice
````

You are ready to go!


### A short description of each function:


1. **Get-MELCloudContextKey**

This first function is the place to begin. In order to communicate with the MELCloud API and your device, you must authenticate yourself. To successfully retrieve an API-key (*Mitsubishi uses the term 'ContextKey', so I decided to use that name*), you must provide a valid ````username```` and ````password````.

*Syntax example:*	
````powershell
Get-MELCloudContextKey -Credentials <Your_Username>
````
After you have provided a valid username (email address), hit ````Enter```` on your keyboard to continue. You will now be presented with a standard PowerShell credential window.
Enter your ````password```` in the *password field* and hit that OK button to continue.

![CRED](/assets/images/MELCLOUD/PSCREDWINDOW.PNG)

After succsesssfully authenticated against the MELCloud service, a ````ContextKey```` and the name of the ````RegistredOwner````, ````CountryName````, and ````LanguageCode```` will be written to the console.

*Output example:*
````powershell
   ContextKey                      RegistredOwner        CountryName LanguageCode
   ----------                      --------------        ----------- ------------
   <12345678912345678912345678912>  <FirstName LastName>    <Norway>   <no>    
````

*Hint:*
You must use  the ````ContextKey```` in every HTTP-request, so I recommend copying the ````ContextKey```` into a variable for a more efficient use.
````powershell 
$Key = <YourAPIKey>
````


	
2. **Get-MELCloudDeviceInfo**

Will list all kind of technical information about your MELCloudDevice(s).

*Syntax example:*
````powershell
Get-MELCloudDeviceInfo -ContextKey $Key
````

*Output example:*
````powershell
   DeviceType                   : 0
   CanCool                      : True
   CanHeat                      : True
   CanDry                       : True
   HasAutomaticFanSpeed         : True
   AirDirectionFunction         : True
   SwingFunction                : True
   NumberOfFanSpeeds            : 5
   UseTemperatureA              : True
   TemperatureIncrementOverride : 0
   TemperatureIncrement         : 0,5
   MinTempCoolDry               : 16,0
   MaxTempCoolDry               : 31,0
   MinTempHeat                  : 10,0
   MaxTempHeat                  : 31,0
   MinTempAutomatic             : 16,0
   MaxTempAutomatic             : 31,0
   LegacyDevice                 : False
   UnitSupportsStandbyMode      : True
   HasWideVane                  : False
   
   #The output has been cut off due to large output
````



3. **Get-MELCloudDevice**

*As I only own one MELCloud device, I haven't had the opportunity to fully test and confirm that this function works 100% correct.*

Will list all of your registered MELCloud devices.

*Syntax example:*
````powershell
Get-MELCloudDevice -ContextKey $Key
````

*Output example:*
````powershell
    DeviceID    BuildingID      DeviceName          MacAddress            SerialNumber
    --------    ----------      ----------          ----------            ------------
    <DeviceID> <BuildingID> <NameOfYourDevice> <MacAddressOfYourDevice>  <10-digit S/N>
````

Please use this function to obtain the ````DeviceID```` of your device(es).
The```` DeviceID```` is a mandatory paramenter when using for example the ````Set-MELCloudDevice```` function. The ````DeviceID```` is (as you may already have understood), the ````ID```` of your MELCloud device. In order to fully communicate with your device, you will have to send the corresponding ````ID```` to the MELCloud platform in each HTTP-request.

I also here recommend that you put your ````DeviceID```` into a variable for easier use:
````powershell
$ID = 123456
````

4. **Start-MELCloudDevice**

Use this function to start your selected device.
   
*Syntax example:*
````powershell
Start-MELCloudDevice -DeviceID <DeviceID> -ContextKey <ContextKey>
````

After a successful start, the following is written to the console:

*Output example:*
````powershell
 Starting up your selected device..

Power SetTemperature SetFanSpeed
----- -------------- -----------
 True           20,0           2 
````

*Please note that the default temperature is set to 20 degrees celsius and a fan speed of 2.
If you wish to change the temperature or the fan speed, this can be completed by using the ````Set-MELCloudDevice```` function.*



5. **Set-MELCloudDevice**

This is probably the most useful function in this module. This badboy allows you to set different settings on your device. You can adjust the temperature, set desired fan speed, change mode among some other things.

*Syntax example:*
````powershell
 Set-MELCloudDevice -DeviceID <DeviceID> -ContextKey <ContextKey> -Temperature <"10" -" 31" degrees celsius> -FanSpeed <"1" - "5"> -VaneHorizontal <"1" - "5"> -VaneVertical <"1" to "5">`
   -Mode <"Heating" or "Cooling"> -HorizontalSwing<"On" / "Off"> -VerticalSwing <"On" / "Off">
````
The example below sets the temperature to 21 degrees (celsius) with a fan speed of 2:
````powershell
Set-MELCloud -DeviceID <DeviceID> -ContextKey <ContextKey> -Temperature <21> -FanSpeed <2> -Mode <Heating>
````

Please note that the ````Mode````, ````Temperature```` and ````FanSpeed```` parameters is mandatory in each request. This due to some different (..and changing) values in the ````Body```` of the ````HTTP-request```` that all of there parameter relies on. The value increments based on each selected parameter. I just haven't found the time to sort this one out in a different way. 


*Output example:*
````powershell
Power SetTemperature SetFanSpeed OperationMode VaneHorizontal VaneVertical
----- -------------- ----------- ------------- -------------- ------------
 True           21,0           5             1              0            0
````

Above you see the output written to the console after you successfully have set your desired settings on your device.
FYI: ````OperatingMode```` equals 1 = Heating | ````OperatingMode```` equals 3 = Cooling.


6. **Stop-MELCloudDevice**

Allows you to stop/shutdown your device. 

*Syntax example:*
````powershell
Stop-MELCloudDevice -DeviceID <DeviceID> -ContextKey <ContextKey>
````

*Output example:*
````powershell
Shutting down your selected device..
````

I have choosen to to a simple ````Write-Host```` as the output in this function.
It only acts as a visual output that the function has been sent, and that your selected device will shutdown in a few moments.



**If you have any questions regarding this module, encounter some problems or maybe some bugs, please drop me an e-mail at ````freddie@cloudpilot.no````.**






   















