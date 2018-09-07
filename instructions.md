# Configuring Microsoft Information Protection Solutions to Protect Your Sensitive Data

## IGNITE 2018 INSTRUCTOR LED LABS ILL3014/3015 & HANDS ON LAB HOL3000

### Introduction

Estimated time to complete this lab

75 minutes

### Objectives

After completing this lab, you will be able to:

- Configure Azure Information Protection labels
- Configure Azure Information Protection policies
- Classify and protect content with Azure Information Protection in Office applications
- Configure Exchange Online Mail Flow Rules for AIP

### Prerequisites

Before working on this lab, you must have:

- Familiarity using Windows 10
- Familiarity with PowerShell
- Familiarity with Office 2016 applications

### Lab machine technology

This lab is designed to be completed on either a native Windows 10 machine or a VM with the following characteristics:

- Windows 10 Enterprise
- Office 365 ProPlus
- Azure Information Protection client (1.29.5)

Microsoft 365 E5 Tenant credentials will be provided during the event.  If you want to run through this lab after the event, you may use a tenant created through https://demos.microsoft.com. If you do not have access to that environment, we have provided instructions in Appendix A for setting up a trial Microsoft 365 tenant.  If you do not have access to the Lab On Demand environment or would like to build this in your own lab, build instructions are provided in Appendix B.  This Lab Guide will be publicly available after the event at https://aka.ms/AIPHOL.

## Exercise 1: Configuring Azure Information Protection Policy

This exercise demonstrates using the Azure Information Protection blade in the Azure portal to configure policies and sub-labels.  We will create a new sub-label and configure protection and then modify an existing sub-label.  We will also create a label that will be scoped to a specific group.  We will next configure AIP Global Policy to use the General sub-label as default, and finally configure a scoped policy to use the new scoped label as default for Word, Excel, and PowerPoint while still using General as default for Outlook.

### Creating, Configuring, and Modifying Sub-Labels

In this task…Internal label/Partner DNF

- [] Switch to the Client1 virtual machine and use the provided login credentials if necessary.

> [!NOTE] Check the bar in the top center of the lab environment to identify which machine you are logged into and to switch machines.

- [] Open a web browser in private mode and navigate to https://portal.azure.com/
- [] Log in using the provided Microsoft 365 Credentials

> [!NOTE] If you do not have credentials, you may follow the instructions in Appendix A – Tenant Setup to create your own demo tenant.

- [] After logging into the portal, type the word info into the search bar and then click on Azure Information Protection. This will display the Azure Information Protection - Labels blade
- [] Under CLASSIFICATIONS in the left pane, click on Labels to load the Azure Information Protection – Labels blade.
- [] In the Azure Information Protection – Labels blade, right-click on Confidential and click Add a sub-label.
- [] In the Sub-label blade, enter Contoso Internal for the Label display name and for Description enter text similar to “Confidential data that requires protection, which allows Contoso Internal employees full permissions. Data owners can track and revoke content.”
- [] Then, under Set permissions for documents and emails containing this label, click Protect, and under Protection, click on Azure (cloud key).
- [] In the Protection blade, click + Add Permissions.
- [] In the Add permissions blade, click on + Add contoso – All members and click OK.
- [] In the Protection blade, click OK.
- [] In the Sub-label blade, scroll down to the Set visual marking (such as header or footer) section and under Documents with this label have a header, click On.
- [] Use the values in the table below to configure the Header.

| Setting          | Value            |
|:-----------------|:-----------------|
| Header text      | Contoso Internal |
| Header font size | 24               |
| Header color     | Purple           |
| Header alignment | Center           |

> [!NOTE] These are sample values to demonstrate marking possibilities and NOT a best practice.

- [] To complete creation of the new sub-label, click the Save button and then click OK in the Save settings dialog.
- [] In the Azure Information Protection - Labels blade, (if necessary) expand Confidential and then click on Recipients Only.
- [] In the Label: Recipients Only blade, change the Label display name from “Recipients Only” to “Do Not Forward”.
- [] Next, under Set permissions for documents and emails containing this label, under Protection, click Azure (cloud key): User defined.
- [] In the Protection blade, under Set user-defined permissions (Preview), verify that only the box next to In Outlook apply Do Not Forward is checked, then click OK.

> [!NOTE] Although there is no action added during this step, it is included for users that may not have access to the demos.microsoft.com or event tenants.

- [] Click Save in the Label: Recipients Only blade and OK to the Save settings prompt. 
- []  Click the X in the upper right corner of the blade to close.

