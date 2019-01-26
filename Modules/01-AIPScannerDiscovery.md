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

1. [] Open an **Administrative PowerShell Window** and type ```C:\Users\LabUser\Desktop\InstallScanner.ps1``` and press **Enter**. 

1. [] In the popup box, click **OK** to accept the default of **Scanner01**.

	> [!NOTE] We have preconfigured SQL Server on Scanner01 with a **default instance**. If using a **named instance** or **SQL Server Express**, you would populate this with **ServerName\\InstanceName** or **ServerName\\SqlExpress** respectively.

3. [] When prompted, provide the credentials for the **local** AIP scanner service account.
	
	```Contoso\AIPScanner```

	```Somepass1```

	^IMAGE[Open Screenshot](\Media\pc9myg9x.jpg)

	> [!KNOWLEDGE] This script installs the AIP scanner Service account using the **local domain user** account provisioned for the AIP Scanner. This account will need to be provided **read** access to **all repositories** that need to be scanned during **discovery**.  
	>
	> When you begin **classifying and labeling** files with the AIP scanner, this account will also need **write** access to the repositories, so this is something you should consider during rights assignment. 
	>
	> This script will run the code below. This script is available online at https://aka.ms/labscripts
	>
	> Add-Type -AssemblyName Microsoft.VisualBasic
	> 
	> $SQL = [Microsoft.VisualBasic.Interaction]::InputBox('Enter the name of your SQL Server or Server\Instance', 'SQL Server', "Scanner01")
	>
	> Install-AIPScanner -SQLServerInstance $SQL
	
	^IMAGE[Open Screenshot](\Media\w7goqgop.jpg)

### Creating Azure AD Applications for the AIP Scanner

Now that you have installed the scanner bits, you need to get an Azure AD token for the scanner service account to authenticate so that it can run unattended. This requires registering both a Web app and a Native app in Azure Active Directory.  The commands below will do this in an automated fashion rather than needing to go into the Azure portal directly.

1. [] Next, on the desktop, right-click on **GenerateAuthToken.ps1** and click **Run with PowerShell**.
1. [] When prompted, provide the username and password below. 
	
	```@lab.CloudCredential(134).Username```
	
	```@lab.CloudCredential(134).Password```

	> [!HINT] This will create a new **Web App Registration**, **Native App Registration**, and associated **Service Principals** in Azure AD. 
	>
	> Next, the script will output a new text file containing the **Set-AIPAuthentication** command and the **required values to generate the authentication token** for **any** AIP scanner server in an environment.
	
	> [!KNOWLEDGE] This script will run the code below. This script is available online at https://aka.ms/labscripts
	>
	> New-AzureADApplication -DisplayName AIPOnBehalfOf -ReplyUrls http://localhost
	> $WebApp = Get-AzureADApplication -Filter "DisplayName eq 'AIPOnBehalfOf'"
	> New-AzureADServicePrincipal -AppId $WebApp.AppId
	> $WebAppKey = New-Guid
	> $Date = Get-Date
	> New-AzureADApplicationPasswordCredential -ObjectId $WebApp.ObjectID -startDate $Date -endDate $Date.AddYears(1) -Value $WebAppKey.Guid -CustomKeyIdentifier "AIPClient"
	>
	> $AIPServicePrincipal = Get-AzureADServicePrincipal -All $true | ? {$_.DisplayName -eq 'AIPOnBehalfOf'}
	> $AIPPermissions = $AIPServicePrincipal | select -expand Oauth2Permissions
	> $Scope = New-Object -TypeName "Microsoft.Open.AzureAD.Model.ResourceAccess" -ArgumentList $AIPPermissions.Id,"Scope"
	> $Access = New-Object -TypeName "Microsoft.Open.AzureAD.Model.RequiredResourceAccess"
	> $Access.ResourceAppId = $WebApp.AppId
	> $Access.ResourceAccess = $Scope
	>
	> New-AzureADApplication -DisplayName AIPClient -ReplyURLs http://localhost -RequiredResourceAccess $Access -PublicClient $true
	> $NativeApp = Get-AzureADApplication -Filter "DisplayName eq 'AIPClient'"
	> New-AzureADServicePrincipal -AppId $NativeApp.AppId
	>
    > "Set-AIPAuthentication -WebAppID " + $WebApp.AppId + " -WebAppKey " + $WebAppKey.Guid + " -NativeAppID " + $NativeApp.AppId | Out-File ~\Desktop\Set-AIPAuthentication.txt
	> Start ~\Desktop\Set-AIPAuthentication.txt

1. [] Leave the notepad window open in the background.
1. [] **Click on the Start menu** and type ```PowerShell```, right-click on the PowerShell program, and click **Run as a different user**.

	!IMAGE[zgt5ikxl.jpg](\Media\zgt5ikxl.jpg)

1. [] When prompted, enter the username and password below and click **OK**.

	```Contoso\AIPScanner``` 

	```Somepass1```

1. [] Return to the **Notepad** window and copy the **full Set-AIPAuthentication** command into this window and run it.
1. [] When prompted, enter the username and password below:

	```AIPScanner@@lab.CloudCredential(134).TenantName```

	```Somepass1```

	^IMAGE[Open Screenshot](\Media\qfxn64vb.jpg)

1. [] In the Permissions requested window, click **Accept**.

   !IMAGE[nucv27wb.jpg](\Media\nucv27wb.jpg)
   
	>[!knowledge] You will a message like the one below in the PowerShell window once complete.
	>
	>!IMAGE[y2bgsabe.jpg](\Media\y2bgsabe.jpg)

1. [] **Close the PowerShell window**.
1. [] Next, in the **Admin PowerShell window**, run the command below.

	```Restart-Service AIPScanner```
   
---

## Configuring Repositories 

In this task, we will configure repositories to be scanned by the AIP scanner.  As previously mentioned, these can be any type of CIFS file shares including NAS devices sharing over the CIFS protocol.  Additionally, On premises SharePoint 2010, 2013, and 2016 document libraries and lists (attachements) can be scanned.  You can even scan entire SharePoint sites by providing the root URL of the site.  There are several optional 

> [!NOTE] SharePoint 2010 is only supported for customers who have extended support for that version of SharePoint.

The next task is to configure repositories to scan.  These can be on-premises SharePoint 2010, 2013, or 2016 document libraries and any accessible CIFS based share.

1. [] In the **Admin PowerShell window**, type ```C:\Users\LabUser\Desktop\ConfigureRepository.ps1``` and press **Enter**.

	> [!HINT] This command configures a **CIFS fileshare** repository and a **SharePoint document library** repository then **displays the configuration** to verify they were added.

	^IMAGE[Open Screenshot](\Media\scanner-repo.png)

    > [!KNOWLEDGE] The script runs the code below. This script is available online at https://aka.ms/labscripts
	>
	> Add-AIPScannerRepository -Path http://Scanner01/documents -SetDefaultLabel Off
	>
	> Add-AIPScannerRepository -Path \\Scanner01\documents -SetDefaultLabel Off
	>
	> Get-AIPScannerRepository
   
	>[!HINT] Notice that we added the **-SetDefaultLabel Off** switch to each of these repositories.  This is useful to prevent any Default labels from applying to files that do not match a condition when we do the enforced scan. This is optional and may be removed if desired.


---

## Running Sensitive Data Discovery 
[:arrow_up: Top](#configuring-aip-scanner-for-discovery)

1. [] In the **Admin PowerShell window**, type ```C:\Users\LabUser\Desktop\StartDiscovery.ps1``` and press **Enter**.

	> [!HINT] This command sets the global configuration of the AIP scanner to use **any custom conditions** that you have specified for labels in the Azure Information Protection policy, and the list of all **default sensitive information types** that are available to specify as conditions for labels.
	>
	> Although the scanner will discover documents to classify, it will not classify them because the configuration for the scanner is set to Discover only mode (Enforce Off).
	>
	> This command also starts the **initial discovery scan**.

	> [!KNOWLEDGE] The script runs the code below. This script is available online at https://aka.ms/labscripts
	>
	> Set-AIPScannerConfiguration -DiscoverInformationTypes All -Enforce Off
	>
	> Start-AIPScan
	
1. [] Right-click on the **Windows** button in the lower left-hand corner and click on **Event Viewer**.

	^IMAGE[Open Screenshot](\Media\cjvmhaf0.jpg)
1. [] Expand **Application and Services Logs** and click on **Azure Information Protection**.

	^IMAGE[Open Screenshot](\Media\dy6mnnpv.jpg)

	> [!ALERT] If you see a **.NET exception**, press **OK**. This is due to SharePoint startup in the VM environment. This event **must be acknowledged** to complete the discovery scan.

	> [!NOTE] You will see an event like the one below when the scanner completes the cycle. 
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

---

===
# AIP Scanner Discovery Exercise Complete

In this exercise, we installed the AIP scanner and performed a discovery scan against an on premises CIFS repository and SharePoint document library.  Although this was a very limited demonstration of the capabilities of the AIP scanner for discovery, it helps to show how quickly you can configure this tool and get actionable information which can be used to make data driven decisions about your security posture.  Choose one of the exercises below or click the Next button to continue sequentially.

- [Base Configuration](#configuring-azure-information-protection-policy)
- [Bulk Classification](#bulk-classification)
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection)
- [Security and Compliance Center](#security-and-compliance-center)
- [AIP Analytics Dashboards](#aip-analytics-dashboards)
- [Exchange IRM](#exchange-online-irm-capabilities)

---

