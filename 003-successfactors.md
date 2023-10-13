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

1. Select SAML 2.0 then click on **Done**.

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image06.png "image_tooltip")

2. Now you need to configure the provisioning in order to push users from SAP to Okta.
   
   Go to the **Provisioning** tab, click on **Configure API Integration**, click on **Enable API Integration**, enter the base URL, admin username and admin password (you can find them below) then click on **Test API Credentials**. If the test result is green, click on **Save**
   
   > **PLEASE COPY AND PASTE THE ADMIN PASSWORD FROM THIS LAB INTO OKTA TO AVOID LOCKING OUT THE ACCOUNT**

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


9. In this section, you will configure the connector in order to customize the first name and last name of the SAP user before importing it into Okta.

   Go to **Provisioning** > **To Okta**, scroll down to the **Okta Attributes Mapping** section,  click on the pencil next to First Name, choose **Expression** from the attribute value then copy the expression language below and click on **Save**. Do the same thing for Last Name.



```
FirstName:
 String.len(String.removeSpaces(appuser.firstName)) > 0 ? appuser.firstName : "F_" + appuser.userName

LastName:
 String.len(String.removeSpaces(appuser.lastName)) > 0 ? appuser.lastName : "L_" + appuser.userName
```

10.  Search for user XXXXX and selct the check box to only import this user from SAP Success Factors to your Okta Org
    
    ***IMPORTANT: DO NOT IMPORT ALL USERS FROM SAP SUCCESS FACTORS***

       ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image07.png "image_tooltip")