> [!NOTE] This is necessary as it gives you the ability to make multiple changes before leaving the blade.

### Configuring Global Policy

In this task, we will assign the new sub-label to the Global policy and configure several global policy settings that will increase Azure Information Protection adoption among your users and reduce ambiguity in the user interface.

- [] In the Azure Information Protection blade, under CLASSIFICATIONS on the left, click Policies then click the Global policy.
- [] In the Policy: Global blade, below the labels, click Add or remove labels.
- [] In the Policy: Add or remove labels blade, check the box next to Contoso Internal and click OK.
- [] In the Policy: Global blade, under the Configure settings to display and apply on Information Protection end users section, configure the policy to match the settings shown in the table and image below.

| Setting | Value |
|:--------|:------|
| Select the default label | General |
|All documents and emails must have a label…|On
Users must provide justification to set a lower…|On
For email messages with attachments, apply a label…|Automatic
Add the Do Not Forward button to the Outlook ribbon|Off

- [] Click Save, then OK to complete configuration of the Global policy.
- [] Click the X in the upper right corner to close the Policy: Global blade.

### Creating a Scoped Label and Policy

Now that you have learned how to work with global labels and policies, we will create a new scoped label and policy for the Legal team at Contoso.  (If you are using your own demo tenant you may need to create the users and described.)

- [] Under CLASSIFICATIONS on the left, click Labels.
- [] In the Azure Information Protection – Labels blade, right-click on Highly-Confidential and click Add a sub-label.
- [] In the Sub-label blade, enter Legal Only for the Label display name and for Description enter “Data is classified and protected. Legal department staff can edit, forward and unprotect.”.
- [] Then, under Set permissions for documents and emails containing this label, click Protect and under Protection, click Azure (cloud key)
- [] In the Protection blade, under Protection settings, click the +Add permissions link.
- [] In the Add permissions blade, click +Browse directory
- [] In the AAD Users and Groups blade, wait for the names to load, then check the boxes next to Adele Vance and Alex Wilber, and click the Select button.
- [] In the Add permissions blade, click OK.

> [!NOTE] In a production environment, you will typically use a synced or Azure AD Group rather than choosing individuals.

- [] In the Protection blade, under Allow offline access, reduce the Number of days the content is available without an Internet connection value to 3 and press OK.

> [!NOTE] This value determines how many days a user will have offline access from the time a document is opened, and an initial Use License is acquired.  While this provides convenience for users, it is recommended that this value be set appropriately based on the sensitivity of the content.

- [] Click Save in the Sub-label blade and OK to the Save settings prompt to complete the creation of the Legal Only sub-label.
- [] In the Azure Information Protection blade, under Classifications on the left, click Policies then click the +Add a new policy link.
- [] In the Policy blade, for Policy name, type Legal Scoped Policy and click on Select which users or groups get this policy. Groups must be email-enabled.
- [] In the AAD Users and Groups blade, click on Users/Groups.  
- [] Then in the second AAD Users and Groups blade, wait for the names to load and check the boxes next to Adele Vance and Alex Wilber
- [] Click the Select button.
- [] Finally, click OK.
- [] In the Policy blade, under the labels, click on Add or remove labels to add the scoped label.
- [] In the Policy: Add or remove labels blade, check the box next to Legal Only and click OK.
- [] In the Policy blade, under Configure settings to display and apply on Information Protection end users section, under Select the default label, select Highly Confidential \ Legal Only as the default label for this scoped policy.
- [] Click Save, then OK to complete creation of the Legal Scoped Policy.
- [] Click on the X in the upper right-hand corner to close the policy.

### Configuring Advanced Policy Settings

There are many advanced policy settings that are useful to tailor your Azure Information Protection deployment to the needs of your environment.  In this task, we will cover one of the settings that is very complimentary when using scoped policies that have a protected default label.  Because the Legal Scoped Policy we created in the previous task uses a protected default label, we will be adding an alternate default label for Outlook to provide a more palatable user experience for those users.

- [] In the Azure Information Protection blade, under CLASSIFICATIONS on the left, click on Labels and then click on the General abel.
- [] In the Label: General blade, scroll to the bottom and copy the Label ID and close the blade using the X in the upper right-hand corner.
- [] In the AIP Portal, under CLASSIFICATIONS on the left, click on Policies. Right-click on the Legal Scoped Policy and click on Advanced settings.
- [] In the Advanced settings blade, in the textbox under NAME, type OutlookDefaultLabel.  In the textbox under VALUE, paste the Label ID for the General label you copied previously, then click Save and close.

