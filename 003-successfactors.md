### Integrate SAP Successfactors

**Overview**

This guide provides instructions for integrating Okta with SAP Success Factors so that the following use cases can be demonstrated:
HRaaS - Sourcing a user profile from SAP Success Factors into Okta's Universal Directory.

This lab uses a pre-built and managed instance of SAP Success Factors. The environment has users and groups defined in a pre-designed organisational structure. The integration uses an API connection to import users from SAP Success Factors into Okta’s Universal Directory.

Okta maintains a specific integration for SAP Success Factors in the Okta Integration Network (OIN). To add this to your Okta org, follow these steps:

1.  Login to the admin console of your Okta demo org by clicking on the **Launch** button at the top left of this lab

2.  Navigate to **Applications \> Applications** then click on **Browse App Catalog**
   
   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/009/image01.png "image_tooltip")

3. Search for **SAP Success Factors**
   
   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image00.png "image_tooltip")

4. click **Add Integration**

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image05.png "image_tooltip")

5. In the **General tab**, copy and paste this company ID **SFPART068962** then click **Next** 

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image06.png "image_tooltip")
```
Company ID = SFPART068962

```

6. Select **SAML 2.0** then click **Done**.

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image09.png "image_tooltip")

7. Now you need to configure the provisioning in order to push users from SAP to Okta.
   
   Go to the **Provisioning** tab, click on **Configure API Integration**, click on **Enable API Integration**, enter the base URL, admin username and admin password (you can find them below) then click on **Test API Credentials**. If the test result is green, click on **Save**
   
   **PLEASE COPY AND PASTE THE ADMIN PASSWORD FROM THIS LAB INTO OKTA TO AVOID LOCKING OUT THE ACCOUNT**

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

8. Go to **Provisioning** > **To Okta**, click **Edit** next to the **General** section, choose **custom** from **Okta username format** then enter the following expression and click on **Save**:
   
   >  String.substringBefore(appuser.email,"@") + "@myofficedomain.onmicrosoft.com"

   Please ensure to use your Office domain.

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image32.png "image_tooltip")

9. Scroll down to **Profile & Lifecycle Sourcing**, click **Edit** then select the followings then click **Save**:
    - Allow SuccessFactors to source Okta users
    - Reactivate suspended Okta users
    - Reactivate deactivated Okta users

10. Scroll down to the attributes section, click the pencil next to the **Primary email**, select **Expression** then enter the following expression with your office domain and click **Save**:
    
    > String.substringBefore(appuser.email,"@") + "@myofficedomain.onmicrosoft.com"
    
   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image31.png "image_tooltip")

11. **Optional**
    
    You will now add some verifications on the first name and last name of the SAP user before importing it into Okta
    
    Go to **Provisioning** > **To Okta**, scroll down to the **Okta Attributes Mapping** section,  click on the pencil next to **First Name**, choose **Expression** from the attribute value then copy the expression language below and click on **Save**. Do the same thing for **Last Name**.

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image13.png "image_tooltip")


   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image14.png "image_tooltip")


   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image16.png "image_tooltip")

```
Expression Languages:

FirstName:
String.len(String.removeSpaces(appuser.firstName)) > 0 ? appuser.firstName : "F_" + appuser.userName

LastName:
String.len(String.removeSpaces(appuser.lastName)) > 0 ? appuser.lastName : "L_" + appuser.userName
```

12.  Now it's time to configure the manager attribute:
   - In the left menu, go to **Directory** > **Profile Editor**
   - Click on the app **SuccessFactors**
   - Click on **Add Attribute**, search for **manager_id**, select the **ST1** attribute and click on **Save**
   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image20.png "image_tooltip")
   - Search for **manager** and add the 2 attributes **Manager Person First Name** and **Manager Person last Name**. Ensure to add the **ST1** attributes as there are different categories in SAP.
   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image27.png "image_tooltip")
   - Go back to **Profile Editor**, click on **Mappings** next to the app **SuccessFactors**
   - In the tab **SuccessFactors to Okta User**, scroll down until you see the attribute **managerId**, search for manager in the text box, select the attribute you have added earlier
   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image22.png "image_tooltip")
   - Now search for the attribute **manager** in the righ column and use the below expression language to build the display name of the manager as we don't have this attribute in SAP then click on **Save**
   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image28.png "image_tooltip")
   
   ```
   String.append(String.append(appuser.person___employment_information_ST1___job_information___manager_person_first_name," "),appuser.person___employment_information_ST1___job_information___manager_person_last_name)
   ```

13.   In the same mapping page, we'll map the secondary email to your personal email. This step is important, because when the user is imported from SAP SuccessFactors into Okta, as he has no password defined, he will receive an activation email to his secondary email in order to activate his account.
    ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image30.png "image_tooltip")


14. You are now ready to import the users from SAP to Okta. The import can be scheduled automatically, however we prefer to do it manually in this lab in order to see this step. Go to your application, click on the **Import** tab, click on **Import Now**, select **Full import** then click on **Import**. This step will take around 5min as the connector will have to import around 1300 users the first time, it will be so much quicker afterwards.

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image18.png "image_tooltip")

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image19.png "image_tooltip") 


15. Users have been imported into Okta but not confirmed yet, this is an extra step that can be skipped if needed. For the purpose of this lab, we kept it manual.

   We will confirm the import of **Emily Boone**.

   In the import page, type **emily** in the search bar, select **Emily Boone** then click on **Confirm Assignments**. 
   
   Click on **Auto-activate users after confirmation** then on **Confirm**
   
   The user Emily has been imported into Okta.
   Do the same thing for user **Wes Chang** who is the manager of Emily

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image24.png "image_tooltip")

   ![alt_text](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/010/image25.png "image_tooltip")



