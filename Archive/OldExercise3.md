# Exercise 3: Client Configuration
[🔙](#azure-information-protection)

Now that we have defined some basic AIP Policies, we need to configure some clients to demonstrate the Discovery, Classification, and Protection capabilities of Azure Information Protection.  In this exercise, we will configure Office 365 Applications for 3 test users to demonstrate these policy actions.  

Office 365 and the latest GA AIP Client (1.37.19.0) have already been installed on these systems to save time in this lab.  In your production environment, you will need to install the AIP Client manually for testing or using an Enterprise Deployment Tool like System Center Configuration Manager for widespread deployment.

We will also be disabling a mail flow rule in the Exchange Admin Center to allow mail to be sent outside the tenant.  This will allow us to test Do Not Forward and Office 365 Message Encryption scenarios.

# Configuring Applications
[🔙](#azure-information-protection)
 
In this task, we will configure Word and Outlook for 3 test users.  These users are Alan Steiner (AlanS) and Amy Alberts (AmyA) who we have defined as members of the Legal group, and Eric Gruber (EricG).  

This will allow us to demonstrate the differences between the global and scoped policy and demonstrate some of the protection features of Azure Information Protection in the next exercise.

1. [] On @lab.VirtualMachine(Client01).SelectLink, minimize the Edge window and launch **Microsoft Word**.

	!IMAGE[pxyal6hb.jpg](\Media\pxyal6hb.jpg)
	
	> [!knowledge] When Word opens, you may see a prompt to log into **Microsoft Azure Information Protection**.  You may **close this** and continue.  Azure Information Protection will automatically inherit the settings from Word after reloading.
	>
	> !IMAGE[3gm9oeee.jpg](\Media\3gm9oeee.jpg)

1. [] In the upper right, click on **Sign in to get the most out of Office**.
	
	^IMAGE[Open Screenshot](\Media\elavdbu1.jpg)
1. [] In the Sign in dialog, enter +++AlanS@@lab.CloudCredential(17).TenantName+++ and press **Next**. 

1. [] In the Enter password dialog, enter +++@lab.CloudCredential(17).Password+++ and click **Sign in**.

1. [] In the Use this account everywhere on your device dialog, click **Yes**.

	^IMAGE[Open Screenshot](\Media\m1e7l6ei.jpg)

1. [] Finally, click **Done** to complete the setup.
1. [] **Close Microsoft Word**
1. [] Next, start **Microsoft Outlook** by clicking on the icon in the taskbar.

	!IMAGE[vlu3sb64.jpg](\Media\vlu3sb64.jpg)
1. [] Click **Connect** and let Outlook configure.  

	> [!KNOWLEDGE] Login details for **AlanS@@lab.CloudCredential(17).TenantName** should be automatically populated. If you still see **LabUser@Contoso.com**, close Microsoft Outlook and reopen.
	>
	> If you receive a prompt to choose an account type, click Office 365.
	>
	> !IMAGE[13mp3hbw.jpg](\Media\13mp3hbw.jpg)

1. [] Continue to the next step while Outlook configures.
1. [] Switch to **@lab.VirtualMachine(Client02).SelectLink**, press @lab.CtrlAltDelete, and log in using the username and password below:

	+++LabUser+++
	
	+++Pa$$w0rd+++

1. [] Launch **Microsoft Word**.

	!IMAGE[pxyal6hb.jpg](\Media\pxyal6hb.jpg)
	
	> [!knowledge] When Word opens, you may see a prompt to log into **Microsoft Azure Information Protection**.  You may **close this** and continue.  Azure Information Protection will automatically inherit the settings from Word after reloading.
	>
	> !IMAGE[3gm9oeee.jpg](\Media\3gm9oeee.jpg)

1. [] In the upper right, click on **Sign in to get the most out of Office**.
	
	^IMAGE[Open Screenshot](\Media\elavdbu1.jpg)
1. [] In the Sign in dialog, enter +++AmyA@@lab.CloudCredential(17).TenantName+++ and press **Next**. 

1. [] In the Enter password dialog, enter +++@lab.CloudCredential(17).Password+++ and click **Sign in**.

1. [] In the Use this account everywhere on your device dialog, click **Yes**.

	^IMAGE[Open Screenshot](\Media\m1e7l6ei.jpg)

1. [] Finally, click **Done** to complete the setup.
1. [] **Close Microsoft Word**
1. [] Next, start **Microsoft Outlook** by clicking on the icon in the taskbar.

	!IMAGE[vlu3sb64.jpg](\Media\vlu3sb64.jpg)
1. [] Click **Connect** and let Outlook configure.  

	> [!KNOWLEDGE] Login details for **Amy@@lab.CloudCredential(17).TenantName** should be automatically populated. If you still see **Install@Contoso.com**, close Microsoft Outlook and reopen.
	>
	> If you receive a prompt to choose an account type, click Office 365.
	>
	> !IMAGE[13mp3hbw.jpg](\Media\13mp3hbw.jpg)

1. [] Continue to the next step while Outlook configures.
1. [] Switch to **@lab.VirtualMachine(Client03).SelectLink**, press @lab.CtrlAltDelete, and log in using the username and password below:

	+++LabUser+++
	
	+++Pa$$w0rd+++

1. [] Launch **Microsoft Word**.

	!IMAGE[pxyal6hb.jpg](\Media\pxyal6hb.jpg)
	
	> [!knowledge] When Word opens, you may see a prompt to log into **Microsoft Azure Information Protection**.  You may **close this** and continue.  Azure Information Protection will automatically inherit the settings from Word after reloading.
	>
	> !IMAGE[3gm9oeee.jpg](\Media\3gm9oeee.jpg)

1. [] In the upper right, click on **Sign in to get the most out of Office**.
	
	^IMAGE[Open Screenshot](\Media\elavdbu1.jpg)
1. [] In the Sign in dialog, enter +++EricG@@lab.CloudCredential(17).TenantName+++ and press **Next**. 

1. [] In the Enter password dialog, enter +++@lab.CloudCredential(17).Password+++ and click **Sign in**.

1. [] In the Use this account everywhere on your device dialog, click **Yes**.

	^IMAGE[Open Screenshot](\Media\m1e7l6ei.jpg)

1. [] Finally, click **Done** to complete the setup.
1. [] Wait for the Getting Office ready for you dialog to close and then **Close Microsoft Word**
1. [] Next, start **Microsoft Outlook** by clicking on the icon in the taskbar.

	!IMAGE[vlu3sb64.jpg](\Media\vlu3sb64.jpg)
1. [] Click **Connect** and let Outlook configure.  

	> [!KNOWLEDGE] Login details for **EricG@@lab.CloudCredential(17).TenantName** should be automatically populated. If you still see **Install@Contoso.com**, close Microsoft Outlook and reopen.
	>
	> If you receive a prompt to choose an account type, click Office 365.
	>
	> !IMAGE[13mp3hbw.jpg](\Media\13mp3hbw.jpg)