> [!NOTE] This and additional Advanced Policy Settings can be found at https://docs.microsoft.com/en-us/azure/information-protection/rms-client/client-admin-guide-customizations 

### Defining Recommended and Automatic Conditions

One of the most powerful features of Azure Information Protection is the ability to guide your users in making sound decisions around safeguarding sensitive data.  This can be achieved in many ways through user education or reactive events such as blocking emails containing sensitive data. However, helping your users to properly classify and protect sensitive data at the time of creation is a more organic user experience that will achieve better results long term.  In this task, we will define some basic recommended and automatic conditions that will trigger based on certain types of sensitive data.

- [] Under CLASSIFICATIONS on the left, click Labels.
- [] In the Azure Information Protection - Labels blade, expand Confidential and click on Contoso Internal.
- [] In the Label: Contoso Internal blade, scroll down to the Configure conditions for automatically applying this label section, and click on +Add a new condition.
- [] In the Condition blade, click on Custom and enter Password for the Name and in the textbox below Match exact phrase or pattern, enter the word password.
- [] Click Save in the Condition blade and OK to the Save settings prompt.

> [!NOTE] By default, the condition is set to Recommended and a standard policy tip is provided.

- [] Click Save in the Label: Contoso Internal blade and OK to the Save settings prompt.
- [] Press the X in the upper right-hand corner to close the Label: Contoso Internal blade.
- [] Next, expand Highly Confidential and click on the All Employees sub-label.
- [] In the Label: All Employees blade, scroll down to the Configure conditions for automatically applying this label section, and click on +Add a new condition.
- [] In the Condition blade, in the Select information types search box, type credit and check the box next to Credit Card Number.
- [] Click Save in the Condition blade and OK to the Save settings prompt.
- [] Under the Configure conditions for automatically applying this label section, under Select how this label is applied: automatically or recommended to user, click Automatic.  

> [!NOTE] The policy tip is updated with default messaging.

- [] Click Save in the Label: All Employees blade and OK to the Save settings prompt.
- [] Press the X in the upper right-hand corner to close the Label: All Employees blade.

## Exercise 2: Client Configuration

Now that we have defined some basic AIP Policies, we need to configure some clients to demonstrate the Discovery, Classification, and Protection capabilities of Azure Information Protection.  In this exercise, we will configure Office 365 Applications for 3 test users to demonstrate these policy actions.  Office 365 and the latest GA AIP Client (1.29.5) have already been installed on these systems to save time in this lab.  In your production environment, you will need to install the AIP Client manually for testing or using an Enterprise Deployment Tool like System Center Configuration Manager for widespread deployment.

### Configuring Applications

In this task, we will configure Word and Outlook for 3 test users.  These users are Alex Wilber (AlexW) and Adele Vance (AdeleV) who we have defined as members of the Legal group, and Ben Walters (BenW).  This will allow us to demonstrate the differences between the global and scoped policy and demonstrate some of the protection features of Azure Information Protection in the next exercise.

- [] On Client1, start Microsoft Word by clicking on the icon in the taskbar.
- [] When Word opens, you may see a prompt to log into Azure Information Protection.  You can close this and you will receive a prompt to sign in to set up Office. Click the Sign in button and enter AlexW@LODSEMS######.onmicrosoft.com (substituting the provided tenant name for LODSEMS#######) and provide the password pass@word1.
- [] Click Yes to the prompt to allow the device to be registered with Azure AD.
- [] Finally, click Accept and start Word to complete the setup.
- [] Close Microsoft Word
- [] Next, start Microsoft Outlook by clicking on the icon in the taskbar.
- [] This should automatically populate the ID based on the previous login.
- [] Click Connect and let Outlook configure.  If you are prompted to choose an account type, click Office 365 to continue.
- [] Uncheck the box to Set up Outlook Mobile on my phone, too and click OK.
- [] Switch to Client2 and go through the same steps for AdeleV.

> [!NOTE] The password for the Install account is Somepass1 if the client is not logged on

- [] Switch to Client3 and go through the same steps for BenW.

## Exercise 3: Testing AIP Policies

Now that you have 3 test systems with users being affected by different policies configured, we can start testing these policies.  This exercise will run through various scenarios to demonstrate the use of AIP global and scoped policies and show the functionality of recommended and automatic labeling.

### Testing User Defined Permissions

One of the most common use cases for AIP is the ability to send emails and protect documents using User Defined Permissions (Do Not Forward and Custom Permissions). In this task, we will send emails and create protected documents using these labels to test their functionality.  We will also send an email to an external Social Email Provider to demonstrate the user experience available for Office 365 Message Encryption.

- [] Switch to Client3 and make sure you are logged into Office as the user BenW.
- [] In Microsoft Outlook, create a new email.
- [] Note that the Sensitivity is set to General by default.
- [] Send an email to AlexW, AdeleV, and your personal email address (preferably at a major social email provider such as Outlook.com, Gmail, or Yahoo).
- [] In the Sensitivity Toolbar, click on the Confidential \ Recipients Only sub-label (note that the Do Not Forward user defined permission was added to the email) and send it.
- [] Switch over to Client1 or Client2 and review the email in Alex or Adele’s Outlook.  You will notice that the email is automatically shown in Outlook natively.
- [] Open the browser and log into your email and review the OME based experience.
- [] Next, on Client3, open Word and create a new document.  Enter some text and use the Confidential \ Recipients Only sub-label to display the Custom Permissions dialog and add AlexW (only) with Co-Author permissions. Save the document.
- [] Email the document to AlexW and AdeleV.
- [] On Client1, open the document as AlexW and verify that you have all rights except the ability to remove permissions.
- [] On Client2, attempt to open the document as AdeleV and observe that you are unable to view the document (this is the expected behavior). 

### Testing Global Policy

In this task, we will create a document and send an email to demonstrate the functionality defined in the first exercise.

- [] Switch to Client3 and make sure you are logged into Office as the user BenW.
- [] In Microsoft Outlook, create a new email.
- [] Send an email to AlexW, AdeleV, and your personal email address.
- [] In the Sensitivity Toolbar, click on the Confidential \ All Employees sub-label and send the email.
- [] On Client1 and Client2 observe that you are able to open the email natively in the client.
- [] In your personal email note that you will be unable to open this email (we will show you how to prevent this poor user experience using Exchange Mail Flow Rules in Exercise 4).

### Testing Scoped Policy

In this task, we will create a document and send an email from one of the users in the Legal group to demonstrate the functionality defined in the first exercise. We will also show the behavior of the No Default Label policy on documents.

- [] Switch to Client1 and make sure you are logged into Office as the user AlexW.
- [] In Microsoft Outlook, create a new email.
- [] Send an email to BenW and AdeleV.
- [] In the Sensitivity Toolbar, click on the Highly Confidential \ Legal Only sub-label and send the email.
- [] On Client2 observe that you are able to open the email natively in the client as AdeleV.
- [] On Client3, observe that you are unable to open the email as BenW.

### Testing Recommended Classification

In this task, we will test the configured Recommended Condition we defined in Exercise 1.

- [] Switch to Client3 and make sure you are logged into Office as the user BenW.
- [] In Microsoft Word, create a new document and enter the phrase “my password is pass@word1” and save the document.
- [] Notice that you are prompted with a recommendation to change the classification to Confidential \ Contoso Internal.

### Testing Automatic Classification

In this task, we will test the configured Recommended Condition we defined in Exercise 1.

- [] Switch to Client3 and make sure you are logged into Office as the user BenW.
- [] In Microsoft Word, create a new document and enter the phrase “My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368” and save the document.

> [!NOTE] This card number is a fake number that was generated using the Credit Card Generator for Testing at https://developer.paypal.com/developer/creditCardGenerator/.  The Microsoft Classification Engine uses the Luhn Algorithm to prevent false positives so when testing, please make sure to use valid numbers.

- [] Notice that the document is automatically classified as Confidential \ All Employees.

## Exercise 4: Exchange Online IRM Capabilities

Exchange Online can work in conjunction with Azure Information Protection (via Azure RMS protection) to provide advanced capabilities for protecting sensitive data flowing out of your network over email.  In this exercise, we will configure a mail flow rule to detect credit card information leaving the network in the clear.  Blocking Internal only labels.

## Exercise 5: SharePoint IRM Configuration

In this exercise, you will configure SharePoint Online Information Rights Management (IRM) and configure a document library with an IRM policy to protect documents that are downloaded from that library.

## Exercise 6: Azure Information Protection Scanner

The Azure Information Protection Scanner allows you to discover and optionally classify and protect sensitive information store in on-premises file shares and SharePoint repositories.  In this exercise, you will install, configure and test the AIP Scanner.

> [!NOTE] This exercise is based on the preview version of the AIP scanner (1.36.18.0) because some of the PowerShell commands have been updated and will be in the next GA version. The AIP client has been installed on SQL1 to save time but is available at https://aka.ms/AIPClient.

### Azure AD Connect Configuration

To authenticate to the AIP Service, the AIP Scanner service account must exist in on-premises Active Directory and in Azure Active Directory.  To accomplish this, you must install Azure AD Connect and configure it using the express settings.

- [] Log into SQL1 using the username Contoso\AIPScanner and the password Somepass1
- [] On the desktop, double-click on AzureADConnect.msi
- [] When prompted, click Run to continue
- [] On the Welcome page, check the box next to I agree and click the Continue button
- [] On the Express Settings page, click Use express settings
- [] On the Connect to Azure AD page, enter the username and password provided by the lab environment and press the Next button

> [!NOTE] The username should be something like admin@LODSEMS######.onmicrosoft.com

> [!NOTE] The wizard will connect to the Microsoft Online tenant to verify the credentials

- [] On the Connect to AD DS page, enter Contoso\Install in the username field and Somepass1 in the password field, then click the Next button
- [] On the Azure AD sign-in page, check the box next to Continue without any verified domains and click the Next button

> [!NOTE] Verified domains are primarily for SSO purposes and are not needed for this lab

- [] On the Configure page, click the Install button

> [!ALERT] WARNING: Do not uncheck the box for initial synchronization

- [] Continue to the next task leaving the Azure AD Connect sync process running

### Installing the AIP Scanner Service

The first step in configuring the AIP Scanner is to install the service and connect the database.  This is done with the Install-AIPScanner cmdlet that is provided by the AIP Client software.  The AIPScanner service account has been pre-staged in Active Directory for convenience.

- [] Click on the Machines tab in the lab interface and select SQL1
- [] Log into SQL1 using the username **Contoso\Install** and the password **Somepass1**
- [] Right-click on the Windows   button in the lower left-hand corner and click on Command Prompt (Admin)
- [] Type PowerShell and hit Enter
- [] At the PowerShell prompt, type the following command and press ENTER:

```Install-AIPScanner```

- [] When prompted, provide the credentials for the AIP scanner service account (Contoso\AIPScanner) and password (Somepass1)
- [] When prompted for SqlServerInstance, enter SQL1 and press Enter

> [!NOTE] You should see a success message like the one below

- [] Right-click on the Windows button in the lower left-hand corner and click on Run
- [] In the Run dialog, type Services.msc and click OK
- [] In the Services console, double-click on the Azure Information Protection Scanner service
- [] On the Log On tab of the Azure Information Protection Scanner Service Properties, verify that Log on as: is set to the Contoso\AIPScanner service account

### Creating Azure AD Applications for the AIP Scanner

Now that you have installed the scanner, you need to get an Azure AD token for the scanner service account to authenticate so that it can run unattended.

- [] Open Internet Explorer and browse to https://portal.azure.com 
- [] At the Sign in to Microsoft Azure page, enter the provided tenant credentials
- [] In the Microsoft Azure portal, click on Azure Active Directory in the left-hand pane
- [] Under Manage, click on App registrations
- [] In the App registrations blade, click the + New application registration button
- [] In the Create blade, use the values in the table below to create the registration

| Setting | Value |
|:--------|:------|
| Name | AIPOnBehalfOf |
|Application type | Web app / API |
|Sign-on URL | http://localhost |

- [] Click the Create button to complete the app registration
- [] Select the AIPOnBehalfOf application
- [] In the AIPOnBehalfOf blade, hover the mouse over the Application ID and click on the Click to copy icon when it appears
- [] Minimize (DO NOT CLOSE) Internet Explorer and other windows to show the desktop
- [] On the desktop, open the Scripts directory and open Set-AIPAuthentication.txt
- [] Replace <ID of the "Web app / API" application> with the copied Application ID value

> [!ALERT] WARNING: Ensure there is only a single space after the Application ID before -webAppKey

- [] Return to the browser and click on the Settings button
- []  In the Settings blade, under API ACCESS, click on Keys
- [] In the Keys blade, add a new key by typing AIPClient in the Key description field and your choice of duration (1 year, 2 years, or never expires)
- [] Select Save and copy the Value that is displayed

> [!ALERT] WARNING: Do not dismiss this screen until you have saved the value as you cannot retrieve it later

- [] Go back to the txt document and replace <key value generated in the "Web app / API" application> with the copied key value.

