===

# AIP Scanner Deployment
[:arrow_left: Home](#azure-information-protection)

Even before configuring an AIP classification taxonomy, customers can scan and identify files containing sensitive information based on the built-in sensitive information types included in the Microsoft Classification Engine.  

!IMAGE[ahwj80dw.jpg](\Media\ahwj80dw.jpg)

Often, this can help drive an appropriate level of urgency and attention to the risk customers face if they delay rolling out AIP classification and protection.  

In this exercise, we will install the AIP scanner and run it against repositories in discovery mode.  Later in this lab (after configuring labels and conditions), we will revisit the scanner to perform automated classification, labeling, and protection of sensitive documents. This Exercise will walk you through the items below.

- [Configuring Azure Log Analytics](#configuring-azure-log-analytics)
- [AIP Scanner Setup](#aip-scanner-setup)
- [Running Sensitive Data Discovery](#running-sensitive-data-discovery)

---
## AIP Scanner Setup
[:arrow_up: Top](#exercise-1-configuring-aip-scanner-for-discovery)

In this task we will install the AIP scanner binaries and create the Azure AD Applications necessary for authentication.

### Installing the AIP Scanner Service

The first step in configuring the AIP Scanner is to install the service and connect the database.  This is done with the Install-AIPScanner cmdlet that is provided by the AIP Client software.  The AIPScanner service account has been pre-staged in Active Directory for convenience.

1. [] Switch to @lab.VirtualMachine(Scanner01).SelectLink and log in using the password +++@lab.VirtualMachine(Client01).Password+++.

1. [] At the Administrative PowerShell prompt, click to type the code below 
   
   ```
   $SQL = "Scanner01"
   Install-AIPScanner -SQLServerInstance $SQL
   
   ```
3. [] When prompted, provide the credentials for the local AIP scanner service account.
	
	```Contoso\AIPScanner```

	```Somepass1```

	^IMAGE[Open Screenshot](\Media\pc9myg9x.jpg)

	> [!knowledge] You should see a success message like the one below. 
	>
	>!IMAGE[w7goqgop.jpg](\Media\w7goqgop.jpg)

### Creating Azure AD Applications for the AIP Scanner
[:arrow_up: Top](#exercise-1-configuring-aip-scanner-for-discovery)

Now that you have installed the scanner bits, you need to get an Azure AD token for the scanner service account to authenticate so that it can run unattended. This requires registering both a Web app and a Native app in Azure Active Directory.  The commands below will do this in an automated fashion rather than needing to go into the Azure portal directly.

1. [] In PowerShell, run ```Connect-AzureAD``` and use the username and password below. 
	
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
1. [] Click on the Start menu and type ```PowerShell```, right-click on the PowerShell program, and click **Run as a different user**.

	!IMAGE[zgt5ikxl.jpg](\Media\zgt5ikxl.jpg)

1. [] When prompted, enter the username and password below and click **OK**.

	```Contoso\AIPScanner``` 

	```Somepass1```

1. [] Restore the **Notepad** window and copy the **full Set-AIPAuthentication** command into this window from and run it.
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
   
