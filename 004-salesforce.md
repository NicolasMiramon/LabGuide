### Configure SSO and LCM for Salesforce

## Overview

This guide provides instructions for integrating Okta with Salesforce so that the following use cases can be demonstrated:



* Single Sign-on (SSO) from Okta to Salesforce
* Lifecycle Management (LCM) for Salesforce


### Introduction

Salesforce is a Customer Relationship Management (CRM) platform. As well as being a well known SaaS application, it is very useful as part of a demonstration environment because it has a free and non-expiring _developer edition_ which supports single sign-on and provisioning.

Salesforce supports both SAML 2.0 and OpenID Connect (OIDC) for Single Sign-On (SSO) from an external Identity Provider such as Okta. The Okta SSO integration with Salesforce in the Okta Integration Network uses SAML 2.0.

Salesforce provides REST APIs for management of users and groups.  Two different approaches to API security are currently supported.  The latest and recommended approach, supported by OIE,  uses an API Key and Consumer Secret (used in the same way as OAuth 2.0 client credentials).  This approach is used in this guide.

It’s worth noting that Okta Classic implements an older API security approach which uses an administrator password and API Token for access to the Salesforce provisioning APIs. You may see references to this in older documents.  This older integration method can be enabled in OIE, using Feature Flags, but that is not covered here.


## Add Salesforce application to Okta

Okta maintains a specific integration for Salesforce in the Okta Integration Network (OIN).  To add this to your Okta org, follow these steps:



1. Use a browser to open the administration UI of your Okta demo org and sign in as an administrator.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image1.png "image_tooltip")


2. Navigate to **Applications > Applications**.
3. Click **Browse App Catalog **to open the Okta Integration Network browser.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image2.png "image_tooltip")


4. Search for and select the **Salesforce.com** integration.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image3.png "image_tooltip")


5. Click **Add integration**.
6. The _Add Salesforce.com_ wizard opens on the _General Settings_ page:


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image4.png "image_tooltip")



    No changes are required on the _General Settings _page.

7. Click **Next**. \
You are taken to the _Sign-On Options_ page of the wizard.


### Configure SSO in Okta


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image5.png "image_tooltip")
 \
This integration supports both Secure Web Authentication (SWA), for password injection, and SAML 2.0, for federated single sign-on.



8. Select the radio button for **SAML 2.0**.

    Note the options to set a default relay state and to add attributes to the attribute statement or group statement within SAML 2.0 messages. You don’t need these for the demo setup.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image6.png "image_tooltip")


9. While you’re here, click the **View Setup Instructions **button.  This will open org-specific instructions for setting up Salesforce to receive SAML 2.0 assertions from your Okta org.  It will open in a new tab which you’ll refer to later on.
10. Go back to the tab where the Okta administration UI is open:


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image7.png "image_tooltip")



    Under Credential Details, you can specify the Application username format.  This determines the username that is passed to Salesforce in single sign-on messages if provisioning is not enabled.  It also sets the default username that will be used to create accounts in Salesforce when provisioning is enabled. 


    In Salesforce usernames must be globally unique; no two users in Salesforce can have the same username even if they are in different tenants. An easy way to ensure uniqueness is to include a username suffix that is unique to your environment.  In this case you will use a custom expression which replaces the domain part of the Okta username with the unique domain of your demo environment.

11. Set _Application username format_ to **Custom**.
12. Enter the following for the expression - replacing yourdemodomain.com with your own custom DNS domain:

        **String.substringBefore(user.login,"@") + "@yourdemodomain.com"**


    This will create the Salesforce username by taking the part of the Okta username attribute (okta.login) before the @ sign and then adding @yourdemodomain.com. \
For example, _your.name**@okta.com**_ would become _your.name**@yourdemodomain.com**_.

13. Click **Done**.

    The wizard completes.


Your Okta org is now configured to send SAML 2.0 assertions to Salesforce for single sign-on.  You must now configure Salesforce so that it will trust these assertions.


### Get required information from Okta

During the configuration of Salesforce, you will need several pieces of information from Okta.  These are all dynamically included in the setup instructions which you opened in the preview section.



1. Go to the browser tab that contains the setup instructions for Salesforce and scroll down to find the dynamic information fields:


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image8.png "image_tooltip")



    Notice the _Issuer_, _Identity Provider Login URL_, and _Custom Logout URL_.  You will need all of these in the next section.

2. Click the link for the **Identity Provider Certificate**.. \
This will initiate a file download.
3. Save the certificate file on your local machine.


### Configure SSO in Salesforce

You will now use the information gathered above to configure Salesforce for single sign-on from Okta.



