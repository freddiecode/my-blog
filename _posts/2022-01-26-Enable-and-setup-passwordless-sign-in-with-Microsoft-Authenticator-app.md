---

title: "Enable and setup passwordless sign-in with Microsoft Authenticator app"
classes: wide
date: 2022-01-26 14:00:00
categories:
- Blog
tags:
- Microsoft Azure Active Directroy
- Microsoft 365
- Microsoft Authenticator
- Passwordless
---
## Enable and setup passwordless sign-in with Microsoft Authenticator app

No need to remember your account password! The Microsoft Authenticator app can be used to sign in to any Azure AD account without using a password.

### Prerequisites
To use passwordless phone sign-in with the Microsoft Authenticator app, the following prerequisites must be met:

-   Recommended: Azure AD Multi-Factor Authentication, with push notifications allowed as a verification method. Push notifications to your smartphone or tablet help the Authenticator app to prevent unauthorized access to accounts and stop fraudulent transactions. The Authenticator app automatically generates codes when set up to do push notifications so a user has a backup sign-in method even if their device doesn't have connectivity.
-   Latest version of Microsoft Authenticator installed on devices running iOS 8.0 or greater, or Android 6.0 or greater.
-   The device on which the Microsoft Authenticator app is installed must be registered within the Azure AD tenant to an individual user.

## Enable passwordless authentication methods

To use passwordless authentication in Azure AD, first enable the combined registration experience, then enable users for the passwordless method.

To enable the authentication method for passwordless phone sign-in, complete the following steps:

1. Sign in to the [Azure portal](https://portal.azure.com).
2. Navigate to **Azure Active Directory**, then browse to **Security** > **Authentication methods** > **Policies**. 

![](/assets/images/PASSWORDLESS/01.png)

3. Under **Microsoft Authenticator**, select the following options:
a. **Enable** - Yes
b. **Target** - All users or Selected users.  In my example, I'll added a single user, `John Doe`.

![](/assets/images/PASSWORDLESS/02.png)

4. Each added group or user is enabled by default to use Microsoft Authenticator in both passwordless and push notification modes ("Any" mode). To change this, for each row:
a.  Browse to  **...** (3)  >  **Configure**.

![](/assets/images/PASSWORDLESS/03.png)

b.  For  **Authentication mode**  - choose  **Any** (4), or  **Passwordless**. Choosing  **Push**  prevents the use of the passwordless phone sign-in credential.

c.  To apply the new policy, click  **Save** (6).

![](/assets/images/PASSWORDLESS/04.png)

## End-user registration and management of Microsoft Authenticator

Users register themself for the passwordless authentication method of Azure AD by using the following steps:

1. Browse to [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo).
2. Sign in, then click **Add method** > **Authenticator app** > **Add** to add the `Authenticator app`.
3. Follow the instructions to install and configure the `Microsoft Authenticator` app on their device.
4. Select **Done** to complete the Authenticator configuration.
5. On their device, in **Microsoft Authenticator**, choose **Setup phone sign-in** from the drop-down menu for the account registered.

* Select `***` on the top right corner.

![](/assets/images/PASSWORDLESS/05.jpg)

* Select **Set up phone sign-in**.

![](/assets/images/PASSWORDLESS/06.jpg)

* Select **Continue**.

![](/assets/images/PASSWORDLESS/07.jpg)

* Enter your account password and click **Sign-in**.

![](/assets/images/PASSWORDLESS/08.jpg)

* Confirm MFA **Sign-in request** on your device.

![](/assets/images/PASSWORDLESS/09.jpg)

* Press **Register** to register your device in Azure Active Directory.

![](/assets/images/PASSWORDLESS/10.jpg)

#### Device registration
Before you can create this new strong credential, there are prerequisites. One prerequisite is that the device on which the Microsoft Authenticator app is installed must be registered within the Azure AD tenant to an individual user.

Currently, a device can only be enabled for passwordless sign-in in a **single tenant**. This limit means that only one work or school account in the Microsoft Authenticator app can be enabled for phone sign-in.

> Note
>Device registration is not the same as device management or mobile device management (MDM). Device registration only associates a device ID and a user ID together, in the Azure AD directory.

* Press **Finish** to complete the passwordless sign-in setup.

![](/assets/images/PASSWORDLESS/11.jpg)


## Sign in using passwordless credentials

1. User enters her name the sign-in page.
2. Selects **Next**.
3. If necassary, selects **Others ways to sign in**.
4. Select **Approve a request on my Microsoft Authenticator app**.

* The user is then presented with a number on their screen:

![](/assets/images/PASSWORDLESS/12.png)

* The **Authenticator app** prompts the user to autenticate by typing in the correct number, instead of by entering their account password:

![](/assets/images/PASSWORDLESS/13.jpg)

After the user has successfully entered the same number in the **Authenticator app**, the user are securely logged in.

* If the user would like to disable phone sign-in, they can select **Disable phone sign-in** in the **Authenticator app** and follow the steps to disable this service.

![](/assets/images/PASSWORDLESS/15.jpg)
