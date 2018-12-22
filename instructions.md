# Configuring Microsoft Information Protection Solutions to Protect Your Sensitive Data

### Introduction

Estimated time to complete this lab

60 minutes

### Objectives

After completing this lab, you will be able to:

- Configure the Azure Information Protection scanner to discover sensitive data 
- Configure Azure Information Protection labels
- Configure Azure Information Protection policies
- Classify and protect content with Azure Information Protection in Office applications
- Classify and Protect sensitive data discovered by the AIP Scanner
- Configure Exchange Online Mail Flow Rules for AIP (Optional)

### Prerequisites

Before working on this lab, you must have:

- Familiarity using Windows 10
- Familiarity with PowerShell
- Familiarity with Office 2016 applications

### Lab machine technology

This lab is designed to be completed on either a native Windows 10 machine or a VM with the following characteristics:

- Windows 10 Enterprise
- Office 365 ProPlus
- Azure Information Protection client (1.41.51.0)

Microsoft 365 E5 Tenant credentials will be provided during the event.  If you want to run through this lab after the event, you may use a tenant created through https://demos.microsoft.com or your own Microsoft 365 Tenant. This Lab Guide will be publicly available after the event at https://aka.ms/AIPHOL.

===
# Tips and Tricks
[🔙](#introduction)

There are a few extras throughout this lab that are designed to make your lab experience a smooth and simple process.  Below are some icons you should watch out for that will save you time during each task.

## Interactive Elements

- Each task contains a series of steps required for successful completion of the lab.  To track your progress throughout the lab, check the box to the left of the numbered series.  

	!IMAGE[6mfi1ekm.jpg](\Media\6mfi1ekm.jpg)
- After you check each box, you will see your lab progress at the bottom of the instruction pane.

	!IMAGE[0ggu265u.jpg](\Media\0ggu265u.jpg)
- When you see an instruction for switching computers, click on the **blue link** in the text to have that VM loaded automatically.

	!IMAGE[12i85vgl.jpg](\Media\12i85vgl.jpg)
- Throughout the lab, you will see text with a letter **T** in a square to the left.  This indicates that you can **click on the text** and it will **type it for you** in the VM.  **This will save you lots of time**.

	!IMAGE[cnyu1tdi.jpg](\Media\cnyu1tdi.jpg)
- Additionally you will a @lab.CtrlAltDelete button that will press **Ctrl-Alt-Delete** in the active VM.

- The last interactive element you will see throughout the lab is the **Open Screenshot** text below many steps.  To reduce clutter, most screenshots have been configured to launch in a popup window.  The only ones left visible are ones that could cause issues if they are missed or if there are multiple elements that are easier to understand with visual representation.

	!IMAGE[n4cqn070.jpg](\Media\n4cqn070.jpg)

## Additional Information

There are also Knowledge Items, Notes, and Hints throughout the lab.

- Knowledge Items are used to provide additional information about a topic related to the task or step.  These are often collapsed to reduce the amount of space they use, but it is recommended that you review these items if you want more information on the subject.

	!IMAGE[8g9nif1j.jpg](\Media\8g9nif1j.jpg)
- Notes are steps that do not require action or modification of any elements.  This includes general observations and reviewing the results of previous steps.

	!IMAGE[kxrbzsr2.jpg](\Media\kxrbzsr2.jpg)
- Hints are recommendations or observations that help with the completion of a step.

	!IMAGE[w11x99oo.jpg](\Media\w11x99oo.jpg)

===
# Lab Environment Configuration
[🔙](#introduction)

There are a few prerequisites that need to be set up to complete all the sections in this lab.  This Exercise will walk you through the items below.

- [Azure AD User Configuration](#azure-ad-user-configuration)

- [Redeem Azure Pass](#redeem-azure-pass)

- [Workplace Join Clients](#workplace-join-clients)

===
# Azure AD User Configuration

In this task, we will create new Azure AD users and assign licenses via PowerShell.  In a procduction evironment this would be done using Azure AD Connect or a similar tool to maintain a single source of authority, but for lab purposes we are doing it via script to reduce setup time.

1. [] Log into @lab.VirtualMachine(Scanner01).SelectLink using the password +++@lab.VirtualMachine(Client01).Password+++
2. [] Open a new Administrative PowerShell window and click below to type the code. 
    
    ```
    # Get Global Admin Credentials
    $cred = Get-Credential
    ```

1. [] When prompted, provide the credentials below:

	```@lab.CloudCredential(17).Username```

	```@lab.CloudCredential(17).Password``` 
    
1. [] In the PowerShell window, click on the code below to create users and assign Office and EMS licenses.

    ```
    # Store Tenant FQDN and Short name
    $tenantfqdn = "@lab.CloudCredential(17).TenantName"
    $tenant = $tenantfqdn.Split('.')[0]

    # Build Licensing SKUs
    $office = $tenant+":ENTERPRISEPREMIUM"
    $ems = $tenant+":EMSPREMIUM"

    # Connect to MSOLService for licensing Operations
    Connect-MSOLService -Credential $cred

    # Remove existing licenses to ensure enough licenses exist for our users
    $LicensedUsers = Get-MsolUser -All  | where {$_.isLicensed -eq $true}
    $LicensedUsers | foreach {Set-MsolUserLicense -UserPrincipalName $_.UserPrincipalName -RemoveLicenses $office, $ems}

    # Connect to Azure AD using stored credentials to create users
    Connect-AzureAD -Credential $cred

    # Import Users from local csv file
    $users = Import-csv C:\users.csv

    foreach ($user in $users){
    	
    # Store UPN created from csv and tenant
    $upn = $user.username+"@"+$tenantfqdn

    # Create password profile preventing automatic password change and storing password from csv
    $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile 
    $PasswordProfile.ForceChangePasswordNextLogin = $false 
    $PasswordProfile.Password = $user.password

    # Create new Azure AD user
    New-AzureADUser -AccountEnabled $True -DisplayName $user.displayname -PasswordProfile $PasswordProfile -MailNickName $user.username -UserPrincipalName $upn
    }

    foreach ($user in $users){

    # Store UPN created from csv and tenant
    $upn = $user.username+"@"+$tenantfqdn

    # Assign Office and EMS licenses to users
    Set-MsolUser -UserPrincipalName $upn -UsageLocation US
    Set-MsolUserLicense -UserPrincipalName $upn -AddLicenses $office, $ems
    }

    # Assign Office and EMS licenses to Admin user
    $upn = "admin@"+$tenantfqdn
    Set-MsolUser -UserPrincipalName $upn -UsageLocation US
    Set-MsolUserLicense -UserPrincipalName $upn -AddLicenses $office, $ems

    ```


===
# Redeem Azure Pass

For several of the exercises in this lab series, you will require an active subscription.  We are providing an Azure Pass for this purpose.  You will be provided with an Azure Pass code to use with the instructions below.

## Redeeming a Microsoft Azure Pass Promo Code:

1. [] Log into @lab.VirtualMachine(Client01).SelectLink using the password +++Pa$$w0rd+++
2. [] Right-click on **Edge** in the taskbar and click on **New InPrivate window**.

3. [] In the InPrivate window, navigate to ```https://www.microsoftazurepass.com```

4. [] Click the **Start** button to get started.

	!IMAGE[wdir7lb3.jpg](\Media\wdir7lb3.jpg)
1. [] Enter the credentials below and select **Sign In**.

	```@lab.CloudCredential(17).Username```

	```@lab.CloudCredential(17).Password``` 

	!IMAGE[gtg8pvp1.jpg](\Media\gtg8pvp1.jpg)
1. [] Click **Confirm** if the correct email address is listed.

	!IMAGE[teyx280d.jpg](\Media\teyx280d.jpg)
1. [] In the Promo code box type ```@lab.CloudCredential(215).PromoCode``` and click the **Claim Promo Code** button.

	!IMAGE[e1l35ko2.jpg](\Media\e1l35ko2.jpg)
	> [!NOTE] It may take up to 5 minutes to process the redemption.

1. [] Scroll to the bottom of the page and click **Next**.

	!IMAGE[ihrjazqi.jpg](\Media\ihrjazqi.jpg)
	> [!NOTE] You can keep the pre-populated information.

1. [] Check the box to agree to the terms and click **Sign up**.

	!IMAGE[k2a97g8e.jpg](\Media\k2a97g8e.jpg)
	> [!NOTE] It may take a few minutes to process the request.

1. [] When you are redirected to the Azure Portal, the process is complete.
===
# Workplace Join Clients

In this task, we will join 3 systems to the Azure AD tenant to provide SSO capabilities in Office.

1. [] On @lab.VirtualMachine(Client01).SelectLink, right-click on the start menu and click **Run**.
1. [] In the Run dialog, type ```ms-settings:workplace``` and click **OK**.

	>!IMAGE[mssettings.png](\Media\mssettings.png)

1. [] In the Access Work or School settings menu, click on **+ Connect** and enter the credentials below to workplace join the client.

	```AdamS@@lab.CloudCredential(17).TenantName```

	```pass@word1```
1. [] Click **Done**.
1. [] Switch to @lab.VirtualMachine(Client02).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] Right-click on the start menu and click **Run**.
1. [] In the Run dialog, type ```ms-settings:workplace``` and click **OK**.

	>!IMAGE[mssettings.png](\Media\mssettings.png)

1. [] In the Access Work or School settings menu, click on **+ Connect** and enter the credentials below to workplace join the client.

	```AliceA@@lab.CloudCredential(17).TenantName```

	```pass@word1```
1. [] Click **Done**.
1. [] Switch to @lab.VirtualMachine(Client03).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] Right-click on the start menu and click **Run**.
1. [] In the Run dialog, type ```ms-settings:workplace``` and click **OK**.

	>!IMAGE[mssettings.png](\Media\mssettings.png)

1. [] In the Access Work or School settings menu, click on **+ Connect** and enter the credentials below to workplace join the client.

	```EvanG@@lab.CloudCredential(17).TenantName```

	```pass@word1```
1. [] Click **Done**.
===
# Azure Information Protection
[🔙](#introduction)
### Objectives

> [!ALERT] Please ensure you have completed the steps in the [Lab Environment Configuration](#lab-environment-configuration) before continuing.

There are 2 options for this Lab.  These options contain similar content except for the items called out below.

- The **New to AIP** option will walk through the label and policy creation including scoped policies and demonstrating recommended and automatic labeling in Office applications. This option takes significantly longer and so there is a chance that all sections may not be completed.

- The **Familiar with AIP** option assumes that you are familiar with label and policy creation and that you have seen the operation of conditions in Office applications as these will not be demonstrated.  This option will use the predefined labels and global policy populated in the demo tenants.

Click on one of the options below to begin.

## [New to AIP](#exercise-1-configuring-aip-scanner-for-discovery)

## [Familiar with AIP](#exercise-1a-configuring-aip-scanner-for-discovery)

After completing this lab, you will be able to:

- [Configure the Azure Information Protection scanner to discover sensitive data](#exercise-1-configuring-aip-scanner-for-discovery)
- [Configure Azure Information Protection labels](#creating-configuring-and-modifying-sub-labels)
- [Configure Azure Information Protection policies](#configuring-global-policy)
- [Activate Unified Labeling for the Security and Compliance Center](#exercise-3-security-and-compliance-center)
- [Classify and protect content with Azure Information Protection in Office applications](#exercise-4-testing-aip-policies)
- [Classify and protect sensitive data discovered by the AIP Scanner](#configuring-automatic-conditions)
- [Configure Exchange Online Mail Flow Rules for AIP](#configuring-exchange-online-mail-flow-rules)

===

# Exercise 1: Configuring AIP Scanner for Discovery
[🔙](#azure-information-protection)

Even before configuring an AIP classification taxonomy, customers can scan and identify files containing sensitive information based on the built-in sensitive information types included in the Microsoft Classification Engine.  

!IMAGE[ahwj80dw.jpg](\Media\ahwj80dw.jpg)

Often, this can help drive an appropriate level of urgency and attention to the risk customers face if they delay rolling out AIP classification and protection.  

In this exercise, we will install the AIP scanner and run it against repositories in discovery mode.  Later in this lab (after configuring labels and conditions) we will revisit the scanner to perform automated classification, labeling, and protection of sensitive documents.

===
# Configuring Azure Log Analytics
[🔙](#azure-information-protection)

In order to collect log data from Azure Information Protection clients and services, you must first configure the log analytics workspace.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] In the InPrivate window, navigate to ```https://portal.azure.com/```
	>
	>^IMAGE[Open Screenshot](\Media\cznh7i2b.jpg)

	> [!KNOWLEDGE] If necessary, log in using the username and password below:
	>
	>```@lab.CloudCredential(17).Username``` 
	>
	>```@lab.CloudCredential(17).Password```
	
1. [] After logging into the portal, type the word ```info``` into the **search bar** and press **Enter**, then click on **Azure Information Protection**. 

	!IMAGE[2598c48n.jpg](\Media\2598c48n.jpg)
	
	> [!HINT] If you do not see the search bar at the top of the portal, click on the **Magnifying Glass** icon to expand it.
	>
	> !IMAGE[ny3fd3da.jpg](\Media\ny3fd3da.jpg)

1. [] In the Azure Information Protection blade, under **Manage**, click **Configure analytics (preview)**.

1. [] Next, click on **+ Create new workspace**.

	!IMAGE[qu68gqfd.jpg](\Media\qu68gqfd.jpg)
1. [] In the Log analytics workspace using the values in the table below and click **OK**.

	|||
	|-----|-----|
	|OMS Workspace|**Unique Workspace Name**|
	|Resource Group|```AIP-RG```|
	|Location|**East US**|

	^IMAGE[Open Screenshot](\Media\5butui15.jpg)
1. [] Next, back in the Configure analytics (preview) blade, **check the box** next to the workspace and click **OK**.

	!IMAGE[gste52sy.jpg](\Media\gste52sy.jpg)
1. [] Click **Yes**, in the confirmation dialog.

	!IMAGE[zgvmm4el.jpg](\Media\zgvmm4el.jpg)
===
# AIP Scanner Setup
In this task we will install the AIP scanner binaries and create the Azure AD Applications necessary for authentication.
[🔙](#azure-information-protection)

## Installing the AIP Scanner Service

The first step in configuring the AIP Scanner is to install the service and connect the database.  This is done with the Install-AIPScanner cmdlet that is provided by the AIP Client software.  The AIPScanner service account has been pre-staged in Active Directory for convenience.

1. [] Switch to @lab.VirtualMachine(Scanner01).SelectLink and use the password +++@lab.VirtualMachine(Client01).Password+++.

1. [] Right-click on the **PowerShell** icon in the taskbar and click on **Run as Administrator**.

	!IMAGE[7to6p334.jpg](\Media\7to6p334.jpg)

1. [] At the PowerShell prompt, click to type the code below 
   
   ```
   $SQL = "Scanner01"
   Install-AIPScanner -SQLServerInstance $SQL
   
   ```
3. [] When prompted, provide the credentials for the AIP scanner service account.
	
	```Contoso\AIPScanner```

	```Somepass1```

	^IMAGE[Open Screenshot](\Media\pc9myg9x.jpg)

	> [!knowledge] You should see a success message like the one below. 
	>
	>!IMAGE[w7goqgop.jpg](\Media\w7goqgop.jpg)
	>

## Creating Azure AD Applications for the AIP Scanner

Now that you have installed the scanner bits, you need to get an Azure AD token for the scanner service account to authenticate so that it can run unattended. This requires registering both a Web app and a Native app in Azure Active Directory.  The commands below will do this in an automated fashion rather than needing to go into the Azure portal directly.

1. [] In PowerShell, run ```Connect-AzureAD``` and use the username and password below. 
	
	```@lab.CloudCredential(17).Username```
	
	```@lab.CloudCredential(17).Password```
1. [] Next, click the **T** to **type the commands below** in the PowerShell window and press **Enter**. 

	> [!NOTE] This will create a new Web App Registration, Native App Registration, and associated Service Principals in Azure AD.

   ```
   New-AzureADApplication -DisplayName AIPOnBehalfOf -ReplyUrls http://localhost
   $WebApp = Get-AzureADApplication -Filter "DisplayName eq 'AIPOnBehalfOf'"
   New-AzureADServicePrincipal -AppId $WebApp.AppId
   $WebAppKey = New-Guid
   $Date = Get-Date
   New-AzureADApplicationPasswordCredential -ObjectId $WebApp.ObjectID -startDate $Date -endDate $Date.AddYears(1) -Value $WebAppKey.Guid -CustomKeyIdentifier "AIPClient"
	
   $AIPServicePrincipal = Get-AzureADServicePrincipal -All $true | ? {$_.DisplayName -eq 'AIPOnBehalfOf'}
   $AIPPermissions = $AIPServicePrincipal | select -expand Oauth2Permissions
   $Scope = New-Object -TypeName "Microsoft.Open.AzureAD.Model.ResourceAccess" -ArgumentList $AIPPermissions.Id,"Scope"
   $Access = New-Object -TypeName "Microsoft.Open.AzureAD.Model.RequiredResourceAccess"
   $Access.ResourceAppId = $WebApp.AppId
   $Access.ResourceAccess = $Scope


   New-AzureADApplication -DisplayName AIPClient -ReplyURLs http://localhost -RequiredResourceAccess $Access -PublicClient $true
   $NativeApp = Get-AzureADApplication -Filter "DisplayName eq 'AIPClient'"
   New-AzureADServicePrincipal -AppId $NativeApp.AppId
   ```
   
1. [] Finally, we will output the Set-AIPAuthentication command by running the commands below and pressing **Enter**.
   
	 
   ```
   "Set-AIPAuthentication -WebAppID " + $WebApp.AppId + " -WebAppKey " + $WebAppKey.Guid + " -NativeAppID " + $NativeApp.AppId | Out-File ~\Desktop\Set-AIPAuthentication.txt

	Start ~\Desktop\Set-AIPAuthentication.txt
	```
1. [] In the new notepad window, copy the command to the clipboard.
1. [] Click on the Start menu and type ```PowerShell```, right-click on the PowerShell program, and click **Run as a different user**.

	!IMAGE[zgt5ikxl.jpg](\Media\zgt5ikxl.jpg)

1. [] When prompted, enter the username and password below and click **OK**.

	```Contoso\AIPScanner``` 

	```Somepass1```

1. [] Paste the copied **Set-AIPAuthentication** command into this window and run it.
1. [] When prompted, enter the username and password below:

	```AIPScanner@@lab.CloudCredential(17).TenantName```

	```Somepass1```

	^IMAGE[Open Screenshot](\Media\qfxn64vb.jpg)

1. [] In the Permissions requested window, click **Accept**.

   !IMAGE[nucv27wb.jpg](\Media\nucv27wb.jpg)
   
	>[!knowledge] You will a message like the one below in the PowerShell window once complete.
	>
	>!IMAGE[y2bgsabe.jpg](\Media\y2bgsabe.jpg)
1. [] **Close the current PowerShell window**.
1. [] **In the admin PowerShell window** and type the command below.

	```Restart-Service AIPScanner```
   
===

# Configuring Repositories
[🔙](#azure-information-protection)

In this task, we will configure repositories to be scanned by the AIP scanner.  As previously mentioned, these can be any type of CIFS file shares including NAS devices sharing over the CIFS protocol.  Additionally, On premises SharePoint 2010, 2013, and 2016 document libraries and lists (attachements) can be scanned.  You can even scan entire SharePoint sites by providing the root URL of the site.  There are several optional 

> [!NOTE] SharePoint 2010 is only supported for customers who have extended support for that version of SharePoint.

The next task is to configure repositories to scan.  These can be on-premises SharePoint 2010, 2013, or 2016 document libraries and any accessible CIFS based share.

1. [] In the PowerShell window on Scanner01 run the commands below

    ```
    Add-AIPScannerRepository -Path http://Scanner01/documents -SetDefaultLabel Off
	```
	```
	Add-AIPScannerRepository -Path \\Scanner01\documents -SetDefaultLabel Off
    ```
	>[!Knowledge] Notice that we added the **-SetDefaultLabel Off** switch to each of these repositories.  This is necessary because our Global policy has a Default label of **General**.  If we did not add this switch, any file that did not match a condition would be labeled as General when we do the enforced scan.

	^IMAGE[Open Screenshot](\Media\00niixfd.jpg)
1. [] To verify the repositories configured, run the command below.
	
    ```
    Get-AIPScannerRepository
    ```
	^IMAGE[Open Screenshot](\Media\n5hj5e7j.jpg)

===

# Running Sensitive Data Discovery
[🔙](#azure-information-protection)

1. [] Run the commands below to run a discovery cycle.

    ```
	Set-AIPScannerConfiguration -DiscoverInformationTypes All -Enforce Off
	```
	```
	Start-AIPScan
    ```

	> [!Knowledge] Note that we used the DiscoverInformationTypes -All switch before starting the scan.  This causes the scanner to use any custom conditions that you have specified for labels in the Azure Information Protection policy, and the list of information types that are available to specify for labels in the Azure Information Protection policy.  Although the scanner will discover documents to classify, it will not do so because the default configuration for the scanner is Discover only mode.
 	
1. [] Right-click on the **Windows** button in the lower left-hand corner and click on **Event Viewer**.
 
	^IMAGE[Open Screenshot](\Media\cjvmhaf0.jpg)
1. [] Expand **Application and Services Logs** and click on **Azure Information Protection**.

	^IMAGE[Open Screenshot](\Media\dy6mnnpv.jpg)
 
	>[!NOTE] You will see an event like the one below when the scanner completes the cycle.
	>
	>!IMAGE[agnx2gws.jpg](\Media\agnx2gws.jpg)
 
1. [] Next, switch to @lab.VirtualMachine(Client01).SelectLink, open a **File Explorer** window, and browse to ```\\Scanner01.contoso.azure\c$\users\aipscanner\AppData\Local\Microsoft\MSIP\Scanner\Reports```.

	> If needed, use the credentials below:
	>
	>```Contoso\LabUser```
	>
	>```Pa$$w0rd```

1. [] Review the summary txt and detailed csv files available there.  

	>[!Hint] Since there are no Automatic conditions configured yet, the scanner found no matches for the 141 files scanned despite 136 of them having sensitive data.
	>
	>!IMAGE[aukjn7zr.jpg](\Media\aukjn7zr.jpg)
	>
	>The details contained in the DetailedReport.csv can be used to identify the types of sensitive data you need to create AIP rules for in the Azure Portal.
	>
	>!IMAGE[9y52ab7u.jpg](\Media\9y52ab7u.jpg)

	>[!NOTE] We will revisit this information later in the lab to review discovered data and create Sensitive Data Type to Classification mappings.

===

# Exercise 2: Configuring Azure Information Protection Policy
[🔙](#azure-information-protection)

This exercise demonstrates using the Azure Information Protection blade in the Azure portal to configure policies and sub-labels.  We will create a new sub-label and configure protection and then modify an existing sub-label.  We will also create a label that will be scoped to a specific group.  

Next, we will configure AIP Global Policy to use the General sub-label as default, and finally, we will configure a scoped policy to use the new scoped label by default for Word, Excel, and PowerPoint while still using General as default for Outlook.
===
# Creating, Configuring, and Modifying Sub-Labels
[🔙](#azure-information-protection)

In this task, we will configure a label protected for internal audiences that can be used to help secure sensitive data within your company.  By limiting the audience of a specific label to only internal employees, you can dramatically reduce the risk of unintentional disclosure of sensitive data and help reduce the risk of successful data exfiltration by bad actors.  

However, there are times when external collaboration is required, so we will configure a label to match the name and functionality of the Do Not Forward button in Outlook.  This will allow users to more securely share sensitive information outside the company to any recipient.  By using the name Do Not Forward, the functionality will also be familiar to what previous users of AD RMS or Azure RMS may have used in the past.

1. [] On @lab.VirtualMachine(Client01).SelectLink, in the Azure Information Protection blade, under **Classifications** in the left pane, click on **Labels** to load the Azure Information Protection – Labels blade.

	^IMAGE[Open Screenshot](\Media\mhocvtih.jpg)

1. [] In the Azure Information Protection – Labels blade, right-click on **Confidential** and click **Add a sub-label**.

	^IMAGE[Open Screenshot](\Media\uktfuwuk.jpg)

1. [] In the Sub-label blade, type ```Contoso Internal``` for the **Label display name** and for **Description** enter text similar to ```Confidential data that requires protection, which allows Contoso Internal employees full permissions. Data owners can track and revoke content.```

	^IMAGE[Open Screenshot](\Media\4luorc0u.jpg)

1. [] Then, under **Set permissions for documents and emails containing this label**, click **Protect**, and under **Protection**, click on **Azure (cloud key)**.

	^IMAGE[Open Screenshot](\Media\tp97a19d.jpg)

1. [] In the Protection blade, click **+ Add Permissions**.

	^IMAGE[Open Screenshot](\Media\layb2pvo.jpg)

1. [] In the Add permissions blade, click on **+ Add contoso – All members** and click **OK**.

	^IMAGE[Open Screenshot](\Media\zc0iuoyz.jpg)

1. [] In the Protection blade, click **OK**.

	^IMAGE[Open Screenshot](\Media\u8jv46zo.jpg)

1. [] In the Sub-label blade, scroll down to the **Set visual marking (such as header or footer)** section and under **Documents with this label have a header**, click **On**.

	> Use the values in the table below to configure the Header.

	| Setting          | Value            |
	|:-----------------|:-----------------|
	| Header text      | ```Contoso Internal``` |
	| Header font size | ```24```               |
	| Header color     | Purple           |
	| Header alignment | Center           |

	> [!NOTE] These are sample values to demonstrate marking possibilities and **NOT** a best practice.

	^IMAGE[Open Screenshot](\Media\0vdoc6qb.jpg)

1. [] To complete creation of the new sub-label, click the **Save** button and then click **OK** in the Save settings dialog.

	^IMAGE[Open Screenshot](\Media\89nk9deu.jpg)

1. [] In the Azure Information Protection - Labels blade, expand **Confidential** (if necessary) and then click on **Recipients Only**.

	^IMAGE[Open Screenshot](\Media\eiiw5zbg.jpg)

1. [] In the Label: Recipients Only blade, change the **Label display name** from **Recipients Only** to ```Do Not Forward```.

	^IMAGE[Open Screenshot](\Media\v54vd4fq.jpg)

1. [] Next, in the **Set permissions for documents and emails containing this label** section, under **Protection**, click **Azure (cloud key): User defined**.

	^IMAGE[Open Screenshot](\Media\qwyranz0.jpg)

1. [] In the Protection blade, under **Set user-defined permissions (Preview)**, verify that only the box next to **In Outlook apply Do Not Forward** is checked, then click **OK**.

	^IMAGE[Open Screenshot](\Media\16.png)

	> [!knowledge] Although there is no action added during this step, it is included to show that this label will only display in Outlook and not in Word, Excel, PowerPoint or File Explorer.

1. [] Click **Save** in the Label: Recipients Only blade and **OK** to the Save settings prompt. 

	^IMAGE[Open Screenshot](\Media\9spkl24i.jpg)

1. []  Click the **X** in the upper right corner of the blade to close.

	^IMAGE[Open Screenshot](\Media\98pvhwdv.jpg)

===

# Configuring Global Policy
[🔙](#azure-information-protection)

In this task, we will assign the new sub-label to the Global policy and configure several global policy settings that will increase Azure Information Protection adoption among your users and reduce ambiguity in the user interface.

1. [] In the Azure Information Protection blade, under **Classifications** on the left, click **Policies** then click the **Global** policy.

	^IMAGE[Open Screenshot](\Media\24qjajs5.jpg)

1. [] In the Policy: Global blade, **wait for the labels to load**.

1. [] Below the labels, click **Add or remove labels**.

1. [] In the Policy: Add or remove labels blade, ensure that the boxes next to all Labels are checked and click **OK**.

1. [] In the Policy: Global blade, under the **Configure settings to display and apply on Information Protection end users** section, configure the policy to match the settings shown in the table and image below.

	| Setting | Value |
	|:--------|:------|
	| Select the default label | General |
	|All documents and emails must have a label…|On
	Users must provide justification to set a lower…|On
	For email messages with attachments, apply a label…|Automatic
	Add the Do Not Forward button to the Outlook ribbon|Off

	!IMAGE[Open Screenshot](\Media\mtqhe3sj.jpg)

1. [] Click **Save**, then **OK** to complete configuration of the Global policy.

	^IMAGE[Open Screenshot](\Media\1p1q4pxe.jpg)

1. [] Click the **X** in the upper right corner to close the Policy: Global blade.

	^IMAGE[Open Screenshot](\Media\m6e4r2u2.jpg)

===

# Creating a Scoped Label and Policy
[🔙](#azure-information-protection)

Now that you have learned how to work with global labels and policies, we will create a new scoped label and policy for the Legal team at Contoso.  

1. [] Under **Classifications** on the left, click **Labels**.

	^IMAGE[Open Screenshot](\Media\50joijwb.jpg)

1. [] In the Azure Information Protection – Labels blade, right-click on **Highly-Confidential** and click **Add a sub-label**.

	^IMAGE[Open Screenshot](\Media\tasz9t0i.jpg)

1. [] In the Sub-label blade, enter ```Legal Only``` for the **Label display name** and for **Description** enter ```Data is classified and protected. Legal department staff can edit, forward and unprotect.```.

	^IMAGE[Open Screenshot](\Media\lpvruk49.jpg)

1. [] Then, under **Set permissions for documents and emails containing this label**, click **Protect** and under **Protection**, click **Azure (cloud key)**.

	^IMAGE[Open Screenshot](\Media\6ood4jqu.jpg)

1. [] In the Protection blade, under **Protection settings**, click the **+ Add permissions** link.

	!IMAGE[ozzumi7l.jpg](\Media\ozzumi7l.jpg)

1. [] In the Add permissions blade, click **+ Browse directory**.

	^IMAGE[Open Screenshot](\Media\2lvwim24.jpg)

1. [] In the AAD Users and Groups blade, **wait for the names to load**, then check the boxes next to **Adam Smith** and **Alice Anderson**, and click the **Select** button.

	^IMAGE[Open Screenshot](\Media\uishk9yh.jpg)

	> [!Note] In a production environment, you will typically use a synced or Azure AD Group rather than choosing individuals.

1. [] In the Add permissions blade, click **OK**.

	^IMAGE[Open Screenshot](\Media\stvnaf4f.jpg)

1. [] In the Protection blade, under **Allow offline access**, reduce the **Number of days the content is available without an Internet connection** value to ```3``` and press **OK** .

	> [!Knowledge] This value determines how many days a user will have offline access from the time a document is opened, and an initial Use License is acquired.  While this provides convenience for users, it is recommended that this value be set appropriately based on the sensitivity of the content.

	^IMAGE[Open Screenshot](\Media\j8masv1q.jpg)

1. [] Click **Save** in the Sub-label blade and **OK** to the Save settings prompt to complete the creation of the Legal Only sub-label.

	^IMAGE[Open Screenshot](\Media\dfhoii1x.jpg)

1. [] In the Azure Information Protection blade, under **Classifications** on the left, click **Policies** then click the **+Add a new policy** link.

	^IMAGE[Open Screenshot](\Media\ospsddz6.jpg)

1. [] In the Policy blade, for Policy name, type ```No Default Label Scoped Policy``` and click on **Select which users or groups get this policy. Groups must be email-enabled.**

	!IMAGE[1sjw3mc7.jpg](\Media\1sjw3mc7.jpg)

1. [] In the AAD Users and Groups blade, click on **Users/Groups**.  
1. [] Then in the second AAD Users and Groups blade, **wait for the names to load** and check the boxes next to **AIPScanner**, **Adam Smith**, and **Alice Anderson**.

	>[!NOTE] The **AIPScanner** account is added here to prevent all scanned documents from being labeled with a default label.
1. [] Click the **Select** button.
1. [] Finally, click **OK**.

	^IMAGE[Open Screenshot](\Media\onne7won.jpg)

1. [] In the Policy blade, under the labels, click on **Add or remove labels** to add the scoped label.

	!IMAGE[b6e9nbui.jpg](\Media\b6e9nbui.jpg)

1. [] In the Policy: Add or remove labels blade, check the box next to **Legal Only** and click **OK**.

	^IMAGE[Open Screenshot](\Media\c2429kv9.jpg)

1. [] In the Policy blade, under **Configure settings to display and apply on Information Protection end users** section, under **Select the default label**, select **None** as the default label for this scoped policy.

	!IMAGE[4mxceage.jpg](\Media\4mxceage.jpg)

1. [] Click **Save**, then **OK** to complete creation of the No Default Label Scoped Policy.

	^IMAGE[Open Screenshot](\Media\41jembjf.jpg)

1. [] Click on the **X** in the upper right-hand corner to close the policy.

===

# Configuring Advanced Policy Settings
[🔙](#azure-information-protection)

There are many advanced policy settings that are useful to tailor your Azure Information Protection deployment to the needs of your environment.  In this task, we will cover one of the settings that is very complimentary when using scoped policies that have no default label or a protected default label.  Because the No Default Label Scoped Policy we created in the previous task uses a protected default label, we will be adding an alternate default label for Outlook to provide a more palatable user experience for those users.

1. [] In the Azure Information Protection blade, under **Classifications** on the left, click on **Labels** and then click on the **General** label.

    ^IMAGE[Open Screenshot](\Media\rvn4xorx.jpg)

1. [] In the Label: General blade, scroll to the bottom and copy the **Label ID** and close the blade using the **X** in the upper right-hand corner.

    !IMAGE[8fi1wr4d.jpg](\Media\8fi1wr4d.jpg)

1. [] In the AIP Portal, under **Classifications** on the left, click on **Policies**. 
1. [] **Right-click** on the **No Default Label Scoped Policy** and click on **Advanced settings**.

    ^IMAGE[Open Screenshot](\Media\2jo71ugb.jpg)

1. [] In the Advanced settings blade, in the textbox under **NAME**, type ```OutlookDefaultLabel```.  In the textbox under **VALUE**, paste the **Label ID** for the **General** label you copied previously, then click **Save and close**.

    > [!ALERT] CAUTION: Please check to ensure that there are **no spaces** before or after the **Label ID** when pasting as this will cause the setting to not apply.

    !IMAGE[ezt8sfs3.jpg](\Media\ezt8sfs3.jpg)

	> [!HINT] This and additional Advanced Policy Settings can be found at [https://docs.microsoft.com/en-us/azure/information-protection/rms-client/client-admin-guide-customizations ](https://docs.microsoft.com/en-us/azure/information-protection/rms-client/client-admin-guide-customizations)

===

# Defining Recommended and Automatic Conditions
[🔙](#azure-information-protection)

One of the most powerful features of Azure Information Protection is the ability to guide your users in making sound decisions around safeguarding sensitive data.  This can be achieved in many ways through user education or reactive events such as blocking emails containing sensitive data. 

However, helping your users to properly classify and protect sensitive data at the time of creation is a more organic user experience that will achieve better results long term.  In this task, we will define some basic recommended and automatic conditions that will trigger based on certain types of sensitive data.

1. [] Under **Dashboards** on the left, click on **Data discovery (Preview)** to view the results of the discovery scan we performed previously.

	!IMAGE[Dashboard.png](\Media\Dashboard.png)

	> [!KNOWLEDGE] Notice that there are no labeled or protected files shown at this time.  This uses the AIP P1 discovery functionality available with the AIP Scanner. Only the predefined Office 365 Sensitive Information Types are available with AIP P1 as Custom Sensitive Information Types require automatic conditions to be defined, which is an AIP P2 feature.

	> [!NOTE] Now that we know the sensitive information types that are most common in this environment, we can use that information to create **Recommended** conditions that will help guide user behavior when they encounter this type of data.

1. [] Under **Classifications** on the left, click **Labels** then expand **Confidential**, and click on **Contoso Internal**.

	^IMAGE[Open Screenshot](\Media\jyw5vrit.jpg)
1. [] In the Label: Contoso Internal blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	!IMAGE[cws1ptfd.jpg](\Media\cws1ptfd.jpg)
1. [] In the Condition blade, in the **Select information types** search box, type ```EU``` and check the boxes next to the **items shown below**.

	!IMAGE[xaj5hupc.jpg](\Media\xaj5hupc.jpg)
1. [] Next, before saving, replace EU in the search bar with ```credit``` and check the box next to **Credit Card Number**.

	^IMAGE[Open Screenshot](\Media\9rozp61b.jpg)
1. [] Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\41o5ql2y.jpg)

	> [!Knowledge] By default the condition is set to Recommended and a policy tip is created with standardized text.
	>
	>  !IMAGE[qdqjnhki.jpg](\Media\qdqjnhki.jpg)

1. [] Click **Save** in the Label: Contoso Internal blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\rimezmh1.jpg)
1. [] Press the **X** in the upper right-hand corner to close the Label: Contoso Internal blade.

	^IMAGE[Open Screenshot](\Media\em124f66.jpg)
1. [] Next, expand **Highly Confidential** and click on the **All Employees** sub-label.

	^IMAGE[Open Screenshot](\Media\2eh6ifj5.jpg)
1. [] In the Label: All Employees blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	^IMAGE[Open Screenshot](\Media\8cdmltcj.jpg)
1. [] In the Condition blade, select the **Custom** tab and enter ```Password``` for the **Name** and in the textbox below **Match exact phrase or pattern**, type ```pass@word1```.

	!IMAGE[ra7dnyg6.jpg](\Media\ra7dnyg6.jpg)
1. [] Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\ie6g5kta.jpg)
1. [] In the Labels: All Employees blade, in the **Configure conditions for automatically applying this label** section, click **Automatic**.

	!IMAGE[245lpjvk.jpg](\Media\245lpjvk.jpg)
	> [!HINT] The policy tip is automatically updated when you switch the condition to Automatic.
1. [] Click **Save** in the Label: All Employees blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\gek63ks8.jpg)
1. [] Press the **X** in the upper right-hand corner to close the Label: All Employees blade.

	^IMAGE[Open Screenshot](\Media\wzwfc1l4.jpg)

===

# Exercise 3: Security and Compliance Center
[🔙](#azure-information-protection)

In this exercise, we will migrate your AIP Labels and activate them in the Security and Compliance Center.  This will allow you to see the labels in Microsoft Information Protection based clients such as Office 365 for Mac and Mobile Devices.

Although we will not be demonstrating these capabilities in this lab, you can use the tenant information provided to test on your own devices.
 

===
# Activating Unified Labeling
[🔙](#azure-information-protection)
 
In this task, we will activate the labels from the Azure Portal for use in the Security and Compliance Center.

1. [] On @lab.VirtualMachine(Client01).SelectLink, navigate to ```https://portal.azure.com/?ActivateMigration=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/migrationActivationBlade```

1. [] Click **Activate** and **Yes**.

	!IMAGE[o0ahpimw.jpg](\Media\o0ahpimw.jpg)

	>[!NOTE] You should see a message similar to the one below.
	>
	> !IMAGE[SCCMigration.png](\Media\SCCMigration.png) 

1. [] In a new tab, browse to ```https://protection.office.com/``` and click on **Classifications** and **Labels** to review the migrated labels. 

	>[!NOTE] Keep in mind that now the SCC Sensitivity Labels have been activated, so any modifications, additions, or deletions will be syncronised to Azure Information Protection in the Azure Portal. There are some functional differences between the two sections (DLP in SCC, HYOK & Custom Permissions in AIP), so please be aware of this when modifying policies to ensure a consistent experience on clients. 

===

# Exercise 4: Testing AIP Policies
[🔙](#azure-information-protection)

Now that you have 3 test systems with users being affected by different policies configured, we can start testing these policies.  This exercise will run through various scenarios to demonstrate the use of AIP global and scoped policies and show the functionality of recommended and automatic labeling.
===
# Testing User Defined Permissions
[🔙](#azure-information-protection)

One of the most common use cases for AIP is the ability to send emails using User Defined Permissions (Do Not Forward). In this task, we will send an email using the Do Not Forward label to test that functionality.


1. [] On @lab.VirtualMachine(Client03).SelectLink, launch Microsoft Outlook, and click **Accept and start Outlook**.
1. [] The username should auto-populate based on the workplace join we performed earlier.  Click **Connect**.
1. [] Once configuration completes, **uncheck the box** to **Set up Outlook Mobile** and click **OK**.
1. [] **Close Outlook** and **reopen** to complete activation.
1. [] Once Outlook opens, click on the **New email** button.

	!IMAGE[6wan9me1.jpg](\Media\6wan9me1.jpg)

	> [!KNOWLEDGE] Note that the **Sensitivity** is set to **General** by default.
	>
	> !IMAGE[5esnhwkw.jpg](\Media\5esnhwkw.jpg)

1. [] Send an email to **Adam Smith** and **Alice Anderson** (```Adam Smith;Alice Anderson```). You may **optionally add an external email address** (preferably from a major social provider like gmail, yahoo, or outlook.com) to test the external recipient experience. For the **Subject** and **Body** type ```Test Do Not Forward Email```.

	^IMAGE[Open Screenshot](\Media\h0eh40nk.jpg)

1. [] In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	!IMAGE[901v6vpa.jpg](\Media\901v6vpa.jpg)

	> [!NOTE] If the AIP toolbar is not signed in, click **Sign In** and wait for it to use SSO and download policies (about 30 seconds).

1. [] Click on **Confidential** and then the **Do Not Forward** sub-label and click **Send**.

	!IMAGE[w8j1w1lm.jpg](\Media\w8j1w1lm.jpg)

	> [!Knowledge] If you receive the error message below, click on the Confidential \ Contoso Internal sub-label to force the download of your AIP identity certificates, then follow the steps above to change the label to Confidential \ Do Not Forward.
	>
	> !IMAGE[6v6duzbd.jpg](\Media\6v6duzbd.jpg)

1. [] Switch over to @lab.VirtualMachine(Client01).SelectLink or @lab.VirtualMachine(Client02).SelectLink and open Outlook, run through setup, and review the email in Adam Smith or Alice Anderson’s Outlook.  You will notice that the email is automatically shown in Outlook natively.

	!IMAGE[0xby56qt.jpg](\Media\0xby56qt.jpg)

	> [!Hint] The **Do Not Forward** protection template will normally prevent the sharing of the screen and taking screenshots when protected documents or emails are loaded.  However, since this screenshot was taken within a VM, the operating system was unaware of the protected content and could not prevent the capture.  
	>
	>It is important to understand that although we put controls in place to reduce risk, if a user has view access to a document or email they can take a picture with their smartphone or even retype the message. That said, if the user is not authorized to read the message then it will not even render and we will demonstrate that next.

	> [!KNOWLEDGE] If you elected to send a Do Not Forward message to an external email, you will have an experience similar to the images below.  These captures are included to demonstrate the functionality for those that chose not to send an external message.
	>
	> !IMAGE[tzj04wi9.jpg](\Media\tzj04wi9.jpg)
	> 
	> Here the user has received an email from Evan Green and they can click on the **Read the message** button.
	>
	>!IMAGE[wiefwcho.jpg](\Media\wiefwcho.jpg)
	>
	>Next, the user is given the option to either log in using the social identity provider (**Sign in with Google**, Yahoo, Microsoft Account), or to **sign in with a one-time passcode**.
	>
	>If they choose the social identity provider login, it should use the token previously cached by their browser and display the message directly.
	>
	>If they choose one-time passcode, they will receive an email like the one below with the one-time passcode.
	>
	>!IMAGE[m6voa9xi.jpg](\Media\m6voa9xi.jpg)
	>
	>They can then use this code to authenticate to the Office 365 Message Encryption portal.
	>
	>!IMAGE[8pllxint.jpg](\Media\8pllxint.jpg)
	>
	>After using either of these authentication methods, the user will see a portal experience like the one shown below.
	>
	>!IMAGE[3zi4dlk9.jpg](\Media\3zi4dlk9.jpg)
===

# Testing Global Policy
[🔙](#azure-information-protection)

In this task, we will create a document and send an email to demonstrate the functionality defined in the Global Policy.

1. [] Switch to @lab.VirtualMachine(Client03).SelectLink.
1. [] In Microsoft Outlook, click on the **New email** button.

	^IMAGE[Open Screenshot](\Media\6wan9me1.jpg)

1. [] Send an email to Adam Smith, Alice Anderson, and yourself (```Adam Smith;Alice Anderson;@lab.User.Email```).  For the **Subject** and **Body** type ```Test Contoso Internal Email```.

	^IMAGE[Open Screenshot](\Media\9gkqc9uy.jpg)

1. [] In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	^IMAGE[Open Screenshot](\Media\901v6vpa.jpg)

1. [] Click on **Confidential** and then **Contoso Internal** and click **Send**.

	^IMAGE[Open Screenshot](\Media\yhokhtkv.jpg)
1. [] On @lab.VirtualMachine(Client01).SelectLink or @lab.VirtualMachine(Client02).SelectLink, observe that you are able to open the email natively in the Outlook client. Also observe the **header text** that was defined in the label settings.

	!IMAGE[bxz190x2.jpg](\Media\bxz190x2.jpg)
	
1. [] In your email, note that you will be unable to open this message.  This experience will vary depending on the client you use (the image below is from Outlook 2016 for Mac) but they should have similar messages after presenting credentials. Since this is not the best experience for the recipient, later in the lab we will configure Exchange Online Mail Flow Rules to prevent content classified with internal only labels from being sent to external users.
	
	!IMAGE[52hpmj51.jpg](\Media\52hpmj51.jpg)

===

# Testing Scoped Policy
[🔙](#azure-information-protection)

In this task, we will create a document and send an email from one of the users in the Legal group to demonstrate the functionality defined in the first exercise. We will also show the behavior of the No Default Label policy on documents.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink.
1. [] In Microsoft Outlook, click on the **New email** button.
	
	^IMAGE[Open Screenshot](\Media\ldjugk24.jpg)
	
1. [] Send an email to Alice Anderson and Evan Green (```Alice Anderson;Evan Green```).  For the **Subject** and **Body** type ```Test Highly Confidential Legal Email```.
1. [] In the Sensitivity Toolbar, click on **Highly Confidential** and the **Legal Only** sub-label, then click **Send**.

	^IMAGE[Open Screenshot](\Media\ny1lwv0h.jpg)
1. [] Switch to @lab.VirtualMachine(Client02).SelectLink and click on the email.  You should be able to open the message natively in the client as Alice.

	!IMAGE[qeqtd2yr.jpg](\Media\qeqtd2yr.jpg)
1. [] Switch to @lab.VirtualMachine(Client03).SelectLink and click on the email. You should be unable to open the message as Evan.

	!IMAGE[6y99u8cl.jpg](\Media\6y99u8cl.jpg)

	> [!Knowledge] You may notice that the Office 365 Message Encryption wrapper message is displayed in the preview pane.  It is important to note that the content of the email is not displayed here.  The content of the message is contained within the encrypted message.rpmsg attachment and only authorized users will be able to decrypt this attachment.
	>
	>!IMAGE[w4npbt49.jpg](\Media\w4npbt49.jpg)
	>
	>If an unauthorized recipient clicks on **Read the message** to go to the OME portal, they will be presented with the same wrapper message.  Like the external recipient from the previous task, this is not an ideal experience. So, you may want to use a mail flow rule to manage scoped labels as well.
	>
	>!IMAGE[htjesqwe.jpg](\Media\htjesqwe.jpg)

1. [] On @lab.VirtualMachine(Client01).SelectLink, open **Microsoft Word**.
1. [] Create a new **Blank document** and type ```This is a test document``` and **save the document**.

	> [!ALERT] When you click **Save**, you will be prompted to choose a classification.  This is a result of having **None** set as the default label in the scoped policy while requiring all documents to be labeled.  This is a useful for driving **active classification decisions** by specific groups within your organization.  Notice that Outlook still has a default of **General** because of the Advanced setting we added to the scoped policy.  **This is recommended** because user send many more emails each day than they create documents. Actively forcing users to classify each email would be an unpleasant user experience whereas they are typically more understanding of having to classify each document if they are in a sensitive department or role.

1. [] Choose a classification to save the document.
===

# Testing Recommended and Automatic Classification
[🔙](#azure-information-protection)

In this task, we will test the configured recommended and automatic conditions we defined in Exercise 1.  Recommended conditions can be used to help organically train your users to classify sensitive data appropriately and provides a method for testing the accuracy of your dectections prior to switching to automatic classification.  Automatic conditions should be used after thorough testing or with items you are certain need to be protected. Although the examples used here are fairly simple, in production these could be based on complex regex statements or only trigger when a specific quantity of sensitive data is present.

1. [] Switch to @lab.VirtualMachine(Client03).SelectLink and launch **Microsoft Word**.
1. [] In Microsoft Word, create a new **Blank document** and type ```My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368``` and **save** the document.

	> [!NOTE] This card number is a fake number that was generated using the Credit Card Generator for Testing at [https://developer.paypal.com/developer/creditCardGenerator/](https://developer.paypal.com/developer/creditCardGenerator/).  The Microsoft Classification Engine uses the Luhn Algorithm to prevent false positives so when testing, please make sure to use valid numbers.

1. [] Notice that you are prompted with a recommendation to change the classification to Confidential \ Contoso Internal. Click on **Change now** to set the classification and protect the document.

	!IMAGE[url9875r.jpg](\Media\url9875r.jpg)
	> [!Knowledge] Notice that, like the email in Task 2 of this exercise, the header value configured in the label is added to the document.
	>
	>!IMAGE[dcq31lz1.jpg](\Media\dcq31lz1.jpg)
1. [] In Microsoft Word, create a new **Blank document** and type ```my password is pass@word1``` and **save** the document.

	>[!HINT] Notice that the document is automatically classified and protected wioth the Highly Confidential \ All Employees label.
	>
	>!IMAGE[6vezzlnj.jpg](\Media\6vezzlnj.jpg)
1. [] Next, in Microsoft Outlook, click on the **New email** button.
	
	^IMAGE[Open Screenshot](\Media\ldjugk24.jpg)
	
1. [] Draft an email to Alice Anderson and Adam Smith (```Alice Anderson;Adam Smith```).  For the **Subject** and **Body** type ```Test Highly Confidential All Employees Automation```.

	^IMAGE[Open Screenshot](\Media\4v3wrrop.jpg)
1. [] Attach the **second document you created** to the email.

	!IMAGE[823tzyfd.jpg](\Media\823tzyfd.jpg)

	> [!HINT] Notice that the email was automatically classified as Highly Confidential \ All Employees.  This functionality is highly recommended because matching the email classification to attachments provides a much more cohesive user experience and helps to prevent inadvertent information disclosure in the body of sensitive emails.
	>
	>!IMAGE[yv0afeow.jpg](\Media\yv0afeow.jpg)

1. [] In the email, click **Send**.
===
# Bulk Classification with the AIP Client

In this task, we will perform bulk classification using the built-in functionality of the AIP Client.  This can be useful for users that want to classify/protect many documents that exist in a central location or locations identified by scanner discovery.  Because this is done manually, it is an AIP P1 feature.

1. [] On @lab.VirtualMachine(Scanner01).SelectLink, browse to the **C:\\**.
2. [] Right-click on the PII folder and select **Classify and Protect**.
   
   !IMAGE[CandP.png](\Media\CandP.png)
1. [] When prompted, click use another user and use the credentials below to authenticate:

	```AIPScanner@@lab.CloudCredential(17).TenantName```

	```Somepass1```

1. [] In the AIP client Classify and protect interface, select **Highly Confidential\\All Employees** and press **Apply**. 

	!IMAGE[CandP2.png](\Media\CandP2.png)

> [!NOTE] You may review the results in a text file by clicking show results, or simply close the window.
===
# Exercise 5: Classification, Labeling, and Protection with the Azure Information Protection Scanner
[🔙](#azure-information-protection)

The Azure Information Protection scanner allows you to  classify and protect sensitive information stored in on-premises CIFS file shares and SharePoint sites.  

In this exercise, you will change the condition we created previously from a recommended to an automatic classification rule.  After that, we will run the AIP Scanner in enforce mode to classify and protect the identified sensitive data.

===

# Configuring Automatic Conditions
[🔙](#azure-information-protection)
 
Now that we know what types of sensitive data we need to protect, we will configure some automatic conditions (rules) that the scanner can use to classify and protect content.

1. [] Switch back to @lab.VirtualMachine(Client01).SelectLink and open the browser that is logged into the Azure Portal.

2. [] Under **Classifications** on the left, click **Labels** then expand **Confidential**, and click on **Contoso Internal**.

3. [] In the Label : Contoso Internal blade, under **Select how this label is applied: automatically or recommended to user**, click **Automatic**.

	^IMAGE[Open Screenshot](\Media\1ifaer4l.jpg)

1. [] Click **Save** in the Label: Contoso Internal blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\rimezmh1.jpg)

1. [] Press the **X** in the upper right-hand corner to close the Label: Contoso Internal blade.

===

# Enforcing Configured Rules
[🔙](#azure-information-protection)
 
In this task, we will set the AIP scanner to enforce the conditions we set up in the previous task and have it rerun on all files using the Start-AIPScan command.

1. [] Switch to @lab.VirtualMachine(Scanner01).SelectLink
1. [] Run the commands below to run an enforced scan using defined policy.

    ```
	Set-AIPScannerConfiguration -Enforce On -DiscoverInformationTypes PolicyOnly
	```
	```
	Start-AIPScan
    ```

	> [!HINT] Note that this time we used the DiscoverInformationTypes -PolicyOnly switch before starting the scan. This will have the scanner only evaluate the conditions we have explicitly defined in conditions.  This increases the effeciency of the scanner and thus is much faster.  After reviewing the event log we will see the result of the enforced scan.
	>
	>!IMAGE[k3rox8ew.jpg](\Media\k3rox8ew.jpg)
	>
	>If we switch back to @lab.VirtualMachine(Client01).SelectLink and look in the reports directory we opened previously at ```\\Scanner01.contoso.azure\c$\users\aipscanner\AppData\Local\Microsoft\MSIP\Scanner\Reports```, you will notice that the old scan reports are zipped in the directory and only the most recent results aare showing.  
	>
	> If needed, use the credentials below:
	>
	>```Contoso\LabUser```
	>
	>```Pa$$w0rd```
	>
	>!IMAGE[s8mn092f.jpg](\Media\s8mn092f.jpg)
	>
	>Also, the DetailedReport.csv now shows the files that were protected.
	>
	>
	>!IMAGE[6waou5x3.jpg](\Media\6waou5x3.jpg)
	>
	>^IMAGE[Open Fullscreen](6waou5x3.jpg)

===

# Reviewing Protected Documents
[🔙](#azure-information-protection)

Now that we have Classified and Protected documents using the scanner, we can review the documents we looked at previously to see their change in status.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink.
 
2. [] Navigate to ```\\Scanner01.contoso.azure\documents```. 

	> If needed, use the credentials below:
	>
	>```Contoso\LabUser```
	>
	>```Pa$$w0rd```
 
	^IMAGE[Open Screenshot](\Media\hipavcx6.jpg)
3. [] Open one of the Contoso Purchasing Permissions documents or Run For The Cure spreadsheets.
 
 	
	
	> [!NOTE] Observe that the same document is now classified as Confidential \ Contoso Internal. 
	>
	>!IMAGE[s1okfpwu.jpg](\Media\s1okfpwu.jpg)
===
# Reviewing the Dashboards

We can now go back and look at the dashboards and observe how they have changed.

1. [] On @lab.VirtualMachine(Client01).SelectLink, open the browser that is logged into the Azure Portal.

1. [] Under **Dashboards**, click on **Usage report (Preview)**.

	> [!NOTE] Observe that there are now entries from the AIP scanner, File Explorer, Microsoft Outlook, and Microsoft Word based on our activities in this lab. You may not see details of label data right away as this takes longer to process.  I have included a screenshot of the results below, but you may check back later in the lab to see the full results.
	>
	> !IMAGE[Usage.png](\Media\Usage.png)
	>
	> !IMAGE[Usage2.png](\Media\Usage2.png)
2. [] Next, under dashboards, click on **Data discovery (Preview)**.

	> [!NOTE] As mentioned above, label data may not show up initially but you should start seeing protection data in the portal.  I have included a screenshot of the final result so please check back throughout the lab to see the label data from the AIP scanner.
	>
	> !IMAGE[Discovery.png](\Media\Discovery.png)
	> 
	> !IMAGE[discovery2.png](\Media\discovery2.png)
	
===
# Exercise 6: Exchange Online IRM Capabilities
[🔙](#azure-information-protection)

Exchange Online can work in conjunction with Azure Information Protection to provide advanced capabilities for protecting sensitive data being sent over email.  You can also manage the flow of classified content to ensure that it is not sent to unintended recipients.  

## Configuring Exchange Online Mail Flow Rules

In this task, we will configure a mail flow rule to detect sensitive information traversing the network in the clear and encrypt it using the Encrypt Only RMS Template.  We will also create a mail flow rule to prevent messages classified as Confidential \ Contoso Internal from being sent to external recipients.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and open an **Admin PowerShell Prompt**.

2. [] Type the commands below to connect to an Exchange Online PowerShell session.  Use the credentials provided when prompted.

	```
	Set-ExecutionPolicy RemoteSigned
	```

	```
	$UserCredential = Get-Credential
	```

	```@lab.CloudCredential(17).Username```

	```@lab.CloudCredential(17).Password```

	```
	$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://outlook.office365.com/powershell-liveid/ -Credential $UserCredential -Authentication Basic -AllowRedirection
	Import-PSSession $Session
	```

1. [] Create a new Exchange Online Mail Flow Rule using the code below:

	```
	New-TransportRule -Name "Encrypt external mails with sensitive content" -SentToScope NotInOrganization -ApplyRightsProtectionTemplate "Encrypt" -MessageContainsDataClassifications @(@{Name="ABA Routing Number"; minCount="1"},@{Name="Credit Card Number"; minCount="1"},@{Name="Drug Enforcement Agency (DEA) Number"; minCount="1"},@{Name="International Classification of Diseases (ICD-10-CM)"; minCount="1"},@{Name="International Classification of Diseases (ICD-9-CM)"; minCount="1"},@{Name="U.S. / U.K. Passport Number"; minCount="1"},@{Name="U.S. Bank Account Number"; minCount="1"},@{Name="U.S. Individual Taxpayer Identification Number (ITIN)"; minCount="1"},@{Name="U.S. Social Security Number (SSN)"; minCount="1"})
	```

	>[!KNOWLEDGE] This mail flow rule can be used to encrypt sensitive data leaving via email.  This can be customized to add additional sensitive data types. A breakdown of the command is listed below.
	>
	>New-TransportRule 
	>
	>-Name "Encrypt external mails with sensitive content" 
	>
	>-SentToScope NotInOrganization 
	>
	>-ApplyRightsProtectionTemplate "Encrypt" 
	>
	>-MessageContainsDataClassifications @(@{Name="ABA Routing Number"; minCount="1"},@{Name="Credit Card Number"; minCount="1"},@{Name="Drug Enforcement Agency (DEA) Number"; minCount="1"},@{Name="International Classification of Diseases (ICD-10-CM)"; minCount="1"},@{Name="International Classification of Diseases (ICD-9-CM)"; minCount="1"},@{Name="U.S. / U.K. Passport Number"; minCount="1"},@{Name="U.S. Bank Account Number"; minCount="1"},@{Name="U.S. Individual Taxpayer Identification Number (ITIN)"; minCount="1"},@{Name="U.S. Social Security Number (SSN)"; minCount="1"})
	
	> [!HINT] Next, we need to capture the **Label ID** for the **Confidential \ Contoso Internal** label. 

1. [] Switch to the Azure Portal and under **Classifications** click on Labels, then expand **Confidential** and click on **Contoso Internal**.

	!IMAGE[w2w5c7xc.jpg](\Media\w2w5c7xc.jpg)

	> [!HINT] If you closed the azure portal, open an Edge InPrivate window and navigate to ```https://portal.azure.com```.

1. [] In the Label: Contoso Internal blade, scroll down to the Label ID and **copy** the value.

	!IMAGE[lypurcn5.jpg](\Media\lypurcn5.jpg)

	> [!ALERT] Make sure that there are no spaces before or after the Label ID as this will cause the mail flow rule to be ineffective.

1. [] Next, return to the PowerShell window and type ```$labelid = "``` then paste the **LabelID** for the **Contoso Internal** label, type ```"```, and press **Enter**.
1. [] Now, create another Exchange Online Mail Flow Rule using the code below:

	```
	$labeltext = "MSIP_Label_"+$labelid+"_enabled=true"
	New-TransportRule -name "Block Confidential Contoso Internal" -SentToScope notinorganization -HeaderContainsMessageHeader  "msip_labels" -HeaderContainsWord $labeltext -RejectMessageReasonText “Contoso internal messages cannot be sent to external recipients.”
	```

	>[!KNOWLEDGE] This mail flow rule can be used to prevent internal only communications from being sent to an external audience.
	>
	>New-TransportRule 
	>
	>-name "Block Confidential Contoso Internal" 
	>
	>-SentToScope notinorganization 
	>
	>-HeaderContainsMessageHeader "msip_labels" 
	>
	>-HeaderContainsWord $labeltext 
	>
	>-RejectMessageReasonText “Contoso internal messages cannot be sent to external recipients.”

	>[!NOTE] In a production environment, customers would want to create a rule like this for each of their labels that they did not want going externally.
===

# Demonstrating Exchange Online Mail Flow Rules
[🔙](#azure-information-protection)

In this task, we will send emails to demonstrate the results of the Exchange Online mail flow rules we configured in the previous task.  This will demonstrate some ways to protect your sensitive data and ensure a positive user experience with the product.

1. [] Switch to @lab.VirtualMachine(Client03).SelectLink.
1. [] In Microsoft Outlook, click on the **New email** button.

	^IMAGE[Open Screenshot](\Media\6wan9me1.jpg)

1. [] Send an email to Adam Smith, Alice Anderson, and yourself (```Adam Smith;Alice Anderson;@lab.User.Email```).  For the **Subject**, type ```Test Credit Card Email``` and for the **Body**, type ```My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368```, then click **Send**.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and review the received email.

	!IMAGE[pidqfaa1.jpg](\Media\pidqfaa1.jpg)

	> [!Knowledge] Note that there is no encryption applied to the message.  That is because we set up the rule to only apply to external recipients.  If you were to leave that condition out of the mail flow rule, internal recipients would also receive an encrypted copy of the message.  The image below shows the encrypted message that was received externally.
	>
	>!IMAGE[c5foyeji.jpg](\Media\c5foyeji.jpg)
	>
	>Below is another view of the same message received in Outlook Mobile on an iOS device.
	>
	>!IMAGE[599ljwfy.jpg](\Media\599ljwfy.jpg)

1. [] Next, in Microsoft Outlook, click on the **New email** button.

	^IMAGE[Open Screenshot](\Media\6wan9me1.jpg)
1. [] Send an email to Adam Smith, Alice Anderson, and yourself (```Adam Smith;Alice Anderson;@lab.User.Email```).  For the **Subject** and **Body** type ```Another Test Contoso Internal Email```.

	^IMAGE[Open Screenshot](\Media\d476fmpg.jpg)

1. [] In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	^IMAGE[Open Screenshot](\Media\901v6vpa.jpg)

1. [] Click on **Confidential** and then **Contoso Internal** and click **Send**.

	^IMAGE[Open Screenshot](\Media\yhokhtkv.jpg)
1. [] In about a minute, you should receive an **Undeliverable** message from Exchange with the users that the message did not reach and the message you defined in the previous task.

	!IMAGE[kgjvy7ul.jpg](\Media\kgjvy7ul.jpg)

> [!HINT] There are many other use cases for Exchange Online mail flow rules but this should give you a quick view into what is possible and how easy it is to improve the security of your sensitive data through the use of Exchange Online mail flow rules and Azure Information Protection.

===
# Exercise 7: SharePoint IRM Configuration
[🔙](#azure-information-protection)

In this exercise, you will configure SharePoint Online Information Rights Management (IRM) and configure a document library with an IRM policy to protect documents that are downloaded from that library.

===
# Enable Information Rights Management in SharePoint Online
[🔙](#azure-information-protection)
 
In this task, we will enable Information Rights Management in SharePoint Online.

1. [] Switch to @lab.VirtualMachine(Client03).SelectLink, click @lab.CtrlAltDelete and use the credentials below to log in.

	+++LabUser+++

	+++Pa$$w0rd+++

1. [] Launch an Edge InPrivate session to ```https://admin.microsoft.com/AdminPortal/Home#/```.
 
1. [] If needed, log in using the credentials below:

	 ```@lab.CloudCredential(17).Username```
	 
	 ```@lab.CloudCredential(17).Password```
 
1. [] Hover over the **Admin centers** section of the bar on the left and choose **SharePoint**.

	!IMAGE[r5a21prc.jpg](\Media\r5a21prc.jpg)
 
1. [] In the SharePoint admin center click on **settings**.

1. [] Scroll down to the Information Rights Management (IRM) section and select the option button for **Use the IRM service specified in your configuration**.
 
1. [] Click the **Refresh IRM Settings** button.

	!IMAGE[1qv8p13n.jpg](\Media\1qv8p13n.jpg)

	>[!HINT] After the browser refreshes, you can scroll down to the same section and you will see a message stating **We successfully refreshed your setings.**
	>
	>!IMAGE[daeglgk9.jpg](\Media\daeglgk9.jpg)
1. [] Scroll down and click **OK**.
1. [] Next, navigate to ```https://admin.microsoft.com/AdminPortal/Home#/users```.
1. [] Click on **Nuck Chorris** and on the profile page, next to Roles, click **Edit**.

	!IMAGE[df6t9nk1.jpg](\Media\df6t9nk1.jpg)
1. [] On the Edit user roles page, select **Customized administrator**, check the box next to **SharePoint administrator**, and click **Save**.

	!IMAGE[3rj47ym9.jpg](\Media\3rj47ym9.jpg)
1. [] **Close the Edge InPrivate browser** window to **clear the credentials**.

 
===

# Site Creation and Information Rights Management Integration
[🔙](#azure-information-protection)
 
In this task, we will create a new SharePoint site and enable Information Rights Management in a document library.

1. [] Launch a new Edge InPrivate session to ```https://portal.office.com```.
1. [] Log in using the credentials below:

	```NuckC@@lab.CloudCredential(17).TenantName```

	```NinjaCat123```
1. [] Click on **SharePoint** in the list.

	!IMAGE[twsp6mvj.jpg](\Media\twsp6mvj.jpg)

1. [] Dismiss any introductory screens and, at the top of the page, click **+ Create site**.

	!IMAGE[7v8wctu2.jpg](\Media\7v8wctu2.jpg)

	[!NOTE] If you do not see the **+ Create site** button, resize the VM window by dragging the divider for the instructions to the right until the VM resizes and you can see the button.
 
1. [] On the Create a site page, click **Team site**.

	^IMAGE[Open Screenshot](\Media\406ah98f.jpg)
 
1. [] On the next page, type ```IRM Demo``` for **Site name** and for the **Site description**, type ```This is a team site for demonstrating SharePoint IRM capabilities``` and set the **Privacy settings** to **Public - anyone in the organization can access the site** and click **Next**.

	^IMAGE[Open Screenshot](\Media\ug4tg8cl.jpg)

1. [] On the Add group members page, click **Finish**.
1. [] In the newly created site, on the left navigation bar, click **Documents**.

	^IMAGE[Open Screenshot](\Media\yh071obk.jpg)
 
1. [] In the upper right-hand corner, click the **Settings icon** and click **Library settings**.

	!IMAGE[1qo31rp6.jpg](\Media\1qo31rp6.jpg)
 
1. [] On the Documents > Settings page, under **Permissions and Management**, click **Information Rights Management**.

	!IMAGE[ie2rmsk2.jpg](\Media\ie2rmsk2.jpg)
 
	>[!ALERT] It may take up to 10 minutes for the global IRM settings to apply to document libraries.  If this has not appeared after a few minutes, try creating a new document library to see if the link is available. 
 
1. [] On the Settings > Information Rights Management Settings page, check the box next to Restrict permissions on this library on download and under **Create a permission policy title** type ```Contoso IRM Policy```, and under **Add a permission policy description** type ```This content contained within this file is for use by Contoso Corporation employees only.```
 
	^IMAGE[Open Screenshot](\Media\m9v7v7ln.jpg)
1. [] Next, click on **SHOW OPTIONS** below the policy description and in the **Set additional IRM library settings** section, check the boxes next to **Do not allow users to upload documents that do not support IRM** and **Prevent opening documents in the browser for this Document Library**.

	!IMAGE[0m2qqtqn.jpg](\Media\0m2qqtqn.jpg)
	>[!KNOWLEDGE] These setting prevent the upload of documents that cannot be protected using Information Rights Managment (Azure RMS) and forces protected documents to be opened in the appropriate application rather than rendering in the SharePoint Online Viewer.
 
1. [] Next, under the **Configure document access rights** section, check the box next to **Allow viewers to run script and screen reader to function on downloaded documents**.

	!IMAGE[72fkz2ds.jpg](\Media\72fkz2ds.jpg)
	>[!HINT] Although this setting may reduce the security of the document, this is typically provided for accessibility purposes.
1. [] Finally, in the **Configure document access rights** section, check the box next to  **Users must verify their credentials using this interval (days)** and type ```7``` in the text box.

	!IMAGE[tt1quq3f.jpg](\Media\tt1quq3f.jpg)
1. [] At the bottom of the page, click **OK** to complete the configuration of the protected document library.
1. [] On the Documents > Settings page, in the left-hand navigation pane, click on **Documents** to return to the document library. section.
 
1. [] Leave the browser open and continue to the next task.
 
===

# Uploading Content to the Document Library
[🔙](#azure-information-protection)
 
Create an unprotected Word document, label it as Internal, and upload it to the document library. 

1. [] Launch **Microsoft Word**.
1. [] Create a new **Blank document**.

	>[!NOTE] Notice that by default the document is labeled as the unprotected classification **General**.
 
1. [] In the Document, type ```This is a test document```.
 
1. [] **Save** the document and **close Microsoft Word**.
1. [] Return to the IRM Demo protected document library and click on **Upload > Files**.

	!IMAGE[m95ixvv1.jpg](\Media\m95ixvv1.jpg)
1. [] Navigate to the location where you saved the document, select it and click **Open** to upload the file.
 
1. [] Next, minimize the browser window and right-click on the desktop. Hover over **New >** and click on **Microsoft Access Database**. Name the database ```BadFile```.

	!IMAGE[e3nxt4a2.jpg](\Media\e3nxt4a2.jpg)
1. [] Return to the document library and attempt to upload the file.

	>[!KNOWLEDGE] Notice that you are unable to upload the file because it cannot be protected.
	>	
	>!IMAGE[432hu3pi.jpg](\Media\432hu3pi.jpg)
===

# SharePoint IRM Functionality
[🔙](#azure-information-protection)
 
Files that are uploaded to a SharePoint IRM protected document library are protected upon download based on the user's access rights to the document library.  In this task, we will share a document with Alice Anderson and review the access rights provided.

1. [] Select the uploaded document and click **Share** in the action bar.

	!IMAGE[1u2jsod7.jpg](\Media\1u2jsod7.jpg)
1. [] In the Send Link dialog, type ```Alice``` and click on **Alice Anderson** then **Send**.

	!IMAGE[j6w1v4z9.jpg](\Media\j6w1v4z9.jpg)
1. [] Switch to @lab.VirtualMachine(Client02).SelectLink.
1. [] Open Outlook and click on the email from Nuck Chorris, then click on the **Open** link.

	^IMAGE[Open Screenshot](\Media\v39ez284.jpg)
1. [] This will launch the IRM Demo document library.  Click on the document to open it in Microsoft Word.

	!IMAGE[xmv9dmvk.jpg](\Media\xmv9dmvk.jpg)
1. [] After the document opens, you will be able to observe that it is protected.  Click on the View Permissions button to review the restrictions set on the document.

	!IMAGE[4uya6mro.jpg](\Media\4uya6mro.jpg)
	>[!NOTE] These permissions are based on the level of access that they user has to the document library.  In a production environment most users would likely have less rights than shown in this example.

===
# Lab Complete
Congratulations! You have completed the Azure Information Protection Hands on Lab. 
===
# Familiar with AIP
[🔙](#azure-information-protection)

## Exercise 1A: Configuring AIP Scanner for Discovery

Even before configuring an AIP classification taxonomy, customers can scan and identify files containing sensitive information based on the built-in sensitive information types included in the Microsoft Classification Engine.  

!IMAGE[ahwj80dw.jpg](\Media\ahwj80dw.jpg)

Often, this can help drive an appropriate level of urgency and attention to the risk customers face if they delay rolling out AIP classification and protection.  

In this exercise, we will install the AIP scanner and run it against repositories in discovery mode.  Later in this lab (after configuring labels and conditions) we will revisit the scanner to perform automated classification, labeling, and protection of sensitive documents.

===
# Configuring Azure Log Analytics
[🔙](#azure-information-protection)

In order to collect log data from Azure Information Protection clients and services, you must first configure the log analytics workspace.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink.
1. [] In the InPrivate window, navigate to ```https://portal.azure.com/```
	>
	>^IMAGE[Open Screenshot](\Media\cznh7i2b.jpg)

	> [!KNOWLEDGE] If necessary, log in using the username and password below:
	>
	>```@lab.CloudCredential(17).Username``` 
	>
	>```@lab.CloudCredential(17).Password```
	
1. [] After logging into the portal, type the word ```info``` into the **search bar** and press **Enter**, then click on **Azure Information Protection**. 

	!IMAGE[2598c48n.jpg](\Media\2598c48n.jpg)
	
	> [!HINT] If you do not see the search bar at the top of the portal, click on the **Magnifying Glass** icon to expand it.
	>
	> !IMAGE[ny3fd3da.jpg](\Media\ny3fd3da.jpg)

1. [] In the Azure Information Protection blade, under **Manage**, click **Configure analytics (preview)**.

1. [] Next, click on **+ Create new workspace**.

	!IMAGE[qu68gqfd.jpg](\Media\qu68gqfd.jpg)
1. [] In the Log analytics workspace using the values in the table below and click **OK**.

	|||
	|-----|-----|
	|OMS Workspace|**Unique Workspace Name**|
	|Resource Group|```AIP-RG```|
	|Location|**East US**|

	^IMAGE[Open Screenshot](\Media\5butui15.jpg)
1. [] Next, back in the Configure analytics (preview) blade, **check the box** next to the workspace and click **OK**.

	!IMAGE[gste52sy.jpg](\Media\gste52sy.jpg)
1. [] Click **Yes**, in the confirmation dialog.

	!IMAGE[zgvmm4el.jpg](\Media\zgvmm4el.jpg)
===
# AIP Scanner Setup
In this task we will install the AIP scanner binaries and create the Azure AD Applications necessary for authentication.
[🔙](#azure-information-protection)

## Installing the AIP Scanner Service

The first step in configuring the AIP Scanner is to install the service and connect the database.  This is done with the Install-AIPScanner cmdlet that is provided by the AIP Client software.  The AIPScanner service account has been pre-staged in Active Directory for convenience.

1. [] Switch to @lab.VirtualMachine(Scanner01).SelectLink and (if necessary) click @lab.CtrlAltDelete and log in using the username and password below:

	+++@lab.VirtualMachine(Scanner01).Username+++ 
	
	+++@lab.VirtualMachine(Scanner01).Password+++

1. [] Right-click on the **PowerShell** icon in the taskbar and click on **Run as Administrator**.

	!IMAGE[7to6p334.jpg](\Media\7to6p334.jpg)

1. [] At the PowerShell prompt, type ```$SQL = "Scanner01"``` and press **Enter**.
1. [] Next, type ```Install-AIPScanner -SQLServerInstance $SQL``` and press **Enter**.
1. [] When prompted, provide the credentials for the AIP scanner service account.
	
	```Contoso\AIPScanner```

	```Somepass1```

	^IMAGE[Open Screenshot](\Media\pc9myg9x.jpg)

	> [!knowledge] You should see a success message like the one below. 
	>
	>!IMAGE[w7goqgop.jpg](\Media\w7goqgop.jpg)
	>

## Creating Azure AD Applications for the AIP Scanner

Now that you have installed the scanner bits, you need to get an Azure AD token for the scanner service account to authenticate so that it can run unattended. This requires registering both a Web app and a Native app in Azure Active Directory.  The commands below will do this in an automated fashion rather than needing to go into the Azure portal directly.

1. [] In PowerShell, run ```Connect-AzureAD``` and use the username and password below. 
	
	```@lab.CloudCredential(17).Username```
	
	```@lab.CloudCredential(17).Password```
1. [] Next, click the **T** to **type the commands below** in the PowerShell window. 

	> [!ALERT] Press Enter only after you see **-CustomKeyIdentifier "AIPClient"**.

	> [!NOTE] This will create a new Web App Registration and Service Principal in Azure AD.

   ```
   New-AzureADApplication -DisplayName AIPOnBehalfOf -ReplyUrls http://localhost
   $WebApp = Get-AzureADApplication -Filter "DisplayName eq 'AIPOnBehalfOf'"
   New-AzureADServicePrincipal -AppId $WebApp.AppId
   $WebAppKey = New-Guid
   $Date = Get-Date
   New-AzureADApplicationPasswordCredential -ObjectId $WebApp.ObjectID -startDate $Date -endDate $Date.AddYears(1) -Value $WebAppKey.Guid -CustomKeyIdentifier "AIPClient"
	```

1. [] Next, we must build the permissions object for the Native App Registration.  This is done using the commands below.
   
	> [!ALERT] Press Enter only after you see **$Access.ResourceAccess = $Scope**.

   ```
   $AIPServicePrincipal = Get-AzureADServicePrincipal -All $true | ? {$_.DisplayName -eq 'AIPOnBehalfOf'}
   $AIPPermissions = $AIPServicePrincipal | select -expand Oauth2Permissions
   $Scope = New-Object -TypeName "Microsoft.Open.AzureAD.Model.ResourceAccess" -ArgumentList $AIPPermissions.Id,"Scope"
   $Access = New-Object -TypeName "Microsoft.Open.AzureAD.Model.RequiredResourceAccess"
   $Access.ResourceAppId = $WebApp.AppId
   $Access.ResourceAccess = $Scope
	```
1. [] Next, we will use the object created above to create the Native App Registration.
   
	> [!ALERT] Press Enter only after you see **-AppId $NativeApp.AppId**.

   ```
   New-AzureADApplication -DisplayName AIPClient -ReplyURLs http://localhost -RequiredResourceAccess $Access -PublicClient $true
   $NativeApp = Get-AzureADApplication -Filter "DisplayName eq 'AIPClient'"
   New-AzureADServicePrincipal -AppId $NativeApp.AppId
	```
   
1. [] Finally, we will output the Set-AIPAuthentication command by running the commands below and pressing **Enter**.
   
	> [!ALERT] Press Enter only after you see **Start ~\Desktop\Set-AIPAuthentication.txt**.
   
   ```
   "Set-AIPAuthentication -WebAppID " + $WebApp.AppId + " -WebAppKey " + $WebAppKey.Guid + " -NativeAppID " + $NativeApp.AppId | Out-File ~\Desktop\Set-AIPAuthentication.txt
	Start ~\Desktop\Set-AIPAuthentication.txt
	```
1. [] In the new notepad window, copy the command to the clipboard.
1. [] Click on the Start menu and type ```PowerShell```, right-click on the PowerShell program, and click **Run as a different user**.

	!IMAGE[zgt5ikxl.jpg](\Media\zgt5ikxl.jpg)

1. [] When prompted, enter the username and password below and click **OK**.

	```Contoso\AIPScanner``` 

	```Somepass1```

1. [] Paste the copied **Set-AIPAuthentication** command into this window and run it.
1. [] When prompted, enter the username and password below:

	```AIPScanner@@lab.CloudCredential(17).TenantName```

	```Somepass1```

	^IMAGE[Open Screenshot](\Media\qfxn64vb.jpg)

1. [] In the Permissions requested window, click **Accept**.

   !IMAGE[nucv27wb.jpg](\Media\nucv27wb.jpg)
   
	>[!knowledge] You will a message like the one below in the PowerShell window once complete.
	>
	>!IMAGE[y2bgsabe.jpg](\Media\y2bgsabe.jpg)
1. [] **Close the current PowerShell window**.
1. [] **In the admin PowerShell window** and type the command below and press **Enter**.

	```Restart-Service AIPScanner```
   
===

# Configuring Repositories
[🔙](#azure-information-protection)

In this task, we will configure repositories to be scanned by the AIP scanner.  As previously mentioned, these can be any type of CIFS file shares including NAS devices sharing over the CIFS protocol.  Additionally, On premises SharePoint 2010, 2013, and 2016 document libraries and lists (attachements) can be scanned.  You can even scan entire SharePoint sites by providing the root URL of the site.  There are several optional 

> [!NOTE] SharePoint 2010 is only supported for customers who have extended support for that version of SharePoint.

The next task is to configure repositories to scan.  These can be on-premises SharePoint 2010, 2013, or 2016 document libraries and any accessible CIFS based share.

1. [] In the PowerShell window on Scanner01 run the commands below

    ```
    Add-AIPScannerRepository -Path http://Scanner01/documents -SetDefaultLabel Off
	```
	```
	Add-AIPScannerRepository -Path \\Scanner01\documents -SetDefaultLabel Off
    ```
	>[!Knowledge] Notice that we added the **-SetDefaultLabel Off** switch to each of these repositories.  This is necessary because our Global policy has a Default label of **General**.  If we did not add this switch, any file that did not match a condition would be labeled as General when we do the enforced scan.

	^IMAGE[Open Screenshot](\Media\00niixfd.jpg)
1. [] To verify the repositories configured, run the command below.
	
    ```
    Get-AIPScannerRepository
    ```
	^IMAGE[Open Screenshot](\Media\n5hj5e7j.jpg)

===

# Running Sensitive Data Discovery
[🔙](#azure-information-protection)

1. [] Run the commands below to run a discovery cycle.

    ```
	Set-AIPScannerConfiguration -DiscoverInformationTypes All -Enforce Off
	```
	```
	Start-AIPScan
    ```

	> [!Knowledge] Note that we used the DiscoverInformationTypes -All switch before starting the scan.  This causes the scanner to use any custom conditions that you have specified for labels in the Azure Information Protection policy, and the list of information types that are available to specify for labels in the Azure Information Protection policy.  Although the scanner will discover documents to classify, it will not do so because the default configuration for the scanner is Discover only mode.
 	
1. [] Right-click on the **Windows** button in the lower left-hand corner and click on **Event Viewer**.
 
	^IMAGE[Open Screenshot](\Media\cjvmhaf0.jpg)
1. [] Expand **Application and Services Logs** and click on **Azure Information Protection**.

	^IMAGE[Open Screenshot](\Media\dy6mnnpv.jpg)
 
	>[!NOTE] You will see an event like the one below when the scanner completes the cycle.
	>
	>!IMAGE[agnx2gws.jpg](\Media\agnx2gws.jpg)
 
1. [] Next, switch to @lab.VirtualMachine(Client01).SelectLink, open a **File Explorer** window, and browse to ```\\Scanner01.contoso.azure\c$\users\aipscanner\AppData\Local\Microsoft\MSIP\Scanner\Reports```.

	> If needed, use the credentials below:
	>
	>```Contoso\LabUser```
	>
	>+++Pa$$w0rd+++

1. [] Review the summary txt and detailed csv files available there.  

	>[!Hint] Since there are no Automatic conditions configured yet, the scanner found no matches for the 141 files scanned despite 136 of them having sensitive data.
	>
	>!IMAGE[aukjn7zr.jpg](\Media\aukjn7zr.jpg)
	>
	>The details contained in the DetailedReport.csv can be used to identify the types of sensitive data you need to create AIP rules for in the Azure Portal.
	>
	>!IMAGE[9y52ab7u.jpg](\Media\9y52ab7u.jpg)

	>[!NOTE] We will revisit this information later in the lab to review discovered data and create Sensitive Data Type to Classification mappings.

===

# Defining Recommended and Automatic Conditions
[🔙](#azure-information-protection)

One of the most powerful features of Azure Information Protection is the ability to guide your users in making sound decisions around safeguarding sensitive data.  This can be achieved in many ways through user education or reactive events such as blocking emails containing sensitive data. 

However, helping your users to properly classify and protect sensitive data at the time of creation is a more organic user experience that will achieve better results long term.  In this task, we will define some basic recommended and automatic conditions that will trigger based on certain types of sensitive data.

1. [] Under **Dashboards** on the left, click on **Data discovery (Preview)** to view the results of the discovery scan we performed previously.

	!IMAGE[Dashboard.png](\Media\Dashboard.png)

	> [!KNOWLEDGE] Notice that there are no labeled or protected files shown at this time.  This uses the AIP P1 discovery functionality available with the AIP Scanner. Only the predefined Office 365 Sensitive Information Types are available with AIP P1 as Custom Sensitive Information Types require automatic conditions to be defined, which is an AIP P2 feature.

	> [!NOTE] Now that we know the sensitive information types that are most common in this environment, we can use that information to create **Recommended** conditions that will help guide user behavior when they encounter this type of data.

1. [] Under **Classifications** on the left, click **Labels** then expand **Confidential**, and click on **All Employees**.

	^IMAGE[Open Screenshot](\Media\jyw5vrit.jpg)
1. [] In the Label: All Employees blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	!IMAGE[cws1ptfd.jpg](\Media\cws1ptfd.jpg)
1. [] In the Condition blade, in the **Select information types** search box, type ```EU``` and check the boxes next to the **items shown below**.

	!IMAGE[xaj5hupc.jpg](\Media\xaj5hupc.jpg)

1. [] Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\41o5ql2y.jpg)
1. [] In the Labels: All Employees blade, in the **Configure conditions for automatically applying this label** section, click **Automatic**.

1. [] Click **Save** in the Label: All Employees blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\rimezmh1.jpg)
1. [] Press the **X** in the upper right-hand corner to close the Label: All Employees blade.

	^IMAGE[Open Screenshot](\Media\em124f66.jpg)
1. [] Next, expand **Highly Confidential** and click on the **All Employees** sub-label.

	^IMAGE[Open Screenshot](\Media\2eh6ifj5.jpg)
1. [] In the Label: All Employees blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	^IMAGE[Open Screenshot](\Media\8cdmltcj.jpg)
1. [] In the Condition blade, in the search bar type ```credit``` and check the box next to **Credit Card Number**.

	^IMAGE[Open Screenshot](\Media\9rozp61b.jpg)
1. [] Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\ie6g5kta.jpg)
1. [] In the Labels: All Employees blade, in the **Configure conditions for automatically applying this label** section, click **Automatic**.

	!IMAGE[245lpjvk.jpg](\Media\245lpjvk.jpg)
	> [!HINT] The policy tip is automatically updated when you switch the condition to Automatic.
1. [] Click **Save** in the Label: All Employees blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\gek63ks8.jpg)
1. [] Press the **X** in the upper right-hand corner to close the Label: All Employees blade.

	^IMAGE[Open Screenshot](\Media\wzwfc1l4.jpg)

===

# Exercise 3A: Security and Compliance Center
[🔙](#azure-information-protection)

In this exercise, we will migrate your AIP Labels and activate them in the Security and Compliance Center.  This will allow you to see the labels in Microsoft Information Protection based clients such as Office 365 for Mac and Mobile Devices.

Although we will not be demonstrating these capabilities in this lab, you can use the tenant information provided to test on your own devices.
 

===
# Activating Unified Labeling
[🔙](#azure-information-protection)
 
In this task, we will activate the labels from the Azure Portal for use in the Security and Compliance Center.

1. [] On @lab.VirtualMachine(Client01).SelectLink, navigate to ```https://portal.azure.com/?ActivateMigration=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/migrationActivationBlade```

1. [] Click **Activate** and **Yes**.

	!IMAGE[o0ahpimw.jpg](\Media\o0ahpimw.jpg)

	>[!NOTE] You should see a message similar to the one below.
	>
	> !IMAGE[SCCMigration.png](\Media\SCCMigration.png) 

1. [] In a new tab, browse to ```https://protection.office.com/``` and click on **Classifications** and **Labels** to review the migrated labels. 

	>[!NOTE] Keep in mind that now the SCC Sensitivity Labels have been activated, so any modifications, additions, or deletions will be syncronised to Azure Information Protection in the Azure Portal. There are some functional differences between the two sections (DLP in SCC, HYOK & Custom Permissions in AIP), so please be aware of this when modifying policies to ensure a consistent experience on clients. 
===
# Exercise 4A: Bulk Classification with the AIP Client

In this task, we will perform bulk classification using the built-in functionality of the AIP Client.  This can be useful for users that want to classify/protect many documents that exist in a central location or locations identified by scanner discovery.  Because this is done manually, it is an AIP P1 feature.

1. [] On @lab.VirtualMachine(Scanner01).SelectLink, browse to the **C:\\**.
2. [] Right-click on the PII folder and select **Classify and Protect**.
   
   !IMAGE[CandP.png](\Media\CandP.png)
1. [] When prompted, click use another user and use the credentials below to authenticate:

	```AIPScanner@@lab.CloudCredential(17).TenantName```

	```Somepass1```

1. [] In the AIP client Classify and protect interface, select **Highly Confidential\\All Employees** and press **Apply**. 

	!IMAGE[CandP2.png](\Media\CandP2.png)

> [!NOTE] You may review the results in a text file by clicking show results, or simply close the window.
===

# Exercise 5A: Classification, Labeling, and Protection with the Azure Information Protection Scanner
[🔙](#azure-information-protection)

The Azure Information Protection scanner allows you to  classify and protect sensitive information stored in on-premises CIFS file shares and SharePoint sites.  

In this exercise, you will change the condition we created previously from a recommended to an automatic classification rule.  After that, we will run the AIP Scanner in enforce mode to classify and protect the identified sensitive data.

===

# Enforcing Configured Rules
[🔙](#azure-information-protection)
 
In this task, we will set the AIP scanner to enforce the conditions we set up in the previous task and have it run on all files using the Start-AIPScan command.

1. [] Switch to @lab.VirtualMachine(Scanner01).SelectLink
1. [] Run the commands below to run an enforced scan using defined policy.

    ```
	Set-AIPScannerConfiguration -Enforce On -DiscoverInformationTypes PolicyOnly
	```
	```
	Start-AIPScan
    ```

	> [!HINT] Note that this time we used the DiscoverInformationTypes -PolicyOnly switch before starting the scan. This will have the scanner only evaluate the conditions we have explicitly defined in conditions.  This increases the effeciency of the scanner and thus is much faster.  After reviewing the event log we will see the result of the enforced scan.
	>
	>!IMAGE[k3rox8ew.jpg](\Media\k3rox8ew.jpg)
	>
	>If we switch back to @lab.VirtualMachine(Client01).SelectLink and look in the reports directory we opened previously at ```\\Scanner01.contoso.azure\c$\users\aipscanner\AppData\Local\Microsoft\MSIP\Scanner\Reports```, you will notice that the old scan reports are zipped in the directory and only the most recent results aare showing.  
	>
	> If needed, use the credentials below:
	>
	>```Contoso\LabUser```
	>
	>+++Pa$$w0rd+++
	>
	>!IMAGE[s8mn092f.jpg](\Media\s8mn092f.jpg)
	>
	>Also, the DetailedReport.csv now shows the files that were protected.
	>
	>
	>!IMAGE[6waou5x3.jpg](\Media\6waou5x3.jpg)
	>
	>^IMAGE[Open Fullscreen](6waou5x3.jpg)

===

# Reviewing Protected Documents
[🔙](#azure-information-protection)

Now that we have Classified and Protected documents using the scanner, we can review the documents we looked at previously to see their change in status.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink.
 
2. [] Navigate to ```\\Scanner01.contoso.azure\documents```. 

	> If needed, use the credentials below:
	>
	>```Contoso\LabUser```
	>
	>+++Pa$$w0rd+++
 
	^IMAGE[Open Screenshot](\Media\hipavcx6.jpg)
3. [] Open one of the Contoso Purchasing Permissions documents or Run For The Cure spreadsheets.
 
 	
	
	> [!NOTE] Observe that the same document is now classified as Confidential \ All Employees. 
	>
	>!IMAGE[s1okfpwu.jpg](\Media\s1okfpwu.jpg)
===
# Reviewing the Dashboards

We can now go back and look at the dashboards and observe how they have changed.

1. [] On @lab.VirtualMachine(Client01).SelectLink, open the browser that is logged into the Azure Portal.

1. [] Under **Dashboards**, click on **Usage report (Preview)**.

	> [!NOTE] Observe that there are now entries from the AIP scanner, and File Explorer based on our activities in this lab. You may not see details of label data right away as this takes longer to process.  I have included a screenshot of the results below, but you may check back later in the lab to see the full results.
	>
	> !IMAGE[Usage.png](\Media\Usage.png)
	>
	> !IMAGE[Usage2.png](\Media\Usage2.png)
2. [] Next, under dashboards, click on **Data discovery (Preview)**.

	> [!NOTE] As mentioned above, label data may not show up initially but you should start seeing protection data in the portal.  I have included a screenshot of the final result so please check back throughout the lab to see the label data from the AIP scanner.
	>
	> !IMAGE[Discovery.png](\Media\Discovery.png)
	> 
	> !IMAGE[discovery2.png](\Media\discovery2.png)
	
===
# Exercise 6A: Exchange Online IRM Capabilities
[🔙](#azure-information-protection)

Exchange Online can work in conjunction with Azure Information Protection to provide advanced capabilities for protecting sensitive data being sent over email.  You can also manage the flow of classified content to ensure that it is not sent to unintended recipients.  

## Configuring Exchange Online Mail Flow Rules

In this task, we will configure a mail flow rule to detect sensitive information traversing the network in the clear and encrypt it using the Encrypt Only RMS Template.  We will also create a mail flow rule to prevent messages classified as Confidential \ All Employees from being sent to external recipients.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and open an **Admin PowerShell Prompt**.

2. [] Type the commands below to connect to an Exchange Online PowerShell session.  Use the credentials provided when prompted.

	```
	Set-ExecutionPolicy RemoteSigned
	```

	```
	$UserCredential = Get-Credential
	```

	```@lab.CloudCredential(17).Username```

	```@lab.CloudCredential(17).Password```

	```
	$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://outlook.office365.com/powershell-liveid/ -Credential $UserCredential -Authentication Basic -AllowRedirection
	Import-PSSession $Session
	```

1. [] Create a new Exchange Online Mail Flow Rule using the code below:

	```
	New-TransportRule -Name "Encrypt external mails with sensitive content" -SentToScope NotInOrganization -ApplyRightsProtectionTemplate "Encrypt" -MessageContainsDataClassifications @(@{Name="ABA Routing Number"; minCount="1"},@{Name="Credit Card Number"; minCount="1"},@{Name="Drug Enforcement Agency (DEA) Number"; minCount="1"},@{Name="International Classification of Diseases (ICD-10-CM)"; minCount="1"},@{Name="International Classification of Diseases (ICD-9-CM)"; minCount="1"},@{Name="U.S. / U.K. Passport Number"; minCount="1"},@{Name="U.S. Bank Account Number"; minCount="1"},@{Name="U.S. Individual Taxpayer Identification Number (ITIN)"; minCount="1"},@{Name="U.S. Social Security Number (SSN)"; minCount="1"})
	```

	>[!KNOWLEDGE] This mail flow rule can be used to encrypt sensitive data leaving via email.  This can be customized to add additional sensitive data types. A breakdown of the command is listed below.
	>
	>New-TransportRule 
	>
	>-Name "Encrypt external mails with sensitive content" 
	>
	>-SentToScope NotInOrganization 
	>
	>-ApplyRightsProtectionTemplate "Encrypt" 
	>
	>-MessageContainsDataClassifications @(@{Name="ABA Routing Number"; minCount="1"},@{Name="Credit Card Number"; minCount="1"},@{Name="Drug Enforcement Agency (DEA) Number"; minCount="1"},@{Name="International Classification of Diseases (ICD-10-CM)"; minCount="1"},@{Name="International Classification of Diseases (ICD-9-CM)"; minCount="1"},@{Name="U.S. / U.K. Passport Number"; minCount="1"},@{Name="U.S. Bank Account Number"; minCount="1"},@{Name="U.S. Individual Taxpayer Identification Number (ITIN)"; minCount="1"},@{Name="U.S. Social Security Number (SSN)"; minCount="1"})
	
	> [!HINT] Next, we need to capture the **Label ID** for the **Confidential \ All Employees** label. 

1. [] Switch to the Azure Portal and under **Classifications** click on Labels, then expand **Confidential** and click on **All Employees**.

	!IMAGE[w2w5c7xc.jpg](\Media\w2w5c7xc.jpg)

	> [!HINT] If you closed the azure portal, open an Edge InPrivate window and navigate to ```https://portal.azure.com```.

1. [] In the Label: All Employees blade, scroll down to the Label ID and **copy** the value.

	!IMAGE[lypurcn5.jpg](\Media\lypurcn5.jpg)

	> [!ALERT] Make sure that there are no spaces before or after the Label ID as this will cause the mail flow rule to be ineffective.

1. [] Next, return to the PowerShell window and type ```$labelid = "``` then paste the **LabelID** for the **All Employees** label, type ```"```, and press **Enter**.

    >[!NOTE] The full command should look like **$labelid = "Label ID GUID"**
1. [] Now, create another Exchange Online Mail Flow Rule using the code below:

	```
	$labeltext = "MSIP_Label_"+$labelid+"_enabled=true"
	New-TransportRule -name "Block Confidential All Employees" -SentToScope notinorganization -HeaderContainsMessageHeader  "msip_labels" -HeaderContainsWord $labeltext -RejectMessageReasonText “All Employees messages cannot be sent to external recipients.”
	```

	>[!KNOWLEDGE] This mail flow rule can be used to prevent internal only communications from being sent to an external audience.
	>
	>New-TransportRule 
	>
	>-name "Block Confidential All Employees" 
	>
	>-SentToScope notinorganization 
	>
	>-HeaderContainsMessageHeader "msip_labels" 
	>
	>-HeaderContainsWord $labeltext 
	>
	>-RejectMessageReasonText “All Employees messages cannot be sent to external recipients.”

	>[!NOTE] In a production environment, customers would want to create a rule like this for each of their labels that they did not want going externally.
===

# Demonstrating Exchange Online Mail Flow Rules
[🔙](#azure-information-protection)

In this task, we will send emails to demonstrate the results of the Exchange Online mail flow rules we configured in the previous task.  This will demonstrate some ways to protect your sensitive data and ensure a positive user experience with the product.

1. [] Switch to @lab.VirtualMachine(Client03).SelectLink.
1. [] In Microsoft Outlook, click on the **New email** button.

	^IMAGE[Open Screenshot](\Media\6wan9me1.jpg)

1. [] Send an email to Adam Smith, Alice Anderson, and yourself (```Adam Smith;Alice Anderson;@lab.User.Email```).  For the **Subject**, type ```Test Credit Card Email``` and for the **Body**, type ```My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368```, then click **Send**.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and review the received email.

	!IMAGE[pidqfaa1.jpg](\Media\pidqfaa1.jpg)

	> [!Knowledge] Note that there is no encryption applied to the message.  That is because we set up the rule to only apply to external recipients.  If you were to leave that condition out of the mail flow rule, internal recipients would also receive an encrypted copy of the message.  The image below shows the encrypted message that was received externally.
	>
	>!IMAGE[c5foyeji.jpg](\Media\c5foyeji.jpg)
	>
	>Below is another view of the same message received in Outlook Mobile on an iOS device.
	>
	>!IMAGE[599ljwfy.jpg](\Media\599ljwfy.jpg)

1. [] Next, in Microsoft Outlook, click on the **New email** button.

	^IMAGE[Open Screenshot](\Media\6wan9me1.jpg)
1. [] Send an email to Adam Smith, Alice Anderson, and yourself (```Adam Smith;Alice Anderson;@lab.User.Email```).  For the **Subject** and **Body** type ```Another Test All Employees Email```.

	^IMAGE[Open Screenshot](\Media\d476fmpg.jpg)

1. [] In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	^IMAGE[Open Screenshot](\Media\901v6vpa.jpg)

1. [] Click on **Confidential** and then **All Employees** and click **Send**.

	^IMAGE[Open Screenshot](\Media\yhokhtkv.jpg)
1. [] In about a minute, you should receive an **Undeliverable** message from Exchange with the users that the message did not reach and the message you defined in the previous task.

	!IMAGE[kgjvy7ul.jpg](\Media\kgjvy7ul.jpg)

> [!HINT] There are many other use cases for Exchange Online mail flow rules but this should give you a quick view into what is possible and how easy it is to improve the security of your sensitive data through the use of Exchange Online mail flow rules and Azure Information Protection.

===
# Exercise 7A: SharePoint IRM Configuration
[🔙](#azure-information-protection)

In this exercise, you will configure SharePoint Online Information Rights Management (IRM) and configure a document library with an IRM policy to protect documents that are downloaded from that library.

===
# Enable Information Rights Management in SharePoint Online
[🔙](#azure-information-protection)
 
In this task, we will enable Information Rights Management in SharePoint Online.

1. [] Switch to @lab.VirtualMachine(Client03).SelectLink, click @lab.CtrlAltDelete and use the credentials below to log in.

	+++LabUser+++

	+++Pa$$w0rd+++

1. [] Launch an Edge InPrivate session to ```https://admin.microsoft.com/AdminPortal/Home#/```.
 
1. [] If needed, log in using the credentials below:

	 ```@lab.CloudCredential(17).Username```
	 
	 ```@lab.CloudCredential(17).Password```
 
1. [] Hover over the **Admin centers** section of the bar on the left and choose **SharePoint**.

	!IMAGE[r5a21prc.jpg](\Media\r5a21prc.jpg)
 
1. [] In the SharePoint admin center click on **settings**.

1. [] Scroll down to the Information Rights Management (IRM) section and select the option button for **Use the IRM service specified in your configuration**.
 
1. [] Click the **Refresh IRM Settings** button.

	!IMAGE[1qv8p13n.jpg](\Media\1qv8p13n.jpg)

	>[!HINT] After the browser refreshes, you can scroll down to the same section and you will see a message stating **We successfully refreshed your setings.**
	>
	>!IMAGE[daeglgk9.jpg](\Media\daeglgk9.jpg)
1. [] Scroll down and click **OK**.
1. [] Next, navigate to ```https://admin.microsoft.com/AdminPortal/Home#/users```.
1. [] Click on **Nuck Chorris** and on the profile page, next to Roles, click **Edit**.

	!IMAGE[df6t9nk1.jpg](\Media\df6t9nk1.jpg)
1. [] On the Edit user roles page, select **Customized administrator**, check the box next to **SharePoint administrator**, and click **Save**.

	!IMAGE[3rj47ym9.jpg](\Media\3rj47ym9.jpg)
1. [] **Close the Edge InPrivate browser** window to **clear the credentials**.

 
===

# Site Creation and Information Rights Management Integration
[🔙](#azure-information-protection)
 
In this task, we will create a new SharePoint site and enable Information Rights Management in a document library.

1. [] Launch a new Edge InPrivate session to ```https://portal.office.com```.
1. [] Log in using the credentials below:

	```NuckC@@lab.CloudCredential(17).TenantName```

	```NinjaCat123```
1. [] Click on **SharePoint** in the list.

	!IMAGE[twsp6mvj.jpg](\Media\twsp6mvj.jpg)

1. [] Dismiss any introductory screens and, at the top of the page, click **+ Create site**.

	!IMAGE[7v8wctu2.jpg](\Media\7v8wctu2.jpg)

	[!NOTE] If you do not see the **+ Create site** button, resize the VM window by dragging the divider for the instructions to the right until the VM resizes and you can see the button.
 
1. [] On the Create a site page, click **Team site**.

	^IMAGE[Open Screenshot](\Media\406ah98f.jpg)
 
1. [] On the next page, type ```IRM Demo``` for **Site name** and for the **Site description**, type ```This is a team site for demonstrating SharePoint IRM capabilities``` and set the **Privacy settings** to **Public - anyone in the organization can access the site** and click **Next**.

	^IMAGE[Open Screenshot](\Media\ug4tg8cl.jpg)

1. [] On the Add group members page, click **Finish**.
1. [] In the newly created site, on the left navigation bar, click **Documents**.

	^IMAGE[Open Screenshot](\Media\yh071obk.jpg)
 
1. [] In the upper right-hand corner, click the **Settings icon** and click **Library settings**.

	!IMAGE[1qo31rp6.jpg](\Media\1qo31rp6.jpg)
 
1. [] On the Documents > Settings page, under **Permissions and Management**, click **Information Rights Management**.

	!IMAGE[ie2rmsk2.jpg](\Media\ie2rmsk2.jpg)
 
	>[!ALERT] It may take up to 10 minutes for the global IRM settings to apply to document libraries.  If this has not appeared after a few minutes, try creating a new document library to see if the link is available. 
 
1. [] On the Settings > Information Rights Management Settings page, check the box next to Restrict permissions on this library on download and under **Create a permission policy title** type ```Contoso IRM Policy```, and under **Add a permission policy description** type ```This content contained within this file is for use by Contoso Corporation employees only.```
 
	^IMAGE[Open Screenshot](\Media\m9v7v7ln.jpg)
1. [] Next, click on **SHOW OPTIONS** below the policy description and in the **Set additional IRM library settings** section, check the boxes next to **Do not allow users to upload documents that do not support IRM** and **Prevent opening documents in the browser for this Document Library**.

	!IMAGE[0m2qqtqn.jpg](\Media\0m2qqtqn.jpg)
	>[!KNOWLEDGE] These setting prevent the upload of documents that cannot be protected using Information Rights Managment (Azure RMS) and forces protected documents to be opened in the appropriate application rather than rendering in the SharePoint Online Viewer.
 
1. [] Next, under the **Configure document access rights** section, check the box next to **Allow viewers to run script and screen reader to function on downloaded documents**.

	!IMAGE[72fkz2ds.jpg](\Media\72fkz2ds.jpg)
	>[!HINT] Although this setting may reduce the security of the document, this is typically provided for accessibility purposes.
1. [] Finally, in the **Configure document access rights** section, check the box next to  **Users must verify their credentials using this interval (days)** and type ```7``` in the text box.

	!IMAGE[tt1quq3f.jpg](\Media\tt1quq3f.jpg)
1. [] At the bottom of the page, click **OK** to complete the configuration of the protected document library.
1. [] On the Documents > Settings page, in the left-hand navigation pane, click on **Documents** to return to the document library. section.
 
1. [] Leave the browser open and continue to the next task.
 
===

# Uploading Content to the Document Library
[🔙](#azure-information-protection)
 
Create an unprotected Word document, label it as Internal, and upload it to the document library. 

1. [] Launch **Microsoft Word**.
1. [] Create a new **Blank document**.

	>[!NOTE] Notice that by default the document is labeled as the unprotected classification **General**.
 
1. [] In the Document, type ```This is a test document```.
 
1. [] **Save** the document and **close Microsoft Word**.
1. [] Return to the IRM Demo protected document library and click on **Upload > Files**.

	!IMAGE[m95ixvv1.jpg](\Media\m95ixvv1.jpg)
1. [] Navigate to the location where you saved the document, select it and click **Open** to upload the file.
 
1. [] Next, minimize the browser window and right-click on the desktop. Hover over **New >** and click on **Microsoft Access Database**. Name the database ```BadFile```.

	!IMAGE[e3nxt4a2.jpg](\Media\e3nxt4a2.jpg)
1. [] Return to the document library and attempt to upload the file.

	>[!KNOWLEDGE] Notice that you are unable to upload the file because it cannot be protected.
	>	
	>!IMAGE[432hu3pi.jpg](\Media\432hu3pi.jpg)
===

# SharePoint IRM Functionality
[🔙](#azure-information-protection)
 
Files that are uploaded to a SharePoint IRM protected document library are protected upon download based on the user's access rights to the document library.  In this task, we will share a document with Alice Anderson and review the access rights provided.

1. [] Select the uploaded document and click **Share** in the action bar.

	!IMAGE[1u2jsod7.jpg](\Media\1u2jsod7.jpg)
1. [] In the Send Link dialog, type ```Alice``` and click on **Alice Anderson** then **Send**.

	!IMAGE[j6w1v4z9.jpg](\Media\j6w1v4z9.jpg)
1. [] Switch to @lab.VirtualMachine(Client02).SelectLink.
1. [] Open Outlook and click on the email from Nuck Chorris, then click on the **Open** link.

	^IMAGE[Open Screenshot](\Media\v39ez284.jpg)
1. [] This will launch the IRM Demo document library.  Click on the document to open it in Microsoft Word.

	!IMAGE[xmv9dmvk.jpg](\Media\xmv9dmvk.jpg)
1. [] After the document opens, you will be able to observe that it is protected.  Click on the View Permissions button to review the restrictions set on the document.

	!IMAGE[4uya6mro.jpg](\Media\4uya6mro.jpg)
	>[!NOTE] These permissions are based on the level of access that they user has to the document library.  In a production environment most users would likely have less rights than shown in this example.

===
# Lab Complete
Congratulations! You have completed the Azure Information Protection Hands on Lab. 