> [!ALERT] WARNING: Ensure there is only a single space after the Application Key before -nativeAppId

- [] Repeat steps 5-9 to create a Native Application using the values in the table below

| Setting | Value |
|:--------|:------|
Name|AIPClient
Application type|Native Application
Sign-on URL|http://localhost

- [] Replace <ID of the "Native" application > in the txt document with the copied Application ID value
- [] Return to the browser and in the AIPClient blade, click on Settings
- [] In the Settings blade, under API ACCESS, select Required permissions
- [] On the Required permissions blade, click Add, and then click Select an API
- [] In the search box, type AIPO and click on AIPOnBehalfOf, and then click the Select button
- [] On the Enable Access blade, check the box next to AIPOnBehalfOf, click the Select button 
- [] Click Done
- [] Return to the PowerShell window and paste the completed command from Set-AIPAuthentication.txt 

> [!ALERT] WARNING: While this does not affect this lab because we are logged into the system as the AIP Scanner service account, if you are installing this in production and you are logged in with a different account, you must make sure to run this command using the credentials of the AIP Scanner service account or the policy will be placed in the local store rather than the AIP Scanner’s policy store.

- [] When prompted, enter the user AIPScanner@tenantname.onmicrosoft.com and a password of Somepass1

> [!NOTE] Replace tenantname with the provided tenant
 
- [] You should see a prompt like the one below. Click Accept
- [] You will see the message below in the PowerShell window once complete

### Configuring Repositories

### Configuring AIP Scanner Policy

Because we have configured a default label for our Global policy, we will need to create a scoped policy for the AIP scanner to prevent all of the scanned files that do not match a rule from being labeled as General.  Since we already covered creation of a scoped policy in Exercise 1, this will be a brief section without screenshots.

### Running Full Discovery Scan

### Configuring Automatic Conditions

### Enforcing Configured Rules



## Appendix A: Tenant Setup

### Task 1

- [] Login to your VM or workstation.
- [] Create Microsoft Online Services Tenant - which includes Azure Information Protection services, the easiest way to do this is by subscribing to an EMS E5 trial.
o	Open IE in InPrivate mode (right click on IE and choose InPrivate) and Browse to https://www.microsoft.com/en-us/cloud-platform/enterprise-mobility-security-trial 
 
o	Fill the information below < This does not have to be Valid as we are not going to use any of this information in the course of this lab > and click “Just one more step”. 


 


o	Create your new user account that will be also the Azure service administrator and click “Create my account” (an Azure tenant will be created automatically based on the information provided) 



 


o	You will be asked to verify that you are not a robot either by Text or Phone Call


 


o	After the code verification, your tenant request in now processed and once finished (couple of minutes) click on “You’re ready to go…”
	Write down the user ID, this user is both your Azure service admin and your end-user.
 

o	At this step, Open a new Tab in the IE and Navigate to https://portal.office.com 
o	You should automatically be signed in with your newly created ID, if not Sign in with your newly created credentials for the E5 Subscription
o	Once on the portal, go to the subscriptions node.
 

o	You will see the following subscription assigned and active, Click on Add Subscription
 
o	On the following screen, select Office 365 E5 Trial Plan.
 
o	On the Next Screen Confirm your order by clicking on Try Now. r 
o	Click on Continue onto the Next Page
o	Confirm your Subscriptions by going into the billing node and making sure you see both 
 
o	Create a New user from the User Node and Assign Licenses.

 

o	Assign the same Licenses to your existing user.
Task 2 – Add EMS licenses
Please Create a user named Adele Vance and assign licenses as described below:

1)	From the Office 365 Admin Portal, chose the “Users” option and choose “Add a User”  
2)	Name:  Adele Vance
3)	Username:  AdeleV
4)	Password:  <any password you can remember>
5)	Follow the steps below to assign licenses to the AdeleV User

The following procedure will assign some EMS license options to “Adele Vance”.
1)	Go to the first tab, the Office Portal, in Edge.
2)	From the choice of applications choose the “Admin” app.
3)	Choose the Users option
 
4)	Click on Adele Vance in the list of users to pull up the user’s properties.
 
5)	Once the user properties populate edit the product licenses.
 
6)	Expand the Enterprise + Security (EMS) E5 section.
 
7)	Assign all the EMS licenses
 
8)	Apply the licenses.
Ensure Office 365 E5 licenses are also applied, and repeat for the Admin user.
 
Appendix B: Lab Environment Setup
