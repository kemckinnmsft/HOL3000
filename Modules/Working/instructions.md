# Configuring Microsoft Information Protection Solutions to Protect Your Sensitive Data

## Introduction

Estimated time to complete this lab

60 minutes*

### Objectives

After completing this lab, you should be able to (depending on the exercises you choose):

- Discover sensitive data using the Azure Information Protection scanner  
- Configure Azure Information Protection labels
- Configure Azure Information Protection policies
- Classify and protect content with Azure Information Protection in Office applications
- Bulk Protect documents using the AIP client Windows integration
- Enable and Publish labels and policies in the Security and Compliance Center
- Classify and Protect sensitive data discovered by the AIP Scanner
- Monitor Usage, User Activity, and Data Risk using the Azure Log Analytics AIP dashboards
- Configure Exchange Online Mail Flow Rules to protect content and enhance AIP usability 

### Prerequisites

Before working on this lab, you must have:

- Familiarity using Windows 10
- Familiarity with PowerShell
- Familiarity with Office 365 applications

### Lab machine technology

This lab is designed to be completed on either a native Windows 10 machine or a VM with the following characteristics:

- Windows 10 Enterprise
- Office 365 ProPlus
- Azure Information Protection client (1.41.51.0)

Microsoft 365 E5 Tenant credentials will be provided during the event.  If you want to run through this lab after the event, you may use a tenant created through https://demos.microsoft.com or your own Microsoft 365 Tenant. This Lab Guide will be publicly available after the event at https://aka.ms/AIPHOL.

*:Note: Not all exercises can be completed within 60 minutes but time estimates are provided to aid in exercise selection.

---

