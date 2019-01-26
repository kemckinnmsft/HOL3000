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
2. [] On the desktop, **right-click** on **AADConfig.ps1** and click **Run with PowerShell**.

	!IMAGE[AADConfig](\Media\AADConfig.png)

	> [!NOTE] If prompted to change the execution policy, type **y** and **Enter**.

1. [] When prompted for the **Tenant name**, **click in the text box** and enter ```@lab.CloudCredential(134).TenantName```.
1. [] When prompted, provide the credentials below:

	```@lab.CloudCredential(134).Username```

	```@lab.CloudCredential(134).Password``` 
   
	> [!KNOWLEDGE] We are running the PowerShell code below to create the accounts and groups in AAD and assign licenses for EMS E5 and Office E5. This script is also available at [https://aka.ms/labscripts](https://aka.ms/labscripts) as AADConfig.ps1.
    > 
    > #### Azure AD User and Group Configuration
    > $tenantfqdn = "@lab.CloudCredential(134).TenantName"
    > $tenant = $tenantfqdn.Split('.')[0]
	> 
    > #### Build Licensing SKUs
    > $office = $tenant+":ENTERPRISEPREMIUM"
    > $ems = $tenant+":EMSPREMIUM"
	> 
    > #### Connect to MSOLService for licensing Operations
    > Connect-MSOLService -Credential $cred
	> 
    > #### Remove existing licenses to ensure enough licenses exist for our users
    > $LicensedUsers = Get-MsolUser -All  | where {$_.isLicensed -eq $true}
    > $LicensedUsers | foreach {Set-MsolUserLicense -UserPrincipalName $_.UserPrincipalName -RemoveLicenses $office, $ems}
	> 
    > #### Connect to Azure AD using stored credentials to create users
    > Connect-AzureAD -Credential $cred
	> 
    > #### Import Users from local csv file
    > $users = Import-csv C:\users.csv
	> 
    > foreach ($user in $users){
    > 	
    > #### Store UPN created from csv and tenant
    > $upn = $user.username+"@"+$tenantfqdn
	> 
    > #### Create password profile preventing automatic password change and storing password from csv
    > $PasswordProfile = New-Object -TypeName Microsoft.Open.AzureAD.Model.PasswordProfile 
    > $PasswordProfile.ForceChangePasswordNextLogin = $false 
    > $PasswordProfile.Password = $user.password
	> 
    > #### Create new Azure AD user
    > New-AzureADUser -AccountEnabled $True -DisplayName $user.displayname -PasswordProfile $PasswordProfile -MailNickName $user.username -UserPrincipalName $upn
    > }
    > 
    > #### MCAS user and group creation
	> $upn = "mcasAdminUS@"+$tenantfqdn
	> New-AzureADUser -AccountEnabled $True -DisplayName "MCAS US admin" -PasswordProfile $PasswordProfile -MailNickName "mcasadminUS" -UserPrincipalName $upn
    > New-AzureADGroup -DisplayName "US employees" -MailNickName "USemployees" -SecurityEnabled $true -MailEnabled $false
    > $groupId = Get-AzureADGroup -SearchString "usemployees"
    > $userId = Get-AzureADUser -SearchString "mcasadminus"
    > Add-AzureADGroupMember -RefObjectId $userId.ObjectId -ObjectId $groupId.ObjectId
	> 
	> Start-Sleep -s 10
	> foreach ($user in $users){
	> 
    > #### Store UPN created from csv and tenant
    > $upn = $user.username+"@"+$tenantfqdn
	> 
    > #### Assign Office and EMS licenses to users
    > Set-MsolUser -UserPrincipalName $upn -UsageLocation US
    > Set-MsolUserLicense -UserPrincipalName $upn -AddLicenses $office, $ems
    > }
	> 
    > #### Assign Office and EMS licenses to Admin user
    > $upn = "admin@"+$tenantfqdn
    > Set-MsolUser -UserPrincipalName $upn -UsageLocation US
    > Set-MsolUserLicense -UserPrincipalName $upn -AddLicenses $office, $ems

	> [!NOTE] The PowerShell window will automatically close once users have been created and licenses have been assigned.

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
1. [] Click **Confirm** if the correct email address is listed.

	!IMAGE[teyx280d.jpg](\Media\teyx280d.jpg)
7. [] Click in the Promo code box and type ```@lab.CloudCredential(215).PromoCode``` and click the **Claim Promo Code** button.

  !IMAGE[e1l35ko2.jpg](\Media\e1l35ko2.jpg)

    > [!NOTE] It may take up to 5 minutes to process the redemption.

8. [] Scroll to the bottom of the page and click **Next**.

  !IMAGE[ihrjazqi.jpg](\Media\ihrjazqi.jpg)

    > [!NOTE] You can keep the pre-populated information.

9. [] Check the box to agree to the terms and click **Sign up**.

  !IMAGE[k2a97g8e.jpg](\Media\k2a97g8e.jpg)

    > [!NOTE] It may take a few minutes to process the request.

1. [] While this is processing, you may continue to the next task.

---
## Configuring Azure Log Analytics

In order to collect log data from Azure Information Protection clients and services, you must first configure the log analytics workspace.

1. [] On @lab.VirtualMachine(Client01).SelectLink, open a new tab in the browser and navigate to ```https://portal.azure.com/```.

	> [!KNOWLEDGE] If necessary, log in using the username and password below:
	>
	>```@lab.CloudCredential(82).Username``` 
	>
	>```@lab.CloudCredential(82).Password```

3. [] In the Azure portal, type the word ```info``` into the **search bar** and press **Enter**, then click on **Azure Information Protection**. 

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