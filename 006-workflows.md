# Okta Workflows

Okta Workflows can be used to augment Lifecycle Management and provide custom logic for organization-specific use cases. Okta Workflows is a no-code automation platform that enables admins to modernize ever-more-sophisticated identity-centric processes without leaning on developers. It provides a graphical drag-and-drop interface that combines triggers, logic, and time-based actions to build powerful “if-this-then-that” flows. As a result, anyone can easily stitch together app-specific provisioning and deprovisioning tasks. For instance, with Okta Workflows, you can leverage Okta’s library of pre-built connectors for apps like Box, Slack, Salesforce, and more (or connect via public APIs) to tailor processes with deeper actions that meet your precise requirements.

With out-of-the-box functions for flow control, branching, and data manipulation, Okta offers the power of code without code, and it is finally possible to orchestrate identity tasks that were previously just too hard to automate. By having this capability built-in to your identity architecture, your team will increase agility and decrease costs, all while facilitating constant business change and improving your company’s security posture.

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image6.png)

Here are a few ways Okta Workflows can automate complex identity scenarios:

1\. Take granular actions during onboarding and offboarding

2\. Resolve identity creation conflicts

3\. Define identity processes based on time, role, and other factors

4\. Distill and share identity insights

## Lab description

The purpose of this Workflow pack is to perform various tasks in Office 365 when a given target user is being onboarded from the HR System in the organization as a whole. This flow pack is triggered when a user is created.  The effects of this Workflow on the Office 365 account are as follows:

**Virtual Orientation:** The new hire user will be sent a virtual orientation email complete with Microsoft Teams link to the new hire’s work as well as personal email.  (The work email is as defined in Okta Universal Directory in the email attribute and the personal email will be defined by the secondary email attribute in Okta Universal Directory).

There will also be a calendar invite sent to the new hire user for the virtual orientation session, 7 days out from the date the new hire is created and this flow pack is executed.

**Microsoft Teams:** The new hire user will be added to a Microsoft Teams. The new hire user is also added to a Microsoft Teams private channel that you create ahead of time. The format of the private channel is “Department - <department in the new hire users department attribute in Okta Universal Directory>”

If the channel for that department does not exist, it will be created at the time the new hire user is assigned to the channel.  
  
Additionally a welcome message is sent to that private channel welcoming the new hire user.  

## Lab Prerequisites

For the workflows in the Okta Workflow Pack to work you will need:

### You will need to configure the following Okta connectors in your Okta Tenant that has workflow enabled

   * Configure Okta Connector

   * From Okta Admin Console, go to Workflows console under Workflow section

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image7.png)

1. Go to Connections tab and click New Connection

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image13.png)

2. Select Okta connection from the list

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image11.png)

3. Configure you Okta connection, you will need

   3.1. Okta domain name of your tenant (without the https)

   3.2. Okta Workflows App client ID and Client Secret, to get them, go Okta Workflows OAuth application, under Applications in the Okta Admin console

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image18.png)

   3.3. The configuration will look like this, click on Create button

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image4.png)

4. Configure Office 365 Admin connector

   4.1. Go to Connections tab and click New Connection

   4.2. Select Office 365 Admin from the list and click Create

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image20.png)

   4.3. Log in with your Microsoft O365 Admin account if you are not already logged in.

   4.4. Check the box Consent on behalf of your organization and click on Accept

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image21.png)

5. Configure Office 365 Mail Connector: follow the same steps to configure it

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image22.png)

6. Configure Office 365 Calendar Connector: follow the same steps to configure it

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image23.png)

7. Configure Microsoft Teams Connector: follow the same steps to configure it

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image24.png)

### Additional prerequisites

1. An existing user (that will represent the manager user) in Okta and Office 365 **where the username in Okta and in O365 are the same (the user’s email address)**. 
The manager user’s email address needs to point to a valid email address and have the appropriate Office 365 licenses so that the manager can get the email notifications as part of the flow.
I this lab we will use the manager account **Wes Chang** imported from SAP SuccessFactor.

2. An existing target user (that will represent the target user) in Okta where the **Okta Primary Email Address for that user is the same as the user’s Office 365 username**. The manager attribute in Okta Universal Directory will need to be the username of the Okta user that represents the manager and you created in the step above.
**This target user must be assign to Office 365 application.**


## Okta Workflows - Flow Pack Setup Steps

### Initialisation instructions

**Please download the flowpack below (right click -> Save link as)**

https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/files/o365OnboardingFlow.folder

1. Go to the Okta Workflows Console from the Okta Admin Console

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image7.png)

2. Go to Flow Tab and click on Add new folder (+)

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image16.png)

3. Create a new folder called Office 365 Onboarding Flow

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image2.png)

4. Import the office365OnboardingFlow.folder into this newly created folder by clicking on the three dots at the right of the folder name

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image15.png)

5. The folder structure should look like the following one

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image10.png)

1. Open the first flow **MAIN FLOW** New Hire Information by clicking on its name

2. Scroll to the right, you will see the card Send Email inside the If/Else Card, click on the Choose Connection

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image9.png)

8. Select the connection you create in the previous steps

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image12.png)

9. Activate the flow

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image19.png)

10. If you have not already done so, authorize the connections to Azure Active Directory, Microsoft Team, Office 365 Mail, Office 365 Calendar and Okta.

11. **Make sure that the following Okta Workflow cards have valid connections assigned: open ALL THE FLOWS and verify that all the connections are correctly setup**

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image25.png)

12. Toggle all the flows from Off to On

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image5.png)

## Testing the Okta Workflow Flow

Create or import a new user with a manager assign (in our case **Wes Chang**) and assign Office 365 application.

In the example below, we have created the user **Elise Dupont** in Okta and assign **Wes Chang** as manager from the user profile.

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image33.png)

In order to put the manager, go to **Profile**, click **Edit** then scroll down to the manager ID attribute and enter the value **wes.chang@wiclab56.onmicrosoft.com**, enter **Sales** in the **Department** attribute.
![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image34.png)

You will be able to visualise the flow execution in real time by clicking on "Execution History" on the main flow.

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image27.png)

### Resultat expected 

On the manager profil (**Wes Chang**) you will see : 

- A new email auto generated

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image28.png)

- A new chat message in Teams

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image29.png)


On the new user (Elise Dupont) profil you will see : 

- A welcome email

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image30.png)

- A channel in Teams "OKTA EMEA WIC Lab"

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image31.png)

- A new calendar invitation

![](https://raw.githubusercontent.com/NicolasMiramon/LabGuide/main/images/006-1/image32.png)