===
# Lab Environment Configuration
[:arrow_left: Home](#introduction)

There are a few prerequisites that need to be set up to complete all the sections in this lab.  This Exercise will walk you through the items below.

- Azure AD User Configuration
- Azure Pass Redemption
- AIP Log Analytics Configuration

---
## Azure AD User Configuration

In this task, we will create new Azure AD users and assign licenses via PowerShell.  In a procduction evironment this would be done using Azure AD Connect or a similar tool to maintain a single source of authority, but for lab purposes we are doing it via script to reduce setup time.

1. [] Log into @lab.VirtualMachine(Scanner01).SelectLink using the password +++@lab.VirtualMachine(Client01).Password+++
2. [] Open a new Administrative PowerShell window and click below to type the code. 
   
    ```
    $cred = Get-Credential
    ```

3. [] When prompted, provide the credentials below:

	```@lab.CloudCredential(82).Username```

	```@lab.CloudCredential(82).Password``` 
   
1. [] In the PowerShell window, click on the code below to create users.

    ```
    # Store Tenant FQDN and Short name
    $tenantfqdn = "@lab.CloudCredential(82).TenantName"
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

	Start-Sleep -s 10

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

---
## Redeem Azure Pass
[:arrow_up: Top](#lab-environment-configuration)

For several of the exercises in this lab series, you will require an active subscription.  We are providing an Azure Pass for this purpose.  You will be provided with an Azure Pass code to use with the instructions below.

### Redeeming a Microsoft Azure Pass Promo Code:

1. [] Log into @lab.VirtualMachine(Client01).SelectLink using the password +++Pa$$w0rd+++
2. [] Right-click on **Edge** in the taskbar and click on **New InPrivate window**.

3. [] In the InPrivate window, navigate to ```https://www.microsoftazurepass.com```

4. [] Click the **Start** button to get started.

	!IMAGE[wdir7lb3.jpg](\Media\wdir7lb3.jpg)
1. [] Enter the credentials below and select **Sign In**.

	```@lab.CloudCredential(82).Username```

	```@lab.CloudCredential(82).Password``` 

	!IMAGE[gtg8pvp1.jpg](\Media\gtg8pvp1.jpg)
1. [] Click **Confirm** if the email address **@lab.CloudCredential(82).Username** is listed.

	!IMAGE[teyx280d.jpg](\Media\teyx280d.jpg)
7. [] **Click in the Promo code box** and type ```@lab.CloudCredential(215).PromoCode``` and click the **Claim Promo Code** button.

  !IMAGE[e1l35ko2.jpg](\Media\e1l35ko2.jpg)

    > [!NOTE] It may take up to 5 minutes to process the redemption.

8. [] Scroll to the bottom of the page and click **Next**.

  !IMAGE[ihrjazqi.jpg](\Media\ihrjazqi.jpg)

    > [!NOTE] You can keep the pre-populated information.

9. [] Check the box to agree to the terms and click **Sign up**.

  !IMAGE[k2a97g8e.jpg](\Media\k2a97g8e.jpg)

    > [!NOTE] It may take a few minutes to process the request.

1. [] Please wait until the process is complete and you should be in the Azure Portal.

---
## Configuring Azure Log Analytics

In order to collect log data from Azure Information Protection clients and services, you must first configure the log analytics workspace.

1. [] In the Azure portal, type the word ```info``` into the **search bar** and press **Enter**, then click on **Azure Information Protection**. 

	!IMAGE[2598c48n.jpg](\Media\2598c48n.jpg)
	
	> [!HINT] If you do not see the search bar at the top of the portal, click on the **Magnifying Glass** icon to expand it.
	>
	> !IMAGE[ny3fd3da.jpg](\Media\ny3fd3da.jpg)

1. [] In the Azure Information Protection blade, under **Manage**, click **Configure analytics (preview)**.

1. [] Next, click on **+ Create new workspace**.

	!IMAGE[qu68gqfd.jpg](\Media\qu68gqfd.jpg)
1. [] Configure the Log analytics workspace using the values in the table below and click **OK**.

	|||
	|-----|-----|
	|OMS Workspace|**Type a unique Workspace Name**|
	|Resource Group|```AIP-RG```|
	|Location|**East US** (Or a location near the event)|

	^IMAGE[Open Screenshot](\Media\5butui15.jpg)
1. [] Next, back in the Configure analytics (preview) blade, **check the boxes** next to the **workspace** and next to **Enable Content Matches**, and click **OK**.

	!IMAGE[gste52sy.jpg](\Media\gste52sy.jpg)
1. [] Click **Yes**, in the confirmation dialog.

	!IMAGE[zgvmm4el.jpg](\Media\zgvmm4el.jpg)

---

===
# Azure Information Protection
[:arrow_left: Home](#introduction)
## Overview

Azure Information Protection (AIP) is a cloud-based solution that can help organizations to protect sensitive information by classifying and (optionally) encrypting documents and emails on Windows, Mac, and Mobile devices. This is done using an organization defined classification taxonomy made up of labels and sub-labels. These labels may be applied manually by users, or automatically by administrators via defined rules and conditions.

The phases of AIP are shown in the graphic below.  

!IMAGE[Phases.png](\Media\Phases.png)

In this lab, we will give you options for addressing each of these phases using various features of AIP.  

The [AIP Scanner Discovery](#aip-scanner-discovery) exercise, will guide you through performing **Discovery using the AIP scanner**. We recommend that everyone complete this exercise first as this step is important to help show the current state of sensitive data in on-premises repositories. This enables you to make data based risk decisions that can help drive appropriate levels of urgency around the rest of your AIP deployment. :clock10: 10-15 min

The [Base Configuration](#base-configuration) exercise, contains information on configuring and testing **Global and Scoped Policy and Labels**. This will also include demonstrating **Recommended and Automatic labeling via the AIP client in Office 365 on Windows 10**. This is the longest exercise in the lab as it requires configuration of policy and the use of multiple clients.  We recommend this exercise if you have minimal experience with AIP. :clock10: 30-45 min

The [Bulk Classification](#bulk-classification) exercise, shows how to manually classify, label, and protect content using the Windows integration features of the AIP client. :clock10: 5 min

The [AIP Scanner Classification, Labeling, and Protection](#aip-scanner-classification-labeling-and-protection) exercise, will show how to use the **AIP scanner in Enforce mode** to take advantage of features like Automatic Conditions to help you **Classify, Label, and Protect** the discovered information easily. This exercise has a dependancy on completion of the AIP Scanner Dicovery exercise. :clock10: 5-10 min

The [Security and Compliance Center](#security-and-compliance-center) exercise, will help you understand how to **Enable and Publish labels in the Security and Compliance Center** so they can be used with Mac, Mobile, ISVs (like Adobe PDF), and other unified clients.  We will demonstrate this functionality using the Adobe PDF reader. :clock10: 5-10 min

In the [AIP Analytics Dashboards](#aip-analytics-dashboards) exercise, we will show how to **Monitor AIP Usage, User Activity, and Data Risk** using the new Azure Log Analytics dashboards built into the AIP Azure Portal.

In the [Exchange IRM](#exchange-online-irm-capabilities) exercise, we will use Exchange PowerShell to create a Mail Flow Rule to prevent sensitive information from leaving your network in the clear.  We will also create a mail flow rule that prevents internal protected messages from accidentally being sent to external recipients who will be unable to open the content. :clock10: 10-15 min

Click on one of the options below to begin. At the end of each section, there will be a summary and links to the other sections so you may continue from that point.

- [AIP Scanner Discovery](#aip-scanner-discovery)
- [Base Configuration](#base-configuration)
- [Bulk Classification](#bulk-classification)
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection)
- [Security and Compliance Center](#security-and-compliance-center)
- [AIP Analytics Dashboards](#aip-analytics-dashboards)
- [Exchange IRM](#exchange-online-irm-capabilities)

---

===
# AIP Scanner Discovery
[:arrow_left: Home](#azure-information-protection)

Even before configuring an AIP classification taxonomy, customers can scan and identify files containing sensitive information based on the built-in sensitive information types included in the Microsoft Classification Engine.  

!IMAGE[ahwj80dw.jpg](\Media\ahwj80dw.jpg)

Often, this can help drive an appropriate level of urgency and attention to the risk customers face if they delay rolling out AIP classification and protection.  

In this exercise, we will install the AIP scanner and run it against repositories in discovery mode.  Later in this lab (after configuring labels and conditions), we will revisit the scanner to perform automated classification, labeling, and protection of sensitive documents. This Exercise will walk you through the items below.

- [Installing the AIP Scanner Service](#installing-the-aip-scanner-service)
- [Creating Azure AD Applications for the AIP Scanner](#creating-azure-ad-applications-for-the-aip-scanner)

---
## AIP Scanner Setup

In this task we will install the AIP scanner binaries and create the Azure AD Applications necessary for authentication.

### Installing the AIP Scanner Service

The first step in configuring the AIP Scanner is to install the service and connect the database.  This is done with the Install-AIPScanner cmdlet that is provided by the AIP Client software.  The AIPScanner service account has been pre-staged in Active Directory for convenience.

1. [] Switch to @lab.VirtualMachine(Scanner01).SelectLink and log in using the password +++@lab.VirtualMachine(Client01).Password+++.

1. [] At the Administrative PowerShell prompt, type the code below 
   
	```
	$SQL = "Scanner01"
	Install-AIPScanner -SQLServerInstance $SQL
	
	```
3. [] When prompted, provide the credentials for the **local** AIP scanner service account.
	
	```Contoso\AIPScanner```

	```Somepass1```

	^IMAGE[Open Screenshot](\Media\pc9myg9x.jpg)

	> [!knowledge] You should see a success message like the one below. 
	>
	>!IMAGE[w7goqgop.jpg](\Media\w7goqgop.jpg)

### Creating Azure AD Applications for the AIP Scanner

Now that you have installed the scanner bits, you need to get an Azure AD token for the scanner service account to authenticate so that it can run unattended. This requires registering both a Web app and a Native app in Azure Active Directory.  The commands below will do this in an automated fashion rather than needing to go into the Azure portal directly.

1. [] In the same PowerShell window, run ```Connect-AzureAD``` and use the username and password below. 
	
	```@lab.CloudCredential(82).Username```
	
	```@lab.CloudCredential(82).Password```
1. [] Next, click the **T** to **type the commands below** in the PowerShell window and press **Enter**. 

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

    >[!NOTE] This will create a new Web App Registration, Native App Registration, and associated Service Principals in Azure AD.

1. [] Finally, we will output the Set-AIPAuthentication command by running the commands below and pressing **Enter**.
   
	
    ```
    "Set-AIPAuthentication -WebAppID " + $WebApp.AppId + " -WebAppKey " + $WebAppKey.Guid + " -NativeAppID " + $NativeApp.AppId | Out-File ~\Desktop\Set-AIPAuthentication.txt
	Start ~\Desktop\Set-AIPAuthentication.txt
	```

1. [] Leave the notepad window open in the background.
1. [] **Click on the Start menu** and type ```PowerShell```, right-click on the PowerShell program, and click **Run as a different user**.

	!IMAGE[zgt5ikxl.jpg](\Media\zgt5ikxl.jpg)

1. [] When prompted, enter the username and password below and click **OK**.

	```Contoso\AIPScanner``` 

	```Somepass1```

1. [] Restore the **Notepad** window and copy the **full Set-AIPAuthentication** command into this window and run it.
1. [] When prompted, enter the username and password below:

	```AIPScanner@@lab.CloudCredential(82).TenantName```

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
   
   > [!Note] Leave the PowerShell window open for the next task

---

## Configuring Repositories 

In this task, we will configure repositories to be scanned by the AIP scanner.  As previously mentioned, these can be any type of CIFS file shares including NAS devices sharing over the CIFS protocol.  Additionally, On premises SharePoint 2010, 2013, and 2016 document libraries and lists (attachements) can be scanned.  You can even scan entire SharePoint sites by providing the root URL of the site.  There are several optional 

> [!NOTE] SharePoint 2010 is only supported for customers who have extended support for that version of SharePoint.

The next task is to configure repositories to scan.  These can be on-premises SharePoint 2010, 2013, or 2016 document libraries and any accessible CIFS based share.

1. [] In the Administrative PowerShell window on Scanner01, run the commands below

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

---

## Running Sensitive Data Discovery 
[:arrow_up: Top](#configuring-aip-scanner-for-discovery)

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

	>[!NOTE] You will see an event like the one below when the scanner completes the cycle. If you see a .NET exception, press OK. This is due to SharePoint startup in the VM environment.
	>
	>!IMAGE[agnx2gws.jpg](\Media\agnx2gws.jpg)

1. [] Next, switch to @lab.VirtualMachine(Client01).SelectLink and log in using the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] Open a **File Explorer** window, and browse to ```\\Scanner01.contoso.azure\c$\users\aipscanner\AppData\Local\Microsoft\MSIP\Scanner\Reports```. Use the credentials below to authenticate:

	```Contoso\LabUser```
	
	```Pa$$w0rd```

1. [] Review the summary txt and detailed csv files available there.  

	>[!Hint] Since there are no Automatic conditions configured yet, the scanner found no matches for the files scanned despite most of them containing sensitive data.
	>
	>!IMAGE[aukjn7zr.jpg](\Media\aukjn7zr.jpg)
	>
	>The details contained in the DetailedReport.csv can be used to identify the types of sensitive data you need to create AIP rules for in the Azure Portal.
	>
	>!IMAGE[9y52ab7u.jpg](\Media\9y52ab7u.jpg)

	>[!NOTE] We will revisit this information later in the lab to review discovered data and create Sensitive Data Type to Classification mappings.

	>[!ALERT] If you see any failures, it is likely due to SharePoint startup in the VM environment.  If you rerun Start-AIPScan on Scanner01 all files will successfully scan.  This should not happen in a production environment.
	
===
# AIP Scanner Discovery Exercise Complete

In this exercise, we installed the AIP scanner and performed a discovery scan against an on premises CIFS repository and SharePoint document library.  Although this was a very limited demonstration of the capabilities of the AIP scanner for discovery, it helps to show how quickly you can configure this tool and get actionable information which can be used to make data driven decisions about your security posture.  Choose one of the exercises below or click the Next button to continue sequentially.

- [Base Configuration](#configuring-azure-information-protection-policy) :clock10: 30-45 min
- [Bulk Classification](#bulk-classification) :clock10: 5 min
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection) :clock10: 5-10 min
- [Security and Compliance Center](#security-and-compliance-center) :clock10: 5-10 min
- [AIP Analytics Dashboards](#aip-analytics-dashboards) :clock10: 5-10 min
- [Exchange IRM](#exchange-online-irm-capabilities) :clock10: 10-15 min

---

===
# Base Configuration
[:arrow_left: Home](#azure-information-protection)

This exercise demonstrates using the Azure Information Protection blade in the Azure portal to configure policies and sub-labels.  We will create a new sub-label and configure protection and then modify an existing sub-label.  We will also create a label that will be scoped to a specific group.  

Next, we will configure AIP Global Policy to use the General sub-label as default, and finally, we will configure a scoped policy to use the new scoped label by default for Word, Excel, and PowerPoint while still using General as default for Outlook. This Exercise will walk you through the items below.


- [Creating, Configuring, and Modifying Sub-Labels](#creating-configuring-and-modifying-sub-labels)
- [Configuring Global Policy](#configuring-global-policy)
- [Creating a Scoped Label and Policy](#creating-a-scoped-label-and-policy)
- [Configuring Advanced Policy Settings](#configuring-advanced-policy-settings)
- [Defining Recommended and Automatic Conditions](#defining-recommended-and-automatic-conditions)

---
## Creating, Configuring, and Modifying Sub-Labels

In this task, we will configure a label protected for internal audiences that can be used to help secure sensitive data within your company.  By limiting the audience of a specific label to only internal employees, you can dramatically reduce the risk of unintentional disclosure of sensitive data and help reduce the risk of successful data exfiltration by bad actors.  

However, there are times when external collaboration is required, so we will configure a label to match the name and functionality of the Do Not Forward button in Outlook.  This will allow users to more securely share sensitive information outside the company to any recipient.  By using the name Do Not Forward, the functionality will also be familiar to what previous users of AD RMS or Azure RMS may have used in the past.

1. [] On @lab.VirtualMachine(Client01).SelectLink, log in with the password +++@lab.VirtualMachine(Client01).Password+++.
2. [] In the Azure Information Protection blade, under **Classifications** in the left pane, click on **Labels** to load the Azure Information Protection – Labels blade.

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

---

## Configuring Global Policy
[:arrow_up: Top](#configuring-azure-information-protection-policy)

In this task, we will assign the new sub-label to the Global policy and configure several global policy settings that will increase Azure Information Protection adoption among your users and reduce ambiguity in the user interface.

1. [] In the Azure Information Protection blade, under **Classifications** on the left, click **Policies**. 
2. [] Click the **Global** policy.

	^IMAGE[Open Screenshot](\Media\24qjajs5.jpg)

1. [] In the Policy: Global blade, **wait for the labels to load**.

	>[!knowledge] The policies should look like the image below.  If they show as loading, refresh the full browser on this page and go back into the **Global** policy and they should load.
	>
	>!IMAGE[labels.png](\Media\labels.png)

2. [] Below the labels, click **Add or remove labels**.

3. [] In the Policy: Add or remove labels blade, ensure that the **boxes** next to **all labels including the new Contoso Internal label** are **checked** and click **OK**.

4. [] In the Policy: Global blade, under the **Configure settings to display and apply on Information Protection end users** section, configure the policy to match the settings shown in the table and image below.

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

---

## Creating a Scoped Label and Policy
[:arrow_up: Top](#configuring-azure-information-protection-policy)

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

---

## Configuring Advanced Policy Settings
[:arrow_up: Top](#configuring-azure-information-protection-policy)

There are many advanced policy settings that are useful to tailor your Azure Information Protection deployment to the needs of your environment.  In this task, we will cover one of the settings that is very complimentary when using scoped policies that have no default label or a protected default label.  Because the No Default Label Scoped Policy we created in the previous task uses a protected default label, we will be adding an alternate default label for Outlook to provide a more palatable user experience for those users.

1. [] In the Azure Information Protection blade, under **Classifications** on the left, click on **Labels** and then click on the **General** label.

    ^IMAGE[Open Screenshot](\Media\rvn4xorx.jpg)

1. [] In the Label: General blade, scroll to the bottom and copy the **Label ID** and close the blade using the **X** in the upper right-hand corner.

    !IMAGE[8fi1wr4d.jpg](\Media\8fi1wr4d.jpg)

1. [] In the AIP Portal, under **Classifications** on the left, click on **Policies**. 
1. [] **Right-click** on the **No Default Label Scoped Policy** and click on **Advanced settings**.

    ^IMAGE[Open Screenshot](\Media\2jo71ugb.jpg)

1. [] In the Advanced settings blade, in the textbox under **VALUE**, paste the **Label ID** for the **General** label you copied previously. In the textbox under **NAME**, type ```OutlookDefaultLabel```, then click **Save and close**.

    > [!ALERT] CAUTION: Please check to ensure that there are **no spaces** before or after the **Label ID** when pasting as this will cause the setting to not apply.

    !IMAGE[ezt8sfs3.jpg](\Media\ezt8sfs3.jpg)

	> [!HINT] This and additional Advanced Policy Settings can be found at [https://docs.microsoft.com/en-us/azure/information-protection/rms-client/client-admin-guide-customizations ](https://docs.microsoft.com/en-us/azure/information-protection/rms-client/client-admin-guide-customizations)

---

## Defining Recommended and Automatic Conditions
[:arrow_up: Top](#configuring-azure-information-protection-policy)

One of the most powerful features of Azure Information Protection is the ability to guide your users in making sound decisions around safeguarding sensitive data.  This can be achieved in many ways through user education or reactive events such as blocking emails containing sensitive data. 

However, helping your users to properly classify and protect sensitive data at the time of creation is a more organic user experience that will achieve better results long term.  In this task, we will define some basic recommended and automatic conditions that will trigger based on certain types of sensitive data.

1. [] Under **Dashboards** on the left, click on **Data discovery (Preview)** to view the results of the discovery scan we performed previously.

	!IMAGE[Dashboard.png](\Media\Dashboard.png)

	> [!KNOWLEDGE] Notice that there are no labeled or protected files shown at this time.  This uses the AIP P1 discovery functionality available with the AIP Scanner. Only the predefined Office 365 Sensitive Information Types are available with AIP P1 as Custom Sensitive Information Types require automatic conditions to be defined, which is an AIP P2 feature.

	> [!NOTE] Now that we know the sensitive information types that are most common in this environment, we can use that information to create **Recommended** conditions that will help guide user behavior when they encounter this type of data.

	> [!ALERT] If no data is shown, it may still be processing. Continue with the lab and come back to see the results later.

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

---

===
# Testing AIP Policies
[:arrow_left: Home](#azure-information-protection)

Now that you have 3 test systems with users being affected by different policies configured, we can start testing these policies.  This exercise will run through various scenarios to demonstrate the use of AIP global and scoped policies and show the functionality of recommended and automatic labeling. This Exercise will walk you through the items below.

- [Exchange Mail Flow Rule Removal](#exchange-mail-flow-rule-removal)
- [Testing User Defined Permissions](#testing-user-defined-permissions)
- [Testing Global Policy](#testing-global-policy)
- [Testing Scoped Policy](#testing-scoped-policy)
- [Testing Recommended and Automatic Classification](#testing-recommended-and-automatic-classification)

> [!ALERT] If you see a warning about a metered connection in Office, click **Connect anyway** to allow Office to connect.  If you do not do this you will get errors when connecting to the AIP service. The VMs are set to metered to increase network speed.

---
## Exchange Mail Flow Rule Removal

By default, many of the demo tenants provided block external communications via mail flow rule.  As this will hinder many demos in this lab, we will verify if such a rule exists and remove it if necesary.

1. [] In the Admin PowerShell window, type the commands below to connect to an Exchange Online PowerShell session.  Use the credentials provided when prompted.

	```
	$cred = Get-Credential
	$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://outlook.office365.com/powershell-liveid/ -Credential $cred -Authentication Basic -AllowRedirection
	Import-PSSession $Session
	```

	> ```@lab.CloudCredential(82).Username```
	>
	> ```@lab.CloudCredential(82).Password```

3. [] Get the active Mail Flow Rules by typing the command below:

	```
	Get-TransportRule
	```

4. [] If a rule exists named something similar to **"Delete if sent outside the organization"**, run the code below to remove this rule.

	```
	Remove-TransportRule *Delete*
	```

---
## Testing User Defined Permissions
[:arrow_up: Top](#testing-aip-policies)

One of the most common use cases for AIP is the ability to send emails using User Defined Permissions (Do Not Forward). In this task, we will send an email using the Do Not Forward label to test that functionality.


1. [] On @lab.VirtualMachine(Client03).SelectLink, log in using the password +++@lab.VirtualMachine(Client01).Password+++.
2. [] Launch Microsoft Outlook, and click **Accept and start Outlook**.
3. [] In the username box, type ```EvanG@@lab.cloudcredential(82).TenantName``` and click **Connect**.
4. [] When prompted, type ```pass@word1``` and Sign in.
5. [] On the Use this account everywhere page, click **Yes** then click **Done**.
6. [] Once configuration completes, **uncheck the box** to **Set up Outlook Mobile** and click **OK**.
7. [] **Close Outlook** and **reopen** to complete activation.
8. [] Once Outlook opens, if you receive a metered connection warning, click **Connect anyway**.
9. [] Click on the **New email** button.

  !IMAGE[6wan9me1.jpg](\Media\6wan9me1.jpg)

	> [!KNOWLEDGE] Note that the **Sensitivity** is set to **General** by default.
	>
	> !IMAGE[5esnhwkw.jpg](\Media\5esnhwkw.jpg)

8. [] Send an email to **Adam Smith** and **Alice Anderson** (```Adam Smith;Alice Anderson```). You may **optionally add an external email address** (preferably from a major social provider like gmail, yahoo, or outlook.com) to test the external recipient experience. For the **Subject** and **Body** type ```Test Do Not Forward Email```.

  ^IMAGE[Open Screenshot](\Media\h0eh40nk.jpg)

9. [] In the Sensitivity Toolbar, click on **Confidential** and then the **Do Not Forward** sub-label and click **Send**.

  !IMAGE[w8j1w1lm.jpg](\Media\w8j1w1lm.jpg)

	> [!Knowledge] If you receive the error message below, click on the Confidential \ Contoso Internal sub-label to force the download of your AIP identity certificates, then follow the steps above to change the label to Confidential \ Do Not Forward.
	>
	> !IMAGE[6v6duzbd.jpg](\Media\6v6duzbd.jpg)

10. [] Switch over to @lab.VirtualMachine(Client01).SelectLink, log in using the password +++@lab.VirtualMachine(Client01).Password+++ and open Outlook. 
11. [] Run through setup, this time using the credentials ```AdamS@@lab.CloudCredential(82).TenantName``` and ```pass@word1```. 
12. [] Review the email in Adam Smith’s Outlook.  You will notice that the email is automatically shown in Outlook natively.

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

---

## Testing Global Policy
[:arrow_up: Top](#testing-aip-policies)

In this task, we will create a document and send an email to demonstrate the functionality defined in the Global Policy.

1. [] Switch to @lab.VirtualMachine(Client03).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] In Microsoft Outlook, click on the **New email** button.

	^IMAGE[Open Screenshot](\Media\6wan9me1.jpg)

1. [] Send an email to Adam Smith, Alice Anderson, and yourself (```Adam Smith;Alice Anderson;@lab.User.Email```).  For the **Subject** and **Body** type ```Test Contoso Internal Email```.

	^IMAGE[Open Screenshot](\Media\9gkqc9uy.jpg)

1. [] In the Sensitivity Toolbar, click on **Confidential** and then **Contoso Internal** and click **Send**.

  ^IMAGE[Open Screenshot](\Media\yhokhtkv.jpg)

1. [] On @lab.VirtualMachine(Client01).SelectLink, log in using the password +++@lab.VirtualMachine(Client01).Password+++ and observe that you are able to open the email natively in the Outlook client. Also observe the **header text** that was defined in the label settings.

	!IMAGE[bxz190x2.jpg](\Media\bxz190x2.jpg)
	
1. [] In your email, note that you will be unable to open this message.  This experience will vary depending on the client you use (the image below is from Outlook 2016 for Mac) but they should have similar messages after presenting credentials. Since this is not the best experience for the recipient, later in the lab we will configure Exchange Online Mail Flow Rules to prevent content classified with internal only labels from being sent to external users.
	
	!IMAGE[52hpmj51.jpg](\Media\52hpmj51.jpg)

---

## Testing Scoped Policy
[:arrow_up: Top](#testing-aip-policies)

In this task, we will create a document and send an email from one of the users in the Legal group to demonstrate the functionality defined in the first exercise. We will also show the behavior of the No Default Label policy on documents.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] In Microsoft Outlook, click on the **New email** button.
	
	^IMAGE[Open Screenshot](\Media\ldjugk24.jpg)
	
1. [] Send an email to Alice Anderson and Evan Green (```Alice Anderson;Evan Green```).  For the **Subject** and **Body** type ```Test Highly Confidential Legal Email```.
1. [] In the Sensitivity Toolbar, click on **Highly Confidential** and the **Legal Only** sub-label, then click **Send**.

	^IMAGE[Open Screenshot](\Media\ny1lwv0h.jpg)
1. [] Switch to @lab.VirtualMachine(Client02).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
11. [] Run through setup, this time using the credentials ```AliceA@@lab.CloudCredential(82).TenantName``` and ```pass@word1```. 
12. [] Review the email in Alice Anderson’s Outlook. You should be able to open the message natively in the client as Alice.

	!IMAGE[qeqtd2yr.jpg](\Media\qeqtd2yr.jpg)
1. [] Switch to @lab.VirtualMachine(Client03).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
2. [] Click on the email. You should be unable to open the message as Evan.

	!IMAGE[6y99u8cl.jpg](\Media\6y99u8cl.jpg)

	> [!Knowledge] You may notice that the Office 365 Message Encryption wrapper message is displayed in the preview pane.  It is important to note that the content of the email is not displayed here.  The content of the message is contained within the encrypted message.rpmsg attachment and only authorized users will be able to decrypt this attachment.
	>
	>!IMAGE[w4npbt49.jpg](\Media\w4npbt49.jpg)
	>
	>If an unauthorized recipient clicks on **Read the message** to go to the OME portal, they will be presented with the same wrapper message.  Like the external recipient from the previous task, this is not an ideal experience. So, you may want to use a mail flow rule to manage scoped labels as well.
	>
	>!IMAGE[htjesqwe.jpg](\Media\htjesqwe.jpg)

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] Open **Microsoft Word**.
1. [] Create a new **Blank document** and type ```This is a test document``` and **save the document**.

	> [!ALERT] When you click **Save**, you will be prompted to choose a classification.  This is a result of having **None** set as the default label in the scoped policy while requiring all documents to be labeled.  This is a useful for driving **active classification decisions** by specific groups within your organization.  Notice that Outlook still has a default of **General** because of the Advanced setting we added to the scoped policy.  **This is recommended** because user send many more emails each day than they create documents. Actively forcing users to classify each email would be an unpleasant user experience whereas they are typically more understanding of having to classify each document if they are in a sensitive department or role.

1. [] Choose a classification to save the document.

---

## Testing Recommended and Automatic Classification
[:arrow_up: Top](#testing-aip-policies)

In this task, we will test the configured recommended and automatic conditions we defined in Exercise 1.  Recommended conditions can be used to help organically train your users to classify sensitive data appropriately and provides a method for testing the accuracy of your dectections prior to switching to automatic classification.  Automatic conditions should be used after thorough testing or with items you are certain need to be protected. Although the examples used here are fairly simple, in production these could be based on complex regex statements or only trigger when a specific quantity of sensitive data is present.

1. [] Switch to @lab.VirtualMachine(Client03).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] Launch **Microsoft Word**.
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
   
---

===
# Base Configuration Exercise Complete

In this exercise, we walked through the configuration of Global and Scoped policies and labels.  We demonstrated the use of these and showed how recommended and automatic conditions function within the Office applications.  Choose one of the exercises below or click the Next button to continue sequentially.

- [AIP Scanner Discovery](#aip-scanner-discovery) :clock10: 10-15 min
- [Bulk Classification](#bulk-classification) :clock10: 5 min
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection) :clock10: 5-10 min
- [Security and Compliance Center](#security-and-compliance-center) :clock10: 5-10 min
- [AIP Analytics Dashboards](#aip-analytics-dashboards) :clock10: 5-10 min
- [Exchange IRM](#exchange-online-irm-capabilities) :clock10: 10-15 min

---

===
# Bulk Classification
[:arrow_left: Home](#azure-information-protection)

In this task, we will perform bulk classification using the built-in functionality of the AIP client.  This can be useful for users that want to classify/protect many documents that exist in a central location or locations identified by scanner discovery.  Because this is done manually, it is an AIP P1 feature.

1. [] On @lab.VirtualMachine(Scanner01).SelectLink, log in with the password +++@lab.VirtualMachine(Scanner01).Password+++.
2. [] Browse to the **C:\\**.
3. [] Right-click on the PII folder and select **Classify and Protect**.
   
   !IMAGE[CandP.png](\Media\CandP.png)
4. [] When prompted, click use another account and use the credentials below to authenticate:

	```AIPScanner@@lab.CloudCredential(82).TenantName```

	```Somepass1```

1. [] In the AIP client Classify and protect interface, select **Highly Confidential\\All Employees** and press **Apply**. 

	!IMAGE[CandP2.png](\Media\CandP2.png)

> [!Alert] If you are unable to see the **Apply** button due to screen resolution, click **Alt+A** and **Enter** to apply the label to the content.

> [!NOTE] You may review the results in a text file by clicking show results, or simply close the window.

---

===
# Bulk Classifiation Exercise Complete

In this exercise, we performed bulk classification using the built-in functionality of the AIP client.  This can be useful for users that want to classify/protect many documents that exist in a central location or locations identified by scanner discovery.  Choose one of the exercises below or click the Next button to continue sequentially.

- [AIP Scanner Discovery](#aip-scanner-discovery) :clock10: 10-15 min
- [Base Configuration](#configuring-azure-information-protection-policy) :clock10: 30-45 min
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection) :clock10: 5-10 min
- [Security and Compliance Center](#security-and-compliance-center) :clock10: 5-10 min
- [AIP Analytics Dashboards](#aip-analytics-dashboards) :clock10: 5-10 min
- [Exchange IRM](#exchange-online-irm-capabilities) :clock10: 10-15 min

---

===
# AIP Scanner Classification, Labeling, and Protection
[:arrow_left: Home](#azure-information-protection)

The Azure Information Protection scanner allows you to  classify and protect sensitive information stored in on-premises CIFS file shares and SharePoint sites.  

In this exercise, you will configure conditions for automatic classification.  After that, we will run the AIP Scanner in enforce mode to classify and protect the identified sensitive data. This Exercise will walk you through the items below.

- [Defining Automatic Conditions](#defining-automatic-conditions)
- [Enforcing Configured Rules](#enforcing-configured-rules)
- [Reviewing Protected Documents](#reviewing-protected-documents)

> [!Alert] This exercise requires completion of the previous [AIP Scanner Discovery](#aip-scanner-discovery) exercise.  If you did not already complete that exercise, please do so prior to continuing.

---

## Defining Automatic Conditions
[:arrow_up: Top](#aip-scanner-classification-labeling-and-protection)

The Azure Information Protection Scanner uses Automatic conditions to identify sensitive content to classify, label, and protect.  In this exercise, we will configure some of these conditions.  If you completed the Base Configuration exercise, you have already done some of these, but we will go through the process for those that may have skipped that exercise.

1. [] On @lab.VirtualMachine(Client01).SelectLink, log in with the password +++@lab.VirtualMachine(Client01).Password+++.
2. [] Open the browser window with the Azure Portal (AIP Blade).
3. [] Under **Dashboards** on the left, click on **Data discovery (Preview)** to view the results of the discovery scan we performed previously.

	!IMAGE[Dashboard.png](\Media\Dashboard.png)

	> [!KNOWLEDGE] Notice that there are no labeled or protected files shown at this time.  This uses the AIP P1 discovery functionality available with the AIP Scanner. Only the predefined Office 365 Sensitive Information Types are available with AIP P1 as Custom Sensitive Information Types require automatic conditions to be defined, which is an AIP P2 feature.

	> [!NOTE] Now that we know the sensitive information types that are most common in this environment, we can use that information to create **Recommended** conditions that will help guide user behavior when they encounter this type of data.

	> [!ALERT] If no data is shown, it may still be processing. Continue with the lab and come back to see the results later.

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
15. [] In the Labels: All Employees blade, in the **Configure conditions for automatically applying this label** section, click **Automatic**.

   !IMAGE[245lpjvk.jpg](\Media\245lpjvk.jpg)

1. [] Click **Save** in the Label: All Employees blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\gek63ks8.jpg)

1. [] Press the **X** in the upper right-hand corner to close the Label: All Employees blade.

	^IMAGE[Open Screenshot](\Media\wzwfc1l4.jpg)

---

## Enforcing Configured Rules
[:arrow_up: Top](#aip-scanner-classification-labeling-and-protection)

In this task, we will set the AIP scanner to enforce the conditions we set up in the previous task and have it rerun on all files using the Start-AIPScan command.

1. [] Switch to @lab.VirtualMachine(Scanner01).SelectLink and log in with the password +++@lab.VirtualMachine(Scanner01).Password+++.
1. [] In an **Administrative PowerShell** window, run the commands below to run an enforced scan using defined policy.

    ```
	Set-AIPScannerConfiguration -Enforce On -DiscoverInformationTypes PolicyOnly
	```
	```
	Start-AIPScan
    ```

	> [!HINT] Note that this time we used the DiscoverInformationTypes -PolicyOnly switch before starting the scan. This will have the scanner only evaluate the conditions we have explicitly defined in conditions.  This increases the effeciency of the scanner and thus is much faster.  After reviewing the event log we will see the result of the enforced scan.
	>
	>!IMAGE[k3rox8ew.jpg](\Media\k3rox8ew.jpg)
		

1. [] Switch back to @lab.VirtualMachine(Client01).SelectLink and log in using the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] Look in the reports directory we opened previously at ```\\Scanner01.contoso.azure\c$\users\aipscanner\AppData\Local\Microsoft\MSIP\Scanner\Reports```, you will notice that the old scan reports are zipped in the directory and only the most recent results are showing.  
	
	> If needed, use the credentials below:
	>
	>```Contoso\LabUser```
	>
	>```Pa$$w0rd```

	>[!NOTE] The DetailedReport.csv now shows the files that were protected.
	>
	>
	>!IMAGE[6waou5x3.jpg](\Media\6waou5x3.jpg)
	>
	>^IMAGE[Open Fullscreen](6waou5x3.jpg)

---

## Reviewing Protected Documents
[:arrow_up: Top](#aip-scanner-classification-labeling-and-protection)

Now that we have Classified and Protected documents using the scanner, we can review the documents to see their change in status.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.

2. [] Navigate to ```\\Scanner01.contoso.azure\documents```. 

	> If needed, use the credentials below:
	>
	>```Contoso\LabUser```
	>
    >```Pa$$w0rd```

	^IMAGE[Open Screenshot](\Media\hipavcx6.jpg)
3. [] Open one of the Contoso Purchasing Permissions documents.

	> [!NOTE] If asked to log in, use the credentials below.
	>
	> ```AdamS@@lab.CloudCredential(82).TenantName```
	>
	> ```pass@word1```

    > [!NOTE] Observe that the document is classified as Confidential \ All Employees. 
    >
    > !IMAGE[s1okfpwu.jpg](\Media\s1okfpwu.jpg)

---

===
# AIP Scanner CLP Exercise Complete

In this exercise, we configured the AIP scanner to use automatic conditions to classify, label, and protect documents in our defined repositories.  Choose one of the exercises below or click the Next button to continue sequentially.

- [AIP Scanner Discovery](#aip-scanner-discovery) :clock10: 10-15 min
- [Base Configuration](#configuring-azure-information-protection-policy) :clock10: 30-45 min
- [Bulk Classification](#bulk-classification) :clock10: 5 min
- [Security and Compliance Center](#security-and-compliance-center) :clock10: 5-10 min
- [AIP Analytics Dashboards](#aip-analytics-dashboards) :clock10: 5 min
- [Exchange IRM](#exchange-online-irm-capabilities) :clock10: 10-15 min

---

===

# Security and Compliance Center
[:arrow_left: Home](#azure-information-protection)

In this exercise, we will migrate your AIP Labels and activate them in the Security and Compliance Center.  This will allow you to see the labels in Microsoft Information Protection based clients such as Office 365 for Mac and Mobile Devices.

We will demonstrate these capabilities using Adobe PDF integration with AIP.

---
## Activating Unified Labeling

In this task, we will activate the labels from the Azure Portal for use in the Security and Compliance Center.

1. [] On @lab.VirtualMachine(Client01).SelectLink, log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] Navigate to ```https://portal.azure.com/?ActivateMigration=true#blade/Microsoft_Azure_InformationProtection/DataClassGroupEditBlade/migrationActivationBlade```

1. [] Click **Activate** and **Yes**.

	!IMAGE[o0ahpimw.jpg](\Media\o0ahpimw.jpg)

	>[!NOTE] You should see a message similar to the one below.
	>
	> !IMAGE[SCCMigration.png](\Media\SCCMigration.png) 

1. [] In a new tab, browse to ```https://protection.office.com/``` and click on **Classifications** and **Labels** to review the migrated labels. 

	>[!NOTE] Keep in mind that now the SCC Sensitivity Labels have been activated, so any modifications, additions, or deletions will be syncronised to Azure Information Protection in the Azure Portal. There are some functional differences between the two sections (DLP in SCC, HYOK & Custom Permissions in AIP), so please be aware of this when modifying policies to ensure a consistent experience on clients. 

---
## Deploying Policy in SCC

The previous step enabled the AIP labels for use in the Security and Compliance Center.  However, this did not also recreate the policies from the AIP portal. In this step we will publish a Global policy like the one we used in the AIP portal for use with unified clients.

1. [] In the Security and Compliance Center, under **Classifications**, click on **Label policies**.

2. [] In the Label policies pane, click **Publish labels**.

   ^IMAGE[Open Screenshot](\Media\SCC01.png)

3. [] On the Choose labels to publish page, click the **Choose labels to publish** link.

   ^IMAGE[Open Screenshot](\Media\SCC02.png)

4. [] In the Choose labels pane, click the **+ Add** button.

   ^IMAGE[Open Screenshot](\Media\SCC03.png)

5. [] Click the box next to **Display name** to select all labels, then click the **Add** button.

   ^IMAGE[Open Screenshot](\Media\SCC04.png)

6. [] Click the **Done** button.

   ^IMAGE[Open Screenshot](\Media\SCC05.png)

7. [] Back on the Choose labels to publish page, click the **Next** button.

   ^IMAGE[Open Screenshot](\Media\SCC06.png)

8. [] On the Publish to users and groups page, notice that **All users are included by default**. If you were creating a scoped policy, you would choose specific users or groups to publish to. Click **Next**.

   ^IMAGE[Open Screenshot](\Media\SCC07.png)

9. [] On the Policy settings page, select the **General** label from the drop-down next to **Apply this label by default to documents and email**.

10. [] Check the box next to **Users must provide justification to remove a label or lower classification label** and click the **Next** button.

    !IMAGE[Open Screenshot](\Media\SCC08.png)

11. [] In the Name textbox, type ```Global Policy``` and for the Description type ```This is the default global policy for all users.``` and click the **Next** button.

    ^IMAGE[Open Screenshot](\Media\SCC09.png)

12. [] Finally, on the Review your settings page, click the **Publish** button.

    !IMAGE[Open Screenshot](\Media\SCC10.png)

---
## Reviewing Adobe PDF Integration

1. [] To review a protected PDF, navigate to ```\\Scanner01.contoso.azure\documents```. 

	> If needed, use the credentials below:
	>
	>```Contoso\LabUser```
	>
    >```Pa$$w0rd```

4. [] In the documents folder, open one of the pdf files.
5. [] When prompted by Adobe, enter ```AdamS@@lab.CloudCredential(82).TenantName``` and press **OK** or **Next**.

	> [!NOTE] If prompted, provide the password ```pass@word1```.

6. [] Click **Yes** to save credentials.
7. [] In the **Permissions requested** dialog, click **Accept**.

	> [!NOTE] The PDF will now open and display the sensitivity across the top of the document.
	>
	> !IMAGE[PDF](\Media\PDF.png)

	> [!Knowledge] The latest version of Acrobat Reader DC and the MIP Plugin have been installed on this system prior to the lab. Additionally, the sensitivity does not display by default in Adobe Acrobat Reader DC.  You must make the modifications below to the registry to make this bar display.
	>
	> In **HKEY_CURRENT_USER\Software\Adobe\Acrobat Reader\DC\MicrosoftAIP**, create a new **DWORD** value of **bShowDMB** and set the **Value** to **1**.
	>
	> !IMAGE[1547416250228](\Media\1547416250228.png)

---

===
# Security and Compliance Center Exercise Complete

In this exercise, we enabled and published labels and policies in the Security and Compliance Center for use with clients based on the MIP SDK.  We demonstrated this using Adobe PDF integration.  Choose one of the exercises below or click the Next button to continue sequentially.

- [AIP Scanner Discovery](#aip-scanner-discovery) :clock10: 10-15 min
- [Base Configuration](#configuring-azure-information-protection-policy) :clock10: 30-45 min
- [Bulk Classification](#bulk-classification) :clock10: 5 min
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection) :clock10: 5-10 min
- [AIP Analytics Dashboards](#aip-analytics-dashboards) :clock10: 5 min
- [Exchange IRM](#exchange-online-irm-capabilities) :clock10: 10-15 min

---

===
# AIP Analytics Dashboards
[:arrow_left: Home](#azure-information-protection)

In this exercise, we will go to the AIP Analytics dashboards and observe them after completing all of the steps in the various exercises.  These dashboards give actionable data to AIP admins and their management related to how users are classifying and protecting data and where sensitive data is located throughout the environment.  Depending on which exercises you completed these may look different from the screenshots.

1. [] On @lab.VirtualMachine(Client01).SelectLink, open the browser that is logged into the Azure Portal.

1. [] Under **Dashboards**, click on **Usage report (Preview)**.

	> [!NOTE] Observe that there are now entries from the AIP scanner, File Explorer, Microsoft Outlook, and Microsoft Word based on our activities in this lab. 
	>
	> !IMAGE[Usage.png](\Media\newusage.png)

2. [] Next, under dashboards, click on **Activity logs (preview)**.
   
    > [!NOTE] We can now see activity from various users and clients including the AIP Scanner and specific users. 
	>
	> !IMAGE[activity.png](\Media\activity.png)
	>
	> You can also very quickly filter to just the **Highly Confidential** documents and identify the repositories and devices that contain this sensitive information.
	>
	> !IMAGE[activity2.png](\Media\activity2.png)

3. [] Finally, click on **Data discovery (Preview)**.

	> [!NOTE] In the Data discovery dashboard, you can see a breakdown of how files are being protected and locations that have sensitive content.
	>
	> !IMAGE[Discovery.png](\Media\Discovery.png)
	> 
	> If you click on one of the locations, you can drill down and see the content that has been protected on that specific device or repository.
	>
	> !IMAGE[discovery2.png](\Media\discovery2.png)
	
---

===
# AIP Analytics Dashboard Exercise Complete

In this exercise, we reviewed the new AIP Azure Log Analytics dashboards.  Choose one of the exercises below or click the Next button to continue sequentially.

- [AIP Scanner Discovery](#aip-scanner-discovery) :clock10: 10-15 min
- [Base Configuration](#configuring-azure-information-protection-policy) :clock10: 30-45 min
- [Bulk Classification](#bulk-classification) :clock10: 5 min
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection) :clock10: 5-10 min
- [Security and Compliance Center](#security-and-compliance-center) :clock10: 5-10 min
- [Exchange IRM](#exchange-online-irm-capabilities) :clock10: 10-15 min

---

===
# Exchange Online IRM Capabilities
[:arrow_left: Home](#azure-information-protection)

Exchange Online can work in conjunction with Azure Information Protection to provide advanced capabilities for protecting sensitive data being sent over email.  You can also manage the flow of classified content to ensure that it is not sent to unintended recipients. This Exercise will walk you through the items below.

- [Configuring Exchange Online Mail Flow Rules](#configuring-exchange-online-mail-flow-rules)
- [Demonstrating Exchange Online Mail Flow Rules](#demonstrating-exchange-online-mail-flow-rules)

## Configuring Exchange Online Mail Flow Rules

In this task, we will configure a mail flow rule to detect sensitive information traversing the network in the clear and encrypt it using the Encrypt Only RMS Template.  We will also create a mail flow rule to prevent messages classified as Confidential \ Contoso Internal from being sent to external recipients.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and open an **Admin PowerShell Prompt**.

2. [] Type the commands below to connect to an Exchange Online PowerShell session.  Use the credentials provided when prompted.

	```
	$UserCredential = Get-Credential
	```

	```@lab.CloudCredential(82).Username```

	```@lab.CloudCredential(82).Password```

	```
	$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://outlook.office365.com/powershell-liveid/ -Credential $UserCredential -Authentication Basic -AllowRedirection
	Import-PSSession $Session
	```

	> [!ALERT] If you did not run through the **Base Configuration** track, run the commands below to remove any rules preventing external collaboration.
	>
	> Get the active Mail Flow Rules by typing the command below:
	>
	> ```
	> Get-TransportRule
	> ```
	>
	> If a rule exists named something similar to **"Delete if sent outside the organization"**, run the code below to remove this rule.
	>
	> ```
	> Remove-TransportRule *Delete*
	> ```

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

1. [] Next, return to the PowerShell window and type +++$labelid = "+++ then paste the **LabelID** for the **All Employees** label, type +++"+++, and press **Enter**.
1. [] Now, create another Exchange Online Mail Flow Rule using the code below:

	```
	$labeltext = "MSIP_Label_"+$labelid+"_enabled=true"
	New-TransportRule -name "Block Confidential Contoso All Employees" -SentToScope notinorganization -HeaderContainsMessageHeader  "msip_labels" -HeaderContainsWord $labeltext -RejectMessageReasonText “Contoso internal messages cannot be sent to external recipients.”
	```

	>[!KNOWLEDGE] This mail flow rule can be used to prevent internal only communications from being sent to an external audience.
	>
	>New-TransportRule 
	>
	>-name "Block Confidential Contoso All Employees" 
	>
	>-SentToScope notinorganization 
	>
	>-HeaderContainsMessageHeader "msip_labels" 
	>
	>-HeaderContainsWord $labeltext 
	>
	>-RejectMessageReasonText “Contoso internal messages cannot be sent to external recipients.”

	>[!NOTE] In a production environment, customers would want to create a rule like this for each of their labels that they did not want going externally.

---
## Demonstrating Exchange Online Mail Flow Rules
[:arrow_up: Top](#exercise-6-exchange-online-irm-capabilities)

In this task, we will send emails to demonstrate the results of the Exchange Online mail flow rules we configured in the previous task.  This will demonstrate some ways to protect your sensitive data and ensure a positive user experience with the product.

1. [] Switch to @lab.VirtualMachine(Client03).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.

2. [] Open an InPrivate browsing session and browse to ```https://outlook.office365.com/owa/```.

1. [] Log in using the credentials below.

	> ```EvanG@@lab.CloudCredential(82).TenantName```
	>
	> ```pass@word1```

3. [] Send an email to Adam Smith, Alice Anderson, and yourself (```Adam Smith;Alice Anderson;@lab.User.Email```).  For the **Subject**, type ```Test Credit Card Email``` and for the **Body**, type ```My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368```, then click **Send**.

4. [] Switch to @lab.VirtualMachine(Client01).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
5. [] Review the received email.

  !IMAGE[pidqfaa1.jpg](\Media\pidqfaa1.jpg)

	> [!Knowledge] Note that there is no encryption applied to the message.  That is because we set up the rule to only apply to external recipients.  If you were to leave that condition out of the mail flow rule, internal recipients would also receive an encrypted copy of the message.  The image below shows the encrypted message that was received externally.
	>
	>!IMAGE[c5foyeji.jpg](\Media\c5foyeji.jpg)
	>
	>Below is another view of the same message received in Outlook Mobile on an iOS device.
	>
	>!IMAGE[599ljwfy.jpg](\Media\599ljwfy.jpg)

6. [] Next, in Microsoft Outlook, click on the **New email** button.

  ^IMAGE[Open Screenshot](\Media\6wan9me1.jpg)
. [] Send an email to Evan Green, Alice Anderson, and yourself (```Evan Green;Alice Anderson;@lab.User.Email```).  For the **Subject** and **Body** type ```Another Test Contoso Internal Email```.

  ^IMAGE[Open Screenshot](\Media\d476fmpg.jpg)

8. [] In the Sensitivity Toolbar, click on **Confidential** and then **All Employees** and click **Send**.

  ^IMAGE[Open Screenshot](\Media\yhokhtkv.jpg)

9. [] In about a minute, you should receive an **Undeliverable** message from Exchange with the users that the message did not reach and the message you defined in the previous task.

  !IMAGE[kgjvy7ul.jpg](\Media\kgjvy7ul.jpg)

	> [!NOTE] This rule may take a few minutes to take effect, so if you do not get the undeliverable message, try again in a few minutes.

	> [!HINT] There are many other use cases for Exchange Online mail flow rules but this should give you a quick view into what is possible and how easy it is to improve the security of your sensitive data through the use of Exchange Online mail flow rules and Azure Information Protection.

---

===
# Exchange IRM Exercise Complete

In this exercise, we created several Exchange Online Mail Flow Rules to protect sensitive data or improve user experience.  Choose one of the exercises below or click the Next button to complete the Lab.

- [AIP Scanner Discovery](#aip-scanner-discovery) :clock10: 10-15 min
- [Base Configuration](#configuring-azure-information-protection-policy) :clock10: 30-45 min
- [Bulk Classification](#bulk-classification) :clock10: 5 min
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection) :clock10: 5-10 min
- [Security and Compliance Center](#security-and-compliance-center) :clock10: 5-10 min
- [AIP Analytics Dashboards](#aip-analytics-dashboards) :clock10: 5 min

---

===
# AIP Lab Complete
[:arrow_left: Home](#azure-information-protection)

Congratulations! You have completed the Azure Information Protection Hands on Lab. 