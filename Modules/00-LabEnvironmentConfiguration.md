===
# Lab Environment Configuration
[:arrow_left: Home](#introduction)

There are a few prerequisites that need to be set up to complete all the sections in this lab.  This Exercise will walk you through the items below.

- [Azure AD User Configuration](#azure-ad-user-configuration)
  
- [Exchange Mail Flow Rule Removal](#exchange-mail-flow-rule-removal)

- [Redeem Azure Pass](#redeem-azure-pass)

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

    ```

1. [] In the PowerShell window, click the code below to assign Office and EMS licenses.
	
	```
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
## Exchange Mail Flow Rule Removal
[:arrow_up: Top](#lab-environment-configuration)

By default, many of the demo tenants provided block external communications via mail flow rule.  As this will hinder many tests in this lab, we will verify if such a rule exists and remove it if necesary.

1. [] In the Admin PowerShell window, type the commands below to connect to an Exchange Online PowerShell session.  Use the credentials provided when prompted.

	```
	$Session = New-PSSession -ConfigurationName Microsoft.Exchange -ConnectionUri https://outlook.office365.com/powershell-liveid/ -Credential $cred -Authentication Basic -AllowRedirection
	Import-PSSession $Session
	```

3. [] Get the active Mail Flow Rules by typing the command below:

	```
	Get-TransportRule
	```

4. [] If a rule exists named something similar to **"Delete if sent outside the organization"**, run the code below to remove this rule.

	```
	Remove-TransportRule *Delete*
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

1. [] While this is processing, you may continue to the main lab.