1. Open a new browser tab and navigate to: [https://login.salesforce.com](https://login.salesforce.com). \
Use a browser that is not logged into your Okta Salesforce.  If using Chrome, you could create a new profile or use the _Guest _profile.  You could also use a different browser (Firefox vs Chrome).


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image9.png "image_tooltip")

    If SalesForce ask for an OTP code sent by email, please contact your Okta animator for the access

2. Login to Salesforce using the administrator account of your developer edition instance.

        If you see this message at the top of the page:


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image10.png "image_tooltip")
 \
Click **Switch to Lightning Experience** to go to the latest Salesforce UI.  The instructions below assume you are using this


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image11.png "image_tooltip")


3. Navigate to **Identity > Single Sign-On Settings**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image12.png "image_tooltip")


4. Click **New **under **SAML Single Sign-On Settings**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image13.png "image_tooltip")


5. Enter a _Name_.  It can be whatever you like.
6. Copy and paste the _Issuer, Identity Provider Login URL, _and _Custom Logout URL_ from the setup instructions.
7. Enter **https://saml.salesforce.com** as the _Entity ID_.
8. Click **Choose File **for _Identity Provider Certificate_ and upload the file you downloaded from setup instructions.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image14.png "image_tooltip")


9. Click **Save**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image15.png "image_tooltip")


10. Click **Back to Single Sign-On Settings**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image16.png "image_tooltip")


11. Click **Edit** under **Single Sign-On Settings**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image17.png "image_tooltip")


12. Check the **SAML Enabled **checkbox.
13. Click **Save**.


### Set Login and Logout URLs in Okta

The final part of Single Sign-On configuration is to configure Okta with the Login URL and Logout URL which have been generated by Salesforce for your new SSO settings.



1. If not already there, navigate to your SSO settings in Salesforce admin UI:


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image18.png "image_tooltip")


2. Note the values for _Login URL_ and _Logout URL _under _Endpoints. \
_These are the values you need to configure in Okta.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image19.png "image_tooltip")


3. In your Okta admin UI, click **Edit** for _Settings_ in the _Sign On _tab of the Salesforce.com application definition.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image20.png "image_tooltip")


4. Copy and paste the values for _Login URL _and _Logout URL_ from Salesforce UI.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image21.png "image_tooltip")


5. Scroll to the bottom of the page and click **Save**.

Single sign-on is now configured.  The next step is to configure provisioning.

[//]: <> (Section to be hiden in this label. All credentials will be provided to users)
## Configure provisioning

The single sign-on configuration performed in the previous section allows your Okta org to assert identity information to your Salesforce tenant and it allows your Salesforce tenant to trust it.  However that’s not enough for single sign-on to complete.  Each identity asserted by Okta must link to an existing account in Salesforce..

The required Salesforce accounts could be created manually but, in this case, you will set up provisioning in Okta so that it can automatically manage accounts in Salesforce.  It will create accounts when users are assigned to the Salesforce application and deactivate these accounts when users are unassigned.  It will also manage user attributes and roles.

You’ll also run a one-time import and link your Okta admin user with your Salesforce admin account. This shows how existing accounts can be assigned to Okta users.


### Create a Connected App in Salesforce

To make the lab run smoothly, all the Salesforce configuration tasks involved in declaring a new application have been generated in advance.

**Consumer Key and Consumer Secret informations have been sent to you.** 

### Configure provisioning in Okta


1. If you are not already there, open your Okta administration UI, navigate to **Applications > Applications**, and select the **Salesforce.com **application definition.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image31.png "image_tooltip")


2. Click on the **Provisioning** tab and click the **Configure API integration **button.

To find the Consumer Key and Consumer Secret please consult the attached document with information about your SalesForce tenant.  

![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image32.png "image_tooltip")


3. Check the checkbox for **Enable API integration**.

4. Copy and paste the values for _OAuth Consumer Key _and _OAuth Consumer Secret_ from the Salesforce Connected App properties page.

5. Click **Authenticate with Salesforce.com**.

    A browser pop-up opens:


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image33.png "image_tooltip")

    If SalesForce ask for an OTP code sent by email, please contact your Okta animator for the access

6. Authenticate to your Salesforce tenant the administrative user. \
A consent page is displayed asking you to grant permissions to your Okta org (which is connecting as the Connected App you created in the previous section):


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image34.png "image_tooltip")


7. Click **Allow** to grant the requested permissions. \
The pop-up closes and you are returned to the Okta admin console:


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image35.png "image_tooltip")



    Hopefully you see the success notifications saying that the API client is authenticated.

8. Click **Save**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image36.png "image_tooltip")



The API connection for provisioning is now in place. Provisioning configuration is now shown under the Provisioning tab.  The integration configuration you just created is under the Integration tab.

There are two provisioning directions supported by this integration:


    **To App** - This is provisioning from Okta to Salesforce.  This is the integration you configure if you want Okta to manage accounts in Salesforce.


    **To Okta** - This is provisioning from Salesforce to Okta.  This is the integration you would set up if you want to create Okta accounts when users are created in Salesforce.  It can also be used to match accounts created in Salesforce to users in Okta.


