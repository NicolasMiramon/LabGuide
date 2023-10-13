### Integrate SAP Successfactors

Overview

This guide provides instructions for integrating Okta with SAP Success Factors so that the following use cases can be demonstrated:
HRaaS - Sourcing a user profile from SAP Success Factors into Universal Directory.

This lab uses a pre-built and managed instance of SAP Success Factors. The environment has users and groups defined in a pre-designed organisational structure. The integration uses an API connection to import users from SAP Success Factors into Okta’s Universal Directory.

Okta maintains a specific integration for SAP Success Factors in the Okta Integration Network (OIN). To add this to your Okta org, follow these steps:

1.  Open the admin console of your Okta demo org by clicking on the **Launch** button at the top left of this lab

2.  Navigate to **Applications \> Applications**

![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/009/image01.png "image_tooltip")

3.  Click **Browse App Catalog** to open the Okta Integration Network

4. **Search** for SAP Success Factors
   
![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image00.png "image_tooltip")

5. click **Add Integration**

![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image05.png "image_tooltip")

6. In the **General tab**, copy and paste this company ID **SFPART068962** then click **Next** 

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image06.png "image_tooltip")

```
Company ID = SFPART068962

```

7. Select SAML 2.0 then click **Save**.

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image06.png "image_tooltip")

8. Now you need to configure the provisioning in order to push users from SAP to Okta. 
   Go to the **Provisioning** tab, click on **Configure API Integration**, click on **Enable API Integration**, enter the base URL, admin username and admin password (you can find them below) then click on **Test API Credentials**. If the test result is green, click on **Save**

   > **Warning**
   > This is a warning

   > [!WARNING]  
   > Please copy and paste the admin password from this lab into Okta in order to avoid locking out the account

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image08.png "image_tooltip")


```
Base URL for Web Service     https://apisalesdemo2.successfactors.eu

Admin Username               paswin

Admin Password               Part@dc55

Pre-Start Interval           0

Post-Termination Interval    0

Import Contingent Workers              (unchecked)  

Time Zone Aware Pre-hires/Terminations (unchecked) 

Import Groups                            checked 

```


1. The "Schedule Import" is being configured for "once a day" in HRM-SuccessFactors.okta.com.

For the best experience, create your own SuccessFactors provisioning with your own Okta tenant so that you can perform the manual import to verify the new employee that you created in SuccessFactors.com tenant. 

Please do not configure "Schedule Import" in your own Okta tenant.

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image03.png "image_tooltip")

9. Complete the attribute mapping using the information below;
   In order to adjust profile mapping navigate to Map Profile attributes [Directory → Profile Editor → Successfactors → 'Successfactors to Okta' mapping]


```
Login:

String.stringContains(appuser.person___logon_user_name, "@") ?
   appuser.person___logon_user_name : appuser.person___personal_information___first_name +
   "." + appuser.person___personal_information___last_name + "@" + org.subdomain + ".com"

Okta UserName Format:
(Under 'Provisioning' -> To Okta -> 'General' -> Okta Username format -> Select 'Custom'):

 String.stringContains(appuser.person___logon_user_name, "@") ? appuser.person___logon_user_name :
   appuser.person___logon_user_name + "@" + org.subdomain + ".com"


FirstName:
 String.len(String.removeSpaces(appuser.person___personal_information___first_name)) > 0 ?
  appuser.person___personal_information___first_name : "F_" + appuser.person___logon_user_name

LastName:
 String.len(String.removeSpaces(appuser.person___personal_information___last_name)) > 0 ? 
  appuser.person___personal_information___last_name : "L_" + appuser.person___logon_user_name


Email:
 String.len(String.removeSpaces(appuser.person___email_information___email_address)) > 0 ?    
  appuser.person___email_information___email_address : appuser.person___logon_user_name + "@" + org.subdomain + ".com"

```

10. Search for user XXXXX and selct the check box to only import this user from SAP Success Factors to your Okta Org
    
    ***IMPORTANT: DO NOT IMPORT ALL USERS FROM SAP SUCCESS FACTORS***

       ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image07.png "image_tooltip")