### Enable provisioning to app

In this section you will enable provisioning to Salesforce.  This will include creation of accounts in Salesforce when users are assigned to the application, updating attributes in Salesforce when things change in Okta, and deactivating accounts in Salesforce when users are unassigned from the application in Okta.

Note: In general, Okta does not delete accounts from applications - it only deactivates them.  This ensures that Okta is not responsible for any data loss in the backend system that could be triggered by deleting an account.

Follow these steps to enable provisioning:


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image37.png "image_tooltip")



    You should already be on the **To App** tab under **Provisioning**.



1. Click **Edit** to set the options for Okta provisioning to Salesforce.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image38.png "image_tooltip")


2. Click the **Enable** checkbox for _Create Users_.
3. Click the **Enable** checkbox for _Update User Attributes_.
4. Click the **Enable** checkbox for _Deactivate Users_.

    Note:_ Sync Password_ is not enabled.  When federation is enabled for single sign-on, no password is required in Salesforce because users will authenticate in Okta and be asserted to Salesforce based on the trusted federation relationship.

5. Click **Save**.

OK.  Provisioning is now enabled.


## Configure group assignment

In this section you will assign an Okta group to the Salesforce application. You will configure the application attributes for the group so that members are assigned the unlimited “Chatter Free” role in Salesforce.

You will then assign a test user to that group.  This will result in the test user being provisioned with an account in Salesforce.


### Create a group

You will now create a group that will be assigned to the Salesforce application.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image50.png "image_tooltip")




1. In the administration UI for your Okta org, navigate to **Directory > Groups**.
2. Click the **Add group **button.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image51.png "image_tooltip")


3. Enter **SalesforceUsers **as the Name of the group.
4. Enter **Salesforce Users ** as the Description.
5. Click **Save**.

    Note: The group may not immediately show in the group list.  Click on the Group navigation item again to reload the page and it should be shown.



### Assign group to application

You can assign a group to an application by either assigning the group within the application definition or by adding the application to the group definition.  In this case you will add the application from the group definition.



1. If not already there, navigate to** Directory > Groups**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image52.png "image_tooltip")


2. Click on the link for the** SalesforceUsers** group.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image53.png "image_tooltip")


3. Select the **Applications** tab in the group properties.
4. Click **Assign applications**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image54.png "image_tooltip")


5. Click the **Assign** button next to the _Salesforce.com_ application.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image55.png "image_tooltip")


6. Select **Chatter Free User **as the _Profile URL_. \
This profile is unlimited in Salesforce Developer Edition so it won’t run out.
7. Select **– No Role – **as the _Role_. \
You can’t assign a role to a Chatter Free user.
8. Scroll to the bottom of the page and click **Save and Go Back**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image56.png "image_tooltip")


9. Click **Done**.

The application is now assigned to the _SalesforceUsers_ group.


### Assign user to group

You will now assign a test user to the_ SalesforceUsers_ group.  This will cause the user to be assigned the Salesforce application which, in turn, will trigger provisioning of an account.



1. If not already there, navigate to **Directory > Groups**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image57.png "image_tooltip")


2. Select the **People** tab.
3. Click **Assign people**.


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image58.png "image_tooltip")


4. Click the **+ icon** for a test user in your Okta org. \
This should not be the admin user (which is already directly assigned to the application)

    The user is now shown as assigned:


![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image59.png "image_tooltip")



The user is now assigned to the group and will be assigned to the Salesforce application using the attribute mapping associated with the group assignment.


## Test single sign-on

You can now test single sign-on to Salesforce for your test user.

1. Open a new browser window that is not signed into Okta or Salesforce. \
This could be a private browsing window, a different profile, or a different browser.

2. Navigate to your Okta tenant. e.g. **_yourdemoorg_.okta.com**

3. Authenticate as your test user: e.g. **_alex.anderson@yourdemodomain.com_** \
You should end up on your Okta dashboard and see that a tile for Salesforce.com is shown:

![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image60.png "image_tooltip")

4. Click the tile for **Salesforce.com**. \
If single sign-on is successful you will see the Salesforce Chatter homepage:

 
![alt_text](https://raw.githubusercontent.com/MarcoBlaesing/LabGuide/main/images/004/image61.png "image_tooltip")


5. Click the identity icon in the top-right of the page.

    Notice the name shown in the pop-up window.  This should show that you are authenticated as your test user.  Provisioning and single sign-on were successful!

6. Click **Log out** to clear the session.  Close the browser you used for the test.

Congratulations! You have successfully configured Lifecycle Management and Single Sign-On to Salesforce. Your demo environment can now be used to demonstrate these capabilities to customers.
