![MSIgnite_MVP Speaker_TW.png](/Content/MSIgnite_MVP Speaker_TW.png)

# Configuring Microsoft Information Protection Solutions to Protect Your Sensitive Data

# IGNITE 2018 INSTRUCTOR LED LABS WRK3014/3015 & HANDS ON LAB HOL3000

### Introduction

Estimated time to complete this lab

75 minutes

### Objectives

After completing this lab, you will be able to:

- Configure Azure Information Protection labels
- Configure Azure Information Protection policies
- Classify and protect content with Azure Information Protection in Office applications
- Configure Exchange Online Mail Flow Rules for AIP (Optional)
- Configure SharePoint IRM Libraries (Optional)
- Configure the Azure Information Protection scanner to discover sensitive data (Optional)
- Classify and Protect sensitive data discovered by the AIP Scanner (Optional)

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

Microsoft 365 E5 Tenant credentials will be provided during the event.  If you want to run through this lab after the event, you may use a tenant created through https://demos.microsoft.com or your own Microsoft 365 Tenant. This Lab Guide will be publicly available after the event at https://aka.ms/AIPHOL.

===

# Tips and Tricks

There are a few extras throughout this lab that are designed to make your lab experience a smooth and simple process.  Below are some icons you should watch out for that will save you time during each task.

## Interactive Elements

- Each task contains a series of steps required for successful completion of the lab.  To track your progress throughout the lab, check the box to the left of the numbered series.  

	!IMAGE[6mfi1ekm.jpg](6mfi1ekm.jpg)
- After you check each box, you will see your lab progress at the bottom of the instruction pane.

	!IMAGE[0ggu265u.jpg](0ggu265u.jpg)
- When you see an instruction for switching computers, click on the **blue link** in the text to have that VM loaded automatically.

	!IMAGE[12i85vgl.jpg](12i85vgl.jpg)
- Throughout the lab, you will see green text with a letter **T** in a square to the left.  This indicates that you can **click on the text** and it will **type it for you** in the VM.  **This will save you lots of time**.

	!IMAGE[cnyu1tdi.jpg](cnyu1tdi.jpg)
- You will also see Blue Click here links with a lightening bolt that will launch applications for you.  Alternate instructions are typically also provided below these links.

	!IMAGE[m2zvmfhk.jpg](m2zvmfhk.jpg)
- The last interactive element you will see throughout the lab is the **Open Screenshot** text below many steps.  To reduce clutter, most screenshots have been configured to launch in a popup window.  The only ones left visible are ones that could cause issues if they are missed or if there are multiple elements that are easier to understand with visual representation.

	!IMAGE[n4cqn070.jpg](n4cqn070.jpg)

## Additional Information

There are also Knowledge Items, Notes, and Hints throughout the lab.

- Knowledge Items are used to provide additional information about a topic related to the task or step.  These are often collapsed to reduce the amount of space they use, but it is recommended that you review these items if you want more information on the subject.

	!IMAGE[8g9nif1j.jpg](8g9nif1j.jpg)
- Notes are steps that do not require action or modification of any elements.  This includes general observations and reviewing the results of previous steps.

	!IMAGE[kxrbzsr2.jpg](kxrbzsr2.jpg)
- Hints are recommendations or observations that help with the completion of a step.

	!IMAGE[w11x99oo.jpg](w11x99oo.jpg)
===

# Exercise 1: Configuring Azure Information Protection Policy

This exercise demonstrates using the Azure Information Protection blade in the Azure portal to configure policies and sub-labels.  We will create a new sub-label and configure protection and then modify an existing sub-label.  We will also create a label that will be scoped to a specific group.  

We will next configure AIP Global Policy to use the General sub-label as default, and finally, we will configure a scoped policy to use the new scoped label by default for Word, Excel, and PowerPoint while still using General as default for Outlook.
===
# Creating, Configuring, and Modifying Sub-Labels

In this task, we will configure a label protected for internal audiences that can be used to help secure sensitive data within your company.  By limiting the audience of a specific label to only internal employees, you can dramatically reduce the risk of unintentional disclosure of sensitive data and help reduce the risk of successful data exfiltration by bad actors.  However, there are times when external collaboration is required, so we will configure a label to match the name and functionality of the Do Not Forward button in Outlook.  This will allow users to more securely share sensitive information outside the company to any recipient.  By using the name Do Not Forward, the functionality will also be familiar to what previous users of AD RMS or Azure RMS may have used in the past.

1. [] Switch to the **@lab.VirtualMachine(Client1).SelectLink** virtual machine.

1. [] @[Click here](`cmd.exe/c start shell:AppsFolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge -private https://portal.azure.com`) to launch the Azure Portal in a new Edge InPrivate window.
	
	> [!knowledge] If the command above does not work, right-click on **Edge** in the taskbar and click on **New InPrivate window**.
	>
	>!IMAGE[jnblioyn.jpg](jnblioyn.jpg)
	>
	> In the InPrivate window, navigate to +++https://portal.azure.com/+++
	>
	>^IMAGE[Open Screenshot](cznh7i2b.jpg)

1. [] Log in using +++@lab.CloudCredential(81).Username+++ and the password +++@lab.CloudCredential(81).Password+++.

	^IMAGE[Open Screenshot](gerhxqeq.jpg)

1. [] After logging into the portal, type the word +++info+++ into the **search bar** and press **Enter**, then click on **Azure Information Protection**. 

	!IMAGE[2598c48n.jpg](2598c48n.jpg)
	
	> [!HINT] If you do not see the search bar at the top of the portal, click on the **Magnifying Glass** icon to expand it.
	>
	> !IMAGE[ny3fd3da.jpg](ny3fd3da.jpg)

1. [] Under **classifications** in the left pane, click on **Labels** to load the Azure Information Protection – Labels blade.

	^IMAGE[Open Screenshot](mhocvtih.jpg)

1. [] In the Azure Information Protection – Labels blade, right-click on **Confidential** and click **Add a sub-label**.

	^IMAGE[Open Screenshot](uktfuwuk.jpg)

1. [] In the Sub-label blade, type +++Contoso Internal+++ for the **Label display name** and for **Description** enter text similar to +++Confidential data that requires protection, which allows Contoso Internal employees full permissions. Data owners can track and revoke content.+++

	^IMAGE[Open Screenshot](4luorc0u.jpg)

1. [] Then, under **Set permissions for documents and emails containing this label**, click **Protect**, and under **Protection**, click on **Azure (cloud key)**.

	^IMAGE[Open Screenshot](tp97a19d.jpg)

1. [] In the Protection blade, click **+ Add Permissions**.

	^IMAGE[Open Screenshot](layb2pvo.jpg)

1. [] In the Add permissions blade, click on **+ Add contoso – All members** and click **OK**.

	^IMAGE[Open Screenshot](zc0iuoyz.jpg)

1. [] In the Protection blade, click **OK**.

	^IMAGE[Open Screenshot](u8jv46zo.jpg)

1. [] In the Sub-label blade, scroll down to the **Set visual marking (such as header or footer)** section and under **Documents with this label have a header**, click **On**.

	> Use the values in the table below to configure the Header.

	| Setting          | Value            |
	|:-----------------|:-----------------|
	| Header text      | +++Contoso Internal+++ |
	| Header font size | +++24+++               |
	| Header color     | Purple           |
	| Header alignment | Center           |

	> [!NOTE] These are sample values to demonstrate marking possibilities and **NOT** a best practice.

	^IMAGE[Open Screenshot](0vdoc6qb.jpg)

1. [] To complete creation of the new sub-label, click the **Save** button and then click **OK** in the Save settings dialog.

	^IMAGE[Open Screenshot](89nk9deu.jpg)

1. [] In the Azure Information Protection - Labels blade, expand **Confidential** (if necessary) and then click on **Recipients Only**.

	^IMAGE[Open Screenshot](eiiw5zbg.jpg)

1. [] In the Label: Recipients Only blade, change the **Label display name** from **Recipients Only** to +++Do Not Forward+++.

	^IMAGE[Open Screenshot](v54vd4fq.jpg)

1. [] Next, in the **Set permissions for documents and emails containing this label** section, under **Protection**, click **Azure (cloud key): User defined**.

	^IMAGE[Open Screenshot](qwyranz0.jpg)

1. [] In the Protection blade, under **Set user-defined permissions (Preview)**, verify that only the box next to **In Outlook apply Do Not Forward** is checked, then click **OK**.

	^IMAGE[Open Screenshot](16.png)

	> [!knowledge] Although there is no action added during this step, it is included to show that this label will only display in Outlook and not in Word, Excel, PowerPoint or File Explorer.

1. [] Click **Save** in the Label: Recipients Only blade and **OK** to the Save settings prompt. 

	^IMAGE[Open Screenshot](9spkl24i.jpg)

1. []  Click the **X** in the upper right corner of the blade to close.

	^IMAGE[Open Screenshot](98pvhwdv.jpg)

===

# Configuring Global Policy

In this task, we will assign the new sub-label to the Global policy and configure several global policy settings that will increase Azure Information Protection adoption among your users and reduce ambiguity in the user interface.

1. [] In the Azure Information Protection blade, under **classifications** on the left, click **Policies** then click the **Global** policy.

	^IMAGE[Open Screenshot](24qjajs5.jpg)

1. [] In the Policy: Global blade, below the labels, click **Add or remove labels**.

	!IMAGE[mqed55qz.jpg](mqed55qz.jpg)

1. [] In the Policy: Add or remove labels blade, check the boxes next to **All Labels except the last 3** and click **OK**.

	!IMAGE[d0pxo2m6.jpg](d0pxo2m6.jpg)

1. [] In the Policy: Global blade, under the **Configure settings to display and apply on Information Protection end users** section, configure the policy to match the settings shown in the table and image below.

	| Setting | Value |
	|:--------|:------|
	| Select the default label | General |
	|All documents and emails must have a label…|On
	Users must provide justification to set a lower…|On
	For email messages with attachments, apply a label…|Automatic
	Add the Do Not Forward button to the Outlook ribbon|Off

	!IMAGE[Open Screenshot](mtqhe3sj.jpg)

1. [] Click **Save**, then **OK** to complete configuration of the Global policy.

	^IMAGE[Open Screenshot](1p1q4pxe.jpg)

1. [] Click the **X** in the upper right corner to close the Policy: Global blade.

	^IMAGE[Open Screenshot](m6e4r2u2.jpg)

===

# Creating a Scoped Label and Policy

Now that you have learned how to work with global labels and policies, we will create a new scoped label and policy for the Legal team at Contoso.  (If you are using your own demo tenant you may need to create the users and described.)

1. [] Under **classifications** on the left, click **Labels**.

	^IMAGE[Open Screenshot](50joijwb.jpg)

1. [] In the Azure Information Protection – Labels blade, right-click on **Highly-Confidential** and click **Add a sub-label**.

	^IMAGE[Open Screenshot](tasz9t0i.jpg)

1. [] In the Sub-label blade, enter +++Legal Only+++ for the **Label display name** and for **Description** enter +++Data is classified and protected. Legal department staff can edit, forward and unprotect.+++.

	^IMAGE[Open Screenshot](lpvruk49.jpg)

1. [] Then, under **Set permissions for documents and emails containing this label**, click **Protect** and under **Protection**, click **Azure (cloud key)**.

	^IMAGE[Open Screenshot](6ood4jqu.jpg)

1. [] In the Protection blade, under **Protection settings**, click the **+ Add permissions** link.

	!IMAGE[ozzumi7l.jpg](ozzumi7l.jpg)

1. [] In the Add permissions blade, click **+ Browse directory**.

	^IMAGE[Open Screenshot](2lvwim24.jpg)

1. [] In the AAD Users and Groups blade, **wait for the names to load**, then check the boxes next to **Alan Steiner** and **Amy Albers**, and click the **Select** button.

	^IMAGE[Open Screenshot](uishk9yh.jpg)

	> [!Note] In a production environment, you will typically use a synced or Azure AD Group rather than choosing individuals.

1. [] In the Add permissions blade, click **OK**.

	^IMAGE[Open Screenshot](stvnaf4f.jpg)

1. [] In the Protection blade, under **Allow offline access**, reduce the **Number of days the content is available without an Internet connection** value to ++3++ and press **OK** .

	> [!Knowledge] This value determines how many days a user will have offline access from the time a document is opened, and an initial Use License is acquired.  While this provides convenience for users, it is recommended that this value be set appropriately based on the sensitivity of the content.

	^IMAGE[Open Screenshot](j8masv1q.jpg)

1. [] Click Save in the Sub-label blade and OK to the Save settings prompt to complete the creation of the Legal Only sub-label.

	^IMAGE[Open Screenshot](dfhoii1x.jpg)

1. [] In the Azure Information Protection blade, under **Classifications** on the left, click **Policies** then click the **+Add a new policy** link.

	^IMAGE[Open Screenshot](ospsddz6.jpg)

1. [] In the Policy blade, for Policy name, type +++Legal Scoped Policy+++ and click on **Select which users or groups get this policy. Groups must be email-enabled.**

	!IMAGE[tosu2a6j.jpg](tosu2a6j.jpg)

1. [] In the AAD Users and Groups blade, click on **Users/Groups**.  
1. [] Then in the second AAD Users and Groups blade, **wait for the names to load** and check the boxes next to **Alan Steiner** and **Amy Albers**.
1. [] Click the **Select** button.
1. [] Finally, click **OK**.

	^IMAGE[Open Screenshot](onne7won.jpg)

1. [] In the Policy blade, under the labels, click on **Add or remove labels** to add the scoped label.

	!IMAGE[b6e9nbui.jpg](b6e9nbui.jpg)

1. [] In the Policy: Add or remove labels blade, check the box next to **Legal Only** and click **OK**.

	^IMAGE[Open Screenshot](c2429kv9.jpg)

1. [] In the Policy blade, under **Configure settings to display and apply on Information Protection end users** section, under **Select the default label**, select **Highly Confidential \ Legal Only** as the default label for this scoped policy.

	!IMAGE[6sh1sfz5.jpg](6sh1sfz5.jpg)

1. [] Click **Save**, then **OK** to complete creation of the Legal Scoped Policy.

	^IMAGE[Open Screenshot](41jembjf.jpg)

1. [] Click on the **X** in the upper right-hand corner to close the policy.

===

# Configuring Advanced Policy Settings

There are many advanced policy settings that are useful to tailor your Azure Information Protection deployment to the needs of your environment.  In this task, we will cover one of the settings that is very complimentary when using scoped policies that have a protected default label.  Because the Legal Scoped Policy we created in the previous task uses a protected default label, we will be adding an alternate default label for Outlook to provide a more palatable user experience for those users.

1. [] In the Azure Information Protection blade, under **classifications** on the left, click on **Labels** and then click on the **General** label.

    ^IMAGE[Open Screenshot](rvn4xorx.jpg)

1. [] In the Label: General blade, scroll to the bottom and copy the **Label ID** and close the blade using the **X** in the upper right-hand corner.

    !IMAGE[8fi1wr4d.jpg](8fi1wr4d.jpg)

1. [] In the AIP Portal, under **classifications** on the left, click on **Policies**. Right-click on the **Legal Scoped Policy** and click on **Advanced settings**.

    ^IMAGE[Open Screenshot](2jo71ugb.jpg)

1. [] In the Advanced settings blade, in the textbox under **NAME**, type +++OutlookDefaultLabel+++.  In the textbox under **VALUE**, paste the **Label ID** for the **General** label you copied previously, then click **Save and close**.

    > [!ALERT] CAUTION: Please check to ensure that there are **no spaces** before or after the **Label ID** when pasting as this will cause the setting to not apply.

    !IMAGE[ezt8sfs3.jpg](ezt8sfs3.jpg)

	> [!HINT] This and additional Advanced Policy Settings can be found at https://docs.microsoft.com/en-us/azure/information-protection/rms-client/client-admin-guide-customizations 

===

# Defining Recommended and Automatic Conditions

One of the most powerful features of Azure Information Protection is the ability to guide your users in making sound decisions around safeguarding sensitive data.  This can be achieved in many ways through user education or reactive events such as blocking emails containing sensitive data. However, helping your users to properly classify and protect sensitive data at the time of creation is a more organic user experience that will achieve better results long term.  In this task, we will define some basic recommended and automatic conditions that will trigger based on certain types of sensitive data.

1. [] Under **classifications** on the left, click **Labels** then expand **Confidential**, and click on **Contoso Internal**.

	^IMAGE[Open Screenshot](jyw5vrit.jpg)
1. [] In the Label: Contoso Internal blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	!IMAGE[cws1ptfd.jpg](cws1ptfd.jpg)
1. [] In the Condition blade, in the **Select information types** search box, type +++credit+++ and check the box next to **Credit Card Number**.

	^IMAGE[Open Screenshot](9rozp61b.jpg)
1. [] Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](41o5ql2y.jpg)

	> [!Knowledge] By default the condition is set to Recommended and a policy tip is created with standardized text.
	>
	>  !IMAGE[qdqjnhki.jpg](qdqjnhki.jpg)

1. [] Click **Save** in the Label: Contoso Internal blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](rimezmh1.jpg)
1. [] Press the **X** in the upper right-hand corner to close the Label: Contoso Internal blade.

	^IMAGE[Open Screenshot](em124f66.jpg)
1. [] Next, expand **Highly Confidential** and click on the **All Employees** sub-label.

	^IMAGE[Open Screenshot](2eh6ifj5.jpg)
1. [] In the Label: All Employees blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	^IMAGE[Open Screenshot](8cdmltcj.jpg)
1. [] In the Condition blade, click on **Custom** and enter +++Password+++ for the **Name** and in the textbox below **Match exact phrase or pattern**, type +++pass@word1+++.

	!IMAGE[ra7dnyg6.jpg](ra7dnyg6.jpg)
1. [] Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](ie6g5kta.jpg)
1. [] In the Labels: All Employees blade, in the **Configure conditions for automatically applying this label** section, click **Automatic**.

	!IMAGE[245lpjvk.jpg](245lpjvk.jpg)
	> [!HINT] The policy tip is automatically updated when you switch the condition to Automatic.
1. [] Click **Save** in the Label: All Employees blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](gek63ks8.jpg)
1. [] Press the **X** in the upper right-hand corner to close the Label: All Employees blade.

	^IMAGE[Open Screenshot](wzwfc1l4.jpg)

===

# Exercise 2: Client and Exchange Configuration

Now that we have defined some basic AIP Policies, we need to configure some clients to demonstrate the Discovery, Classification, and Protection capabilities of Azure Information Protection.  In this exercise, we will configure Office 365 Applications for 3 test users to demonstrate these policy actions.  

Office 365 and the latest GA AIP Client (1.29.5) have already been installed on these systems to save time in this lab.  In your production environment, you will need to install the AIP Client manually for testing or using an Enterprise Deployment Tool like System Center Configuration Manager for widespread deployment.

We will also be disabling a mail flow rule in the Exchange Admin Center to allow mail to be sent outside the tenant.  This will allow us to test Do Not Forward and Office 365 Message Encryption scenarios.
===
# Configuring Applications

In this task, we will configure Word and Outlook for 3 test users.  These users are Alan Steiner (AlanS) and Amy Alberts (AmyA) who we have defined as members of the Legal group, and Eric Grimes (EricG).  This will allow us to demonstrate the differences between the global and scoped policy and demonstrate some of the protection features of Azure Information Protection in the next exercise.

1. [] On @lab.VirtualMachine(Client1).SelectLink, minimize the Edge window and @[Click here](`"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\WINWORD.EXE"`) to launch **Microsoft Word** (This may take a few seconds).

	> [!Hint] If the automation does not work, start **Microsoft Word** by clicking on the icon in the taskbar.
	>
	>!IMAGE[pxyal6hb.jpg](pxyal6hb.jpg)
	
	> [!knowledge] When Word opens, you may see a prompt to log into **Microsoft Azure Information Protection**.  You may **close this** and continue.  Azure Information Protection will automatically inherit the settings from Word after reloading.
	>
	> !IMAGE[3gm9oeee.jpg](3gm9oeee.jpg)

1. [] In the Sign in set up Office dialog, click Sign in.
	
	^IMAGE[Open Screenshot](4yb3mnd1.jpg)
1. [] In the Activate Office dialog, enter +++AlanS@@lab.CloudCredential(81).TenantName+++ and press **Next**. 

1. [] In the Enter password dialog, enter +++pass@word1+++ and click **Sign in**.

1. [] In the Use this account everywhere on your device dialog, click **Yes**.

	^IMAGE[Open Screenshot](m1e7l6ei.jpg)

1. [] Finally, click **Done** to complete the setup.
1. [] Wait for the Getting Office ready for you dialog to close and then **Close Microsoft Word**
1. [] Next, @[Click here](`"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\OUTLOOK.EXE"`) to start **Microsoft Outlook** or click on the icon in the taskbar.

	!IMAGE[vlu3sb64.jpg](vlu3sb64.jpg)
1. [] Click **Connect** and let Outlook configure.  

	> [!KNOWLEDGE] Login details for **AlanS@@lab.CloudCredential(81).TenantName** should be automatically populated.
	>
	> If you receive a prompt to choose an account type, click Office 365.
	>
	> !IMAGE[13mp3hbw.jpg](13mp3hbw.jpg)

1. [] **Uncheck** the box to **Set up Outlook Mobile on my phone**, too and click **OK**.

	!IMAGE[hjmvdzvv.jpg](hjmvdzvv.jpg)

1. [] Switch to **@lab.VirtualMachine(Windows10-CLI3).SelectLink** and and @[Click here](`"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\WINWORD.EXE"`) to launch **Microsoft Word** (This may take a few seconds).

	> [!Hint] If the automation does not work, start **Microsoft Word** by clicking on the icon in the taskbar.
	>
	>!IMAGE[pxyal6hb.jpg](pxyal6hb.jpg)
	
	> [!knowledge] When Word opens, you may see a prompt to log into **Microsoft Azure Information Protection**.  You may **close this** and continue.  Azure Information Protection will automatically inherit the settings from Word after reloading.
	>
	> !IMAGE[3gm9oeee.jpg](3gm9oeee.jpg)

1. [] In the Sign in set up Office dialog, click Sign in.
	
	^IMAGE[Open Screenshot](4yb3mnd1.jpg)
1. [] In the Activate Office dialog, enter +++AmyA@@lab.CloudCredential(81).TenantName+++ and press **Next**. 

1. [] In the Enter password dialog, enter +++pass@word1+++ and click **Sign in**.

1. [] In the Use this account everywhere on your device dialog, click **Yes**.

	^IMAGE[Open Screenshot](m1e7l6ei.jpg)

1. [] Finally, click **Done** to complete the setup.
1. [] Wait for the Getting Office ready for you dialog to close and then **Close Microsoft Word**
1. [] Next, @[Click here](`"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\OUTLOOK.EXE"`) to start **Microsoft Outlook** or click on the icon in the taskbar.

	!IMAGE[vlu3sb64.jpg](vlu3sb64.jpg)
1. [] Click **Connect** and let Outlook configure.  

	> [!KNOWLEDGE] Login details for **AmyA@@lab.CloudCredential(81).TenantName** should be automatically populated. If the are not, please close and reopen Outlook.
	>
	> If you receive a prompt to choose an account type, click Office 365.
	>
	> !IMAGE[13mp3hbw.jpg](13mp3hbw.jpg)

1. [] **Uncheck** the box to **Set up Outlook Mobile on my phone**, too and click **OK**.

	^IMAGE[Open Screenshot](amepnw76.jpg)

1. [] Switch to **@lab.VirtualMachine(Windows10-CLI4).SelectLink** and @[Click here](`"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\WINWORD.EXE"`) to launch **Microsoft Word** (This may take a few seconds).

	> [!Hint] If the automation does not work, start **Microsoft Word** by clicking on the icon in the taskbar.
	>
	>!IMAGE[pxyal6hb.jpg](pxyal6hb.jpg)
	
	> [!knowledge] When Word opens, you may see a prompt to log into **Microsoft Azure Information Protection**.  You may **close this** and continue.  Azure Information Protection will automatically inherit the settings from Word after reloading.
	>
	> !IMAGE[3gm9oeee.jpg](3gm9oeee.jpg)

1. [] In the Sign in set up Office dialog, click Sign in.
	
	^IMAGE[Open Screenshot](4yb3mnd1.jpg)
1. [] In the Activate Office dialog, enter +++EricG@@lab.CloudCredential(81).TenantName+++ and press **Next**. 

1. [] In the Enter password dialog, enter +++pass@word1+++ and click **Sign in**.

1. [] In the Use this account everywhere on your device dialog, click **Yes**.

	^IMAGE[Open Screenshot](m1e7l6ei.jpg)

1. [] Finally, click **Done** to complete the setup.
1. [] Wait for the Getting Office ready for you dialog to close and then **Close Microsoft Word**
1. [] Next, @[Click here](`"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\OUTLOOK.EXE"`) to start **Microsoft Outlook** or click on the icon in the taskbar.

	!IMAGE[vlu3sb64.jpg](vlu3sb64.jpg)
1. [] Click **Connect** and let Outlook configure.  

	> [!KNOWLEDGE] Login details for **EricG@@lab.CloudCredential(81).TenantName** should be automatically populated.
	>
	> If you receive a prompt to choose an account type, click Office 365.
	>
	> !IMAGE[13mp3hbw.jpg](13mp3hbw.jpg)

1. [] **Uncheck** the box to **Set up Outlook Mobile on my phone**, too and click **OK**.

	^IMAGE[Open Screenshot](hjmvdzvv.jpg)

===

# Configuring Exchange Online Mail Flow Rules

1. [] Switch to **@lab.VirtualMachine(Client1).SelectLink**.

1. [] @[Click here](`cmd.exe/c start shell:AppsFolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge -private https://outlook.office365.com/ecp/`) to launch the Exchange Admin Center.

	> [!knowledge] Right-click on **Edge** in the taskbar and click on **New InPrivate window**.
	>
	>!IMAGE[jnblioyn.jpg](jnblioyn.jpg)
	>In the InPrivate window, navigate to +++https://outlook.office365.com/ecp/+++.
	
1. [] Log in using +++@lab.CloudCredential(81).Username+++ and the password +++@lab.CloudCredential(81).Password+++.

1. [] In the Exchange admin center, click on **mail flow** on the left, then uncheck the box next to **Delete if sent outside the organization**.

	!IMAGE[kgoqkfrs.jpg](kgoqkfrs.jpg)
1. [] Wait for the view to upadte showing the disabled rule, then **minimize this window** and save it for when we add new rules in Exercise 4.

===

# Exercise 3: Testing AIP Policies

Now that you have 3 test systems with users being affected by different policies configured, we can start testing these policies.  This exercise will run through various scenarios to demonstrate the use of AIP global and scoped policies and show the functionality of recommended and automatic labeling.
===
# Testing User Defined Permissions

One of the most common use cases for AIP is the ability to send emails using User Defined Permissions (Do Not Forward). In this task, we will send an email using the Do Not Forward label to test that functionality.

1. [] Switch to @lab.VirtualMachine(Windows10-CLI4).SelectLink.
1. [] In Microsoft Outlook, click on the **New email** button.

	!IMAGE[6wan9me1.jpg](6wan9me1.jpg)

	> [!KNOWLEDGE] Note that the **Sensitivity** is set to **General** by default.
	>
	> !IMAGE[5esnhwkw.jpg](5esnhwkw.jpg)

1. [] Send an email to Alan and Amy (+++AlanS;AmyA+++). You may optionally add an external email address (preferably from a major social provider like gmail, yahoo, or outlook.com) to test the external recipient experience. For the **Subject** and **Body** type +++Test Do Not Forward Email+++.

	^IMAGE[Open Screenshot](h0eh40nk.jpg)

1. [] In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	!IMAGE[901v6vpa.jpg](901v6vpa.jpg)

1. [] Click on **Confidential** and then the **Do Not Forward** sub-label and click **Send**.

	!IMAGE[w8j1w1lm.jpg](w8j1w1lm.jpg)

	> [!Knowledge] If you receive the error message below, click on the Confidential \ Contoso Internal sub-label to force the download of your AIP identity certificates, then follow the steps above to change the label to Confidential \ Do Not Forward.
	>
	> !IMAGE[6v6duzbd.jpg](6v6duzbd.jpg)

1. [] Switch over to @lab.VirtualMachine(Client1).SelectLink or @lab.VirtualMachine(Windows10-CLI3).SelectLink and review the email in Alan or Amy’s Outlook.  You will notice that the email is automatically shown in Outlook natively.

	!IMAGE[0xby56qt.jpg](0xby56qt.jpg)

	> [!Hint] The **Do Not Forward** protection template will normally prevent the sharing of the screen and taking screenshots when protected documents or emails are loaded.  However, since this screenshot was taken within a VM, the operating system was unaware of the protected content and could not prevent the capture.  
	>
	>It is important to understand that although we put controls in place to reduce risk, if a user has view access to a document or email they can take a picture with their smartphone or even retype the message. That said, if the user is not authorized to read the message then it will not even render and we will demonstrate that next.

	> [!KNOWLEDGE] If you elected to send a Do Not Forward message to an external email, you will have an experience similar to the images below.  These captures are included to demonstrate the functionality for those that chose not to send an external message.
	>
	> !IMAGE[tzj04wi9.jpg](tzj04wi9.jpg)
	> 
	> Here the user has received an email from Eric Grimes and they can click on the **Read the message** button.
	>
	>!IMAGE[wiefwcho.jpg](wiefwcho.jpg)
	>
	>Next, the user is given the option to either log in using the social identity provider (**Sign in with Google**, Yahoo, Microsoft Account), or to **sign in with a one-time passcode**.
	>
	>If they choose the social identity provider login, it should use the token previously cached by their browser and display the message directly.
	>
	>If they choose one-time passcode, they will receive an email like the one below with the one-time passcode.
	>
	>!IMAGE[m6voa9xi.jpg](m6voa9xi.jpg)
	>
	>They can then use this code to authenticate to the Office 365 Message Encryption portal.
	>
	>!IMAGE[8pllxint.jpg](8pllxint.jpg)
	>
	>After using either of these authentication methods, the user will see a portal experience like the one shown below.
	>
	>!IMAGE[3zi4dlk9.jpg](3zi4dlk9.jpg)
===

# Testing Global Policy

In this task, we will create a document and send an email to demonstrate the functionality defined in the Global Policy.

1. [] Switch to @lab.VirtualMachine(Windows10-CLI4).SelectLink.
1. [] In Microsoft Outlook, click on the **New email** button.

	^IMAGE[Open Screenshot](6wan9me1.jpg)

1. [] Send an email to Alan, Amy, and yourself (+++AlanS;AmyA;@lab.User.Email+++).  For the **Subject** and **Body** type +++Test Contoso Internal Email+++.

	^IMAGE[Open Screenshot](9gkqc9uy.jpg)

1. [] In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	^IMAGE[Open Screenshot](901v6vpa.jpg)

1. [] Click on **Confidential** and then **Contoso Internal** and click **Send**.

	^IMAGE[Open Screenshot](yhokhtkv.jpg)
1. [] On @lab.VirtualMachine(Client1).SelectLink or @lab.VirtualMachine(Windows10-CLI3).SelectLink, observe that you are able to open the email natively in the Outlook client. Also observe the **header text** that was defined in the label settings.

	!IMAGE[bxz190x2.jpg](bxz190x2.jpg)
	
1. [] In your email, note that you will be unable to open this message.  This experience will vary depending on the client you use (the image below is from Outlook 2016 for Mac) but they should have similar messages after presenting credentials. Since this is not the best experience for the recipient, in Exercise 4, we will configure Exchange Online Mail Flow Rules to prevent content classified with internal only labels from being sent to external users.
	
	!IMAGE[52hpmj51.jpg](52hpmj51.jpg)

===

# Testing Scoped Policy

In this task, we will create a document and send an email from one of the users in the Legal group to demonstrate the functionality defined in the first exercise. We will also show the behavior of the No Default Label policy on documents.

1. [] Switch to @lab.VirtualMachine(Client1).SelectLink.
1. [] In Microsoft Outlook, click on the **New email** button.
	
	^IMAGE[Open Screenshot](ldjugk24.jpg)
	
1. [] Send an email to Amy and Eric (+++AmyA;EricG+++).  For the **Subject** and **Body** type +++Test Highly Confidential Legal Email+++.
1. [] In the Sensitivity Toolbar, click on **Highly Confidential** and the **Legal Only** sub-label, then click **Send**.

	^IMAGE[Open Screenshot](ny1lwv0h.jpg)
1. [] Switch to @lab.VirtualMachine(Windows10-CLI3).SelectLink and click on the email.  You should be able to open the message natively in the client as AmyA.

	!IMAGE[qeqtd2yr.jpg](qeqtd2yr.jpg)
1. [] Switch to @lab.VirtualMachine(Windows10-CLI4).SelectLink and click on the email. You should be unable to open the message as EricG.

	!IMAGE[6y99u8cl.jpg](6y99u8cl.jpg)

	> [!Knowledge] You may notice that the Office 365 Message Encryption wrapper message is displayed in the preview pane.  It is important to note that the content of the email is not displayed here.  The content of the message is contained within the encrypted message.rpmsg attachment and only authorized users will be able to decrypt this attachment.
	>
	>!IMAGE[w4npbt49.jpg](w4npbt49.jpg)
	>
	>If an unauthorized recipient clicks on **Read the message** to go to the OME portal, they will be presented with the same wrapper message.  Like the external recipient from the previous task, this is not an ideal experience. So, you may want to use a mail flow rule to manage scoped labels as well.
	>
	>!IMAGE[htjesqwe.jpg](htjesqwe.jpg)

===

# Testing Recommended and Automatic Classification

In this task, we will test the configured recommended and automatic conditions we defined in Exercise 1.  Recommended conditions can be used to help organically train your users to classify sensitive data appropriately and provides a method for testing the accuracy of your dectections prior to switching to automatic classification.  Automatic conditions should be used after thorough testing or with items you are certain need to be protected. Although the examples used here are fairly simple, in production these could be based on complex regex statements or only trigger when a specific quantity of sensitive data is present.

1. [] Switch to @lab.VirtualMachine(Windows10-CLI4).SelectLink and @[Click here](`"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\WINWORD.EXE"`) to launch **Microsoft Word**.
1. [] In Microsoft Word, create a new **Blank document** and type +++My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368+++ and **save** the document.

	> [!NOTE] This card number is a fake number that was generated using the Credit Card Generator for Testing at https://developer.paypal.com/developer/creditCardGenerator/.  The Microsoft Classification Engine uses the Luhn Algorithm to prevent false positives so when testing, please make sure to use valid numbers.

1. [] Notice that you are prompted with a recommendation to change the classification to Confidential \ Contoso Internal. Click on **Change now** to set the classification and protect the document.

	!IMAGE[url9875r.jpg](url9875r.jpg)
	> [!Knowledge] Notice that, like the email in Task 2 of this exercise, the header value configured in the label is added to the document.
	>
	>!IMAGE[dcq31lz1.jpg](dcq31lz1.jpg)
1. [] In Microsoft Word, create a new **Blank document** and type +++my password is pass@word1+++ and **save** the document.

	>[!HINT] Notice that the document is automatically classified and protected wioth the Highly Confidential \ All Employees label.
	>
	>!IMAGE[6vezzlnj.jpg](6vezzlnj.jpg)
1. [] Next, in Microsoft Outlook, click on the **New email** button.
	
	^IMAGE[Open Screenshot](ldjugk24.jpg)
	
1. [] Draft an email to Amy and Alan (+++AmyA;AlanS+++).  For the **Subject** and **Body** type +++Test Highly Confidential All Employees Automation+++.

	^IMAGE[Open Screenshot](4v3wrrop.jpg)
1. [] Attach the **second document you created** to the email.

	!IMAGE[823tzyfd.jpg](823tzyfd.jpg)

	> [!HINT] Notice that the email was automatically classified as Highly Confidential \ All Employees.  This functionality is highly recommended because matching the email classification to attachments provides a much more cohesive user experience and helps to prevent inadvertent information disclosure in the body of sensitive emails.
	>
	>!IMAGE[yv0afeow.jpg](yv0afeow.jpg)

1. [] In the email, click **Send**.
===

# Exercise 4: Exchange Online IRM Capabilities

Exchange Online can work in conjunction with Azure Information Protection to provide advanced capabilities for protecting sensitive data being sent over email.  You can also manage the flow of classified content to ensure that it is not sent to unintended recipients.  

# Configuring Exchange Online Mail Flow Rules

In this task, we will configure a mail flow rule to detect credit card information traversing the network in the clear and encrypt it using the Encrypt Only RMS Template.  We will also create a mail flow rule to prevent messages classified as Confidential \ Contoso Internal from being sent to external recipients.

1. [] Switch to @lab.VirtualMachine(Client1).SelectLink and restore the **Exchange admin center** browser window.

	> [!HINT] If you closed the window, @[Click here](`cmd.exe/c start shell:AppsFolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge -private https://outlook.office365.com/ecp/`) to open an Edge InPrivate window and navigate to +++https://outlook.office365.com/ecp/+++ and log in using +++@lab.CloudCredential(81).Username+++ and the password +++@lab.CloudCredential(81).Password+++.  

1. [] Ensure you are under **mail flow** > **rules**, then click on the **plus icon** and click **Create a new rule...**

	!IMAGE[5mfzbjt1.jpg](5mfzbjt1.jpg)
1. [] In the new rule window, for **Name** type +++Sensitive Data Encrypt Only+++.
1. [] Under **\*Apply this rule if...**, in the drop-down, click on **The recipient is located...**.
1. [] In the select recipient location dialog, in the drop-down, click **Outside the organization**, and click **OK**.

	!IMAGE[x2cj3zor.jpg](x2cj3zor.jpg)
1. [] Next, click on **More options...** to display th option to add additional conditions.

	!IMAGE[rpqtrf8m.jpg](rpqtrf8m.jpg)
1. [] Click on **add condition**.

	!IMAGE[mjcrysfj.jpg](mjcrysfj.jpg)
1. [] Click the **Select one** drop-down and hover over **The message...**, and click on **contains any of these types of sensitive information**.

	!IMAGE[ezuengms.jpg](ezuengms.jpg)
	!IMAGE[kq8wxegt.jpg](kq8wxegt.jpg)
1. [] In the Contains any of these sensitive information types dialog, click the **plus sign**.

	!IMAGE[53f6tfbi.jpg](53f6tfbi.jpg)
1. [] In the Sensitive information types window, scroll down and click on **Credit Card Number**, then click **Add** and **OK**.

	!IMAGE[dgmsayk6.jpg](dgmsayk6.jpg)
1. [] In the Contains any of these sensitive information types dialog, click **OK**.

	!IMAGE[dihs4zqr.jpg](dihs4zqr.jpg)
1. [] In the new rule window, under \*Do the following..., in the Select one drop-down, hover over **Modify the message security...** and click on **Apply Office 365 Message Encryption and rights protection**.

	!IMAGE[cu1bgeho.jpg](cu1bgeho.jpg)
	!IMAGE[rwo2scwt.jpg](rwo2scwt.jpg)
1. [] In the select RMS template dialog, in the RMS template drop-down, click on **Encrypt** and click **OK**.

	!IMAGE[c8r08bh7.jpg](c8r08bh7.jpg)
1. [] Review the settings in the new rule window and click **Save**.

	!IMAGE[wwlsz0p2.jpg](wwlsz0p2.jpg)
	> [!HINT] Next, we need to capture the **Label ID** for the **Confidential \ Contoso Internal** label. 

1. [] Switch to the Azure Portal and under **classifications** click on Labels, then expand **Confidential** and click on **Contoso Internal**.

	!IMAGE[w2w5c7xc.jpg](w2w5c7xc.jpg)

	> [!HINT] If you closed the azure portal, @[Click here](`cmd.exe/c start shell:AppsFolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge -private https://portal.azure.com`) to open an Edge InPrivate window and navigate to +++https://portal.azure.com+++.

1. [] In the Label: Contoso Internal blade, scroll down to the Label ID and **copy** the value.

	!IMAGE[lypurcn5.jpg](lypurcn5.jpg)

	> [!ALERT] Make sure that there are no spaces before or after the Label ID as this will cause the mail flow rule to be ineffective.

1. [] Return the rules section of the Exchange admin center, click on the **plus icon** and click **Create a new rule...**

	!IMAGE[5mfzbjt1.jpg](5mfzbjt1.jpg)
1. [] In the new rule window, for **Name** type +++Block Contoso Internal to External Recipient+++.
1. [] Under **\*Apply this rule if...**, in the drop-down, click on **The recipient is located...**.
1. [] In the select recipient location dialog, in the drop-down, click **Outside the organization**, and click **OK**.

	!IMAGE[on6fbr0d.jpg](on6fbr0d.jpg)
1. [] Next, click on **More options...** to display th option to add additional conditions.

	!IMAGE[rpqtrf8m.jpg](rpqtrf8m.jpg)
1. [] Click on **add condition**.

	!IMAGE[cqlps27h.jpg](cqlps27h.jpg)
1. [] Click the **Select one** drop-down and hover over **A message header...**, and click on **includes any of these words**.

	!IMAGE[zus8h1mn.jpg](zus8h1mn.jpg)
1. [] Click on **\*Enter text...**, and in the specify header name dialog, type +++msip_labels+++ then click **OK**.

	!IMAGE[jrpy4k02.jpg](jrpy4k02.jpg)
1. [] Next, click on **\*Enter words...**, and in the specify words or phrases dialog, type +++MSIP_LABEL_+++, **paste the Label ID value** for the Confidential \ Contoso Internal label, then type +++_enabled\=True;+++. Next, click the **plus sign**, and then click **OK**.

	!IMAGE[temiq3e1.jpg](temiq3e1.jpg)
1. [] In the new rule window, under \*Do the following..., in the Select one drop-down, hover over **Block the message...**, and click **reject the message and include an explanation**.

	!IMAGE[04mlyfs3.jpg](04mlyfs3.jpg)
	!IMAGE[akeykn8a.jpg](akeykn8a.jpg)

1. [] In the **specify rejection reason**, type +++Contoso Internal messages cannot be sent to external recipients.+++ and click **OK**.

	!IMAGE[4odbewl3.jpg](4odbewl3.jpg)

1. [] Review the rule and make sure it looks similar to the picture below and click **Save**.

	!IMAGE[lgg2r4se.jpg](lgg2r4se.jpg)

===

# Demonstrating Exchange Online Mail Flow Rules

In this task, we will send emails to demonstrate the results of the Exchange Online mail flow rules we configured in the previous task.  This will demonstrate some ways to protect your sensitive data and ensure a positive user experience with the product.

1. [] Switch to @lab.VirtualMachine(Windows10-CLI4).SelectLink.
1. [] In Microsoft Outlook, click on the **New email** button.

	^IMAGE[Open Screenshot](6wan9me1.jpg)

1. [] Send an email to Alan, Amy, and yourself (+++AlanS;AmyA;@lab.User.Email+++).  For the **Subject**, type +++Test Credit Card Email+++ and for the **Body**, type +++My AMEX card number is 344047014854133. The expiration date is 09/28, and the CVV is 4368+++. an
1. [] In the DLP Policy Tip, click override.

	!IMAGE[aezwvoir.jpg](aezwvoir.jpg)
1. [] In the Override dialog, type +++Lab Demo+++ and click **Override**, then click **Send**.

	!IMAGE[7o8406n7.jpg](7o8406n7.jpg)

	> [!KNOWLEDGE] Notice that there is a policy tip that has popped up to inform you that there is a credit card number in the email and it is being shared outside the organization.  This type of policy tip can be defined with the Office 365 Security and Compliance center and was pre-staged in the demo tenants we are using.  

1. [] Switch to @lab.VirtualMachine(Client1).SelectLink and review the received email.

	!IMAGE[pidqfaa1.jpg](pidqfaa1.jpg)

	> [!Knowledge] Note that there is no encryption applied to the message.  That is because we set up the rule to only apply to external recipients.  If you were to leave that condition out of the mail flow rule, internal recipients would also receive an encrypted copy of the message.  The image below shows the encrypted message that was received externally.
	>
	>!IMAGE[c5foyeji.jpg](c5foyeji.jpg)
	>
	>Below is another view of the same message received in Outlook Mobile on an iOS device.
	>
	>!IMAGE[599ljwfy.jpg](599ljwfy.jpg)
1. [] Switch to @lab.VirtualMachine(Windows10-CLI4).SelectLink.
1. [] In Microsoft Outlook, click on the **New email** button.

	^IMAGE[Open Screenshot](6wan9me1.jpg)
1. [] Send an email to Alan, Amy, and yourself (+++AlanS;AmyA;@lab.User.Email+++).  For the **Subject** and **Body** type +++Another Test Contoso Internal Email+++.

	^IMAGE[Open Screenshot](d476fmpg.jpg)

1. [] In the Sensitivity Toolbar, click on the **pencil** icon to change the Sensitivity label.

	^IMAGE[Open Screenshot](901v6vpa.jpg)

1. [] Click on **Confidential** and then **Contoso Internal** and click **Send**.

	^IMAGE[Open Screenshot](yhokhtkv.jpg)
1. [] In about a minute, you should receive an **Undeliverable** message from Exchange with the users that the message did not reach and the message you defined in the previous task.

	!IMAGE[kgjvy7ul.jpg](kgjvy7ul.jpg)

> [!HINT] There are many other use cases for Exchange Online mail flow rules but this should give you a quick view into what is possible and how easy it is to improve the security of your sensitive data through the use of Exchange Online mail flow rules and Azure Information Protection.

===

# Exercise 5: SharePoint IRM Configuration

In this exercise, you will configure SharePoint Online Information Rights Management (IRM) and configure a document library with an IRM policy to protect documents that are downloaded from that library.

===
# Enable Information Rights Management in SharePoint Online

In this task, we will enable Information Rights Management in SharePoint Online.

1. [] Switch to **@lab.VirtualMachine(Windows10-CLI4).SelectLink**.

1. [] @[Click here](`cmd.exe/c start shell:AppsFolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge -private https://admin.microsoft.com/AdminPortal/Home#/homepage`) to launch an Edge InPrivate session to +++https://admin.microsoft.com/AdminPortal/Home#/homepage+++.
 
1. [] If needed, log in using +++@lab.CloudCredential(81).Username+++ and the password +++@lab.CloudCredential(81).Password+++.
 
1. [] Hover over the **Admin centers** section of the bar on the left and choose **SharePoint**.

	!IMAGE[r5a21prc.jpg](r5a21prc.jpg)
 
1. [] In the SharePoint admin center click on **settings**.

1. [] Scroll down to the Information Rights Management (IRM) section and select the option button for **Use the IRM service specified in your configuration**.
 
1. [] Click the **Refresh IRM Settings** button.

	!IMAGE[1qv8p13n.jpg](1qv8p13n.jpg)

	>[!HINT] After the browser refreshes, you can scroll down to the same section and you will see a message stating **We successfully refreshed your setings.**
	>
	>!IMAGE[daeglgk9.jpg](daeglgk9.jpg)
 
1. [] Keep the browser open and go to the next task.
 
===

# Site Creation and Information Rights Management Integration

In this task, we will create a new SharePoint site and enable Information Rights Management in a document library.

1. [] In the upper left-hand corner of the page, click on the **app launcher** and click on **SharePoint** in the list.

	!IMAGE[ahiylfbv.jpg](ahiylfbv.jpg)

	!IMAGE[s5wj8fpe.jpg](s5wj8fpe.jpg)

1. [] Dismiss any introductory screens and, at the top of the page, click **+Create site**.

	!IMAGE[7v8wctu2.jpg](7v8wctu2.jpg)
 
1. [] On the Create a site page, click **Team site**.

	^IMAGE[Open Screenshot](406ah98f.jpg)
 
1. [] On the next page, type +++IRM Demo+++ for **Site name** and for the **Site description**, type +++This is a team site for demonstrating SharePoint IRM capabilities+++ and set the **Privacy settings** to **Public - anyone in the organization can access the site** and click **Next**.

	^IMAGE[Open Screenshot](ug4tg8cl.jpg)

1. [] On the Add group members page, click **Finish**.
1. [] In the newly created site, on the left navigation bar, click **Documents**.

	^IMAGE[Open Screenshot](yh071obk.jpg)
 
1. [] In the upper right-hand corner, click the **Settings icon** and click **Library settings**.

	!IMAGE[1qo31rp6.jpg](1qo31rp6.jpg)
 
1. [] On the Documents > Settings page, under **Permissions and Management**, click **Information Rights Management**.

	!IMAGE[ie2rmsk2.jpg](ie2rmsk2.jpg)
 
1. [] On the Settings > Information Rights Management Settings page, check the box next to Restrict permissions on this library on download and under **Create a permission policy title** type +++Contoso IRM Policy+++, and under **Add a permission policy description** type +++This content contained within this file is for use by Contoso Corporation employees only.+++
 
	^IMAGE[Open Screenshot](m9v7v7ln.jpg)
1. [] Next, click on **SHOW OPTIONS** below the policy description and in the **Set additional IRM library settings** section, check the boxes next to **Do not allow users to upload documents that do not support IRM** and **Prevent opening documents in the browser for this Document Library**.

	!IMAGE[0m2qqtqn.jpg](0m2qqtqn.jpg)
	>[!KNOWLEDGE] These setting prevent the upload of documents that cannot be protected using Information Rights Managment (Azure RMS) and forces protected documents to be opened in the appropriate application rather than rendering in the SharePoint Online Viewer.
 
1. [] Next, under the **Configure document access rights** section, check the box next to **Allow viewers to run script and screen reader to function on downloaded documents**.

	!IMAGE[72fkz2ds.jpg](72fkz2ds.jpg)
	>[!HINT] Although this setting may reduce the security of the document, this is typically provided for accessibility purposes.
1. [] Finally, in the **Configure document access rights** section, check the box next to  **Users must verify their credentials using this interval (days)** and type +++7+++ in the text box.

	!IMAGE[tt1quq3f.jpg](tt1quq3f.jpg)
1. [] At the bottom of the page, click **OK** to complete the configuration of the protected document library.
1. [] On the Documents > Settings page, in the left-hand navigation pane, click on **Documents** to return to the document library. section.
 
1. [] Leave the browser open and continue to the next task.
 
===

# Uploading Content to the Document Library

Create an unprotected Word document, label it as Internal, and upload it to the document library. 

1. [] @[Click here](`"C:\\Program Files (x86)\\Microsoft Office\\root\\Office16\\WINWORD.EXE"`) to launch **Microsoft Word**.
1. [] Create a new **Blank document**.

	>[!NOTE] Notice that by default the document is labeled as the unprotected classification **General**.
 
1. [] In the Document, type +++This is a test document+++.
 
1. [] **Save** the document and **close Microsoft Word**.
1. [] Return to the IRM Demo protected document library and click on **Upload > Files**.

	!IMAGE[m95ixvv1.jpg](m95ixvv1.jpg)
1. [] Navigate to the location where you saved the document, select it and click **Open** to upload the file.
 
	>[!NOTE] Note that despite this document being labeled, the Sensitivity is not listed.
 
1. [] To resolve this, on the right-hand side of the document library, click on the **+ Add column** header and click on **More...**.

	!IMAGE[y8h1vf7o.jpg](y8h1vf7o.jpg)
1. [] In the Settings > Create Column page, under **Column name:**, type +++Sensitivity+++.  Verify that **The type of information in this column is:** is set to **Single line of text**, and click **OK**. 

	!IMAGE[66hzke2b.jpg](66hzke2b.jpg)
1. [] In the document library, click on the **Show actions** button to the right of the uploaded document and click **Delete**.

	!IMAGE[gt7sjulo.jpg](gt7sjulo.jpg)
	>[!Note] This is only necessary to expedite the appearance of the Sensitivity metadata.  In production, this would be unnecessary.
1. [] Re-upload the document and you will see that the Sensitivity column is populated.

	!IMAGE[0yr96t56.jpg](0yr96t56.jpg)
1. [] Next, minimize the browser window and right-click on the desktop. Hover over **New >** and click on **Microsoft Access Database**. Name the database +++BadFile+++.

	!IMAGE[e3nxt4a2.jpg](e3nxt4a2.jpg)
1. [] Return to the document library and attempt to upload the file.

	>[!KNOWLEDGE] Notice that you are unable to upload the file because it cannot be protected.
	>	
	>!IMAGE[432hu3pi.jpg](432hu3pi.jpg)
===

# SharePoint IRM Functionality

Files that are uploaded to a SharePoint IRM protected document library are protected upon download based on the user's access rights to the document library.  In this task, we will share a document with Amy Alberts and review the access rights provided.

1. [] Select the uploaded document and click **Share** in the action bar.

	!IMAGE[1u2jsod7.jpg](1u2jsod7.jpg)
1. [] In the Send Link dialog, type +++AmyA+++ and click on **Amy Alberts** then **Send**.

	!IMAGE[j6w1v4z9.jpg](j6w1v4z9.jpg)
1. [] Switch to @lab.VirtualMachine(Windows10-CLI3).SelectLink.
1. [] Open Outlook and click on the email from CIE Administrator, then click on the **Open** link.

	^IMAGE[Open Screenshot](v39ez284.jpg)
1. [] This will launch the IRM Demo document library.  Click on the document to open it in Microsoft Word.

	!IMAGE[xmv9dmvk.jpg](xmv9dmvk.jpg)
1. [] After the document opens, you will be able to observer that it is protected.  Click on the View Permissions button to review the restrictions set on the document.

	!IMAGE[4uya6mro.jpg](4uya6mro.jpg)
	>[!NOTE] These permissions are based on the level of access that they user has to the document library.  In a production environment most users would likely have less rights than shown in this example.
	
===
 
# Exercise 6: Azure Information Protection Scanner

The Azure Information Protection scanner allows you to discover and optionally classify and protect sensitive information stored in on-premises CIFS file shares and SharePoint sites.  

In this exercise, you will configure Azure AD Connect to sync the AIP scanner service account, install the AIP scanner, and acquire an authentication token for the AIP scanner to use. Once the scanner is prepared, you will run a full discovery and then finally classify and protect identified sensitive data.

> [!NOTE] This exercise is based on the preview version of the AIP scanner (1.36.18.0) because some of the PowerShell commands have been updated and will be live in GA version releasing this month. The AIP client has been installed on SQL1 to save time but is always available at https://aka.ms/AIPClient.

# Azure AD Connect Configuration

To authenticate to the Azure Information Protection service, the AIP scanner service account must exist in on-premises Active Directory and in Azure Active Directory.  To accomplish this, you can install Azure AD Connect and configure it using the express settings.

1. [] Switch to @lab.VirtualMachine(DC1).SelectLink and (if necessary) log in using the username +++@lab.VirtualMachine(DC1).Username+++ and the password +++@lab.VirtualMachine(DC1).Password+++.
1. [] On the desktop, double-click on **AzureADConnect.msi**.
1. [] When prompted, click **Run** to continue.
1. [] On the Welcome page, check the box next to **I agree to the license terms and privacy notice** and click the **Continue** button.
1. [] On the Express Settings page, click **Use express settings**.

	^IMAGE[Open Screenshot](00y2goa8.jpg)
1. [] On the Connect to Azure AD page, enter the username +++@lab.CloudCredential(81).Username+++ and the password +++@lab.CloudCredential(81).Password+++ and press the **Next** button.
	
	^IMAGE[Open Screenshot](8fxbjj2i.jpg)
	> [!knowledge] The wizard will connect to the Microsoft Online tenant to verify the credentials.
	>
	>!IMAGE[d1xts1td.jpg](d1xts1td.jpg)

1. [] On the Connect to AD DS page, enter +++Contoso\Install+++ in the username field and +++Somepass1+++ in the password field, then click the **Next** button.

	^IMAGE[Open Screenshot](b21cm4qt.jpg)
1. [] On the Azure AD sign-in page, check the box next to **Continue without matching all UPN suffixes to verified domains** and click the **Next** button.

	^IMAGE[Open Screenshot](4r9j29rl.jpg)

	> [!NOTE] Verified domains are primarily for SSO purposes and are not needed for this lab

1. [] On the Configure page, click the Install button

	> [!ALERT] WARNING: Do not uncheck the box for initial synchronization

1. [] Continue to the next task leaving the Azure AD Connect sync process running

===

# Installing the AIP Scanner Service

The first step in configuring the AIP Scanner is to install the service and connect the database.  This is done with the Install-AIPScanner cmdlet that is provided by the AIP Client software.  The AIPScanner service account has been pre-staged in Active Directory for convenience.

1. [] Switch to @lab.VirtualMachine(SQL1).SelectLink and (if necessary) log in using the username +++@lab.VirtualMachine(SQL1).Username+++ and the password +++@lab.VirtualMachine(SQL1).Password+++.
1. [] Right-click on the **PowerShell** icon in the taskbar and click on **Run as Administrator**.

	!IMAGE[7to6p334.jpg](7to6p334.jpg)
1. [] At the PowerShell prompt, type +++Install-AIPScanner+++ and press **Enter**.
1. [] When prompted, provide the credentials for the AIP scanner service account (+++Contoso\AIPScanner+++) and password (+++Somepass1+++).

	^IMAGE[Open Screenshot](pc9myg9x.jpg)
1. [] When prompted for SqlServerInstance, type +++SQL1+++ and press **Enter**.

	> [!knowledge] You should see a success message like the one below. 
	>
	>!IMAGE[w7goqgop.jpg](w7goqgop.jpg)

1. [] Right-click on the **Windows** button in the lower left-hand corner and click on **Run**.
1. [] In the Run dialog, type +++Services.msc+++ and click **OK**.

	^IMAGE[Open Screenshot](h0ys0h4u.jpg)
1. [] In the Services console, double-click on the **Azure Information Protection Scanner** service.
1. [] On the **Log On** tab of the Azure Information Protection Scanner Service Properties, verify that **Log on as:** is set to the **Contoso\AIPScanner** service account.

	^IMAGE[Open Screenshot](ek9jsd0a.jpg)
===

# Creating Azure AD Applications for the AIP Scanner

Now that you have installed the scanner, you need to get an Azure AD token for the scanner service account to authenticate so that it can run unattended.

1. [] Click on Internet Explorer in the taskbar and navigate to +++https://portal.azure.com+++.

	^IMAGE[Open Screenshot](qyunuz74.jpg)
1. [] At the Sign in to Microsoft Azure page, enter the username +++@lab.CloudCredential(81).Username+++ and the password +++@lab.CloudCredential(81).Password+++.
1. [] In the Microsoft Azure portal, click on **Azure Active Directory** in the left-hand pane.

	^IMAGE[Open Screenshot](njrgwpzi.jpg)
1. [] Under Manage, click on **App registrations**.

	^IMAGE[Open Screenshot](aryms6ao.jpg)
1. [] In the Contoso - App registrations blade, click the **+ New application registration** button.

	!IMAGE[xbyh1hme.jpg](xbyh1hme.jpg)
1. [] In the Create blade, use the values in the table below to create the registration and click **Create**.

	| Setting | Value |
	|:--------|:------|
	| Name | +++AIPOnBehalfOf+++ |
	|Application type | Web app / API |
	|Sign-on URL | +++http://localhost+++ |

	^IMAGE[Open Screenshot](xw6pldwz.jpg)
1. [] In the AIPOnBehalfOf blade, hover the mouse over the **Application ID** and click on the **Click to copy icon** when it appears.

	!IMAGE[6hb5h9jj.jpg](6hb5h9jj.jpg)

	!IMAGE[x8h9gsz7.jpg](x8h9gsz7.jpg)
1. [] Minimize (**DO NOT CLOSE**) Internet Explorer and other windows to show the desktop.
1. [] On the desktop, double-click on **Set-AIPAuthentication.txt**.
1. [] Replace `<ID of the "Web app / API" application>` with the **copied Application ID value**.

	> [!ALERT] WARNING: Ensure there is only a single space after the Application ID before -webAppKey
	>
	>!IMAGE[jppkimfn.jpg](jppkimfn.jpg)

1. [] Return to the browser and click on the **Settings** button.

	^IMAGE[Open Screenshot](3btn5r2d.jpg)
1. []  In the Settings blade, under API ACCESS, click on **Keys**.

	^IMAGE[Open Screenshot](eax6b8au.jpg)
1. [] In the Keys blade, add a new key by typing +++AIPClient+++ in the **Key description** field and your **choice of duration** (1 year, 2 years, or never expires).

	^IMAGE[Open Screenshot](5xlf97xb.jpg)
1. [] Click **Save** and copy the **Value** that is displayed.

	^IMAGE[Open Screenshot](9h9xvfmv.jpg)

	> [!ALERT] WARNING: Do not dismiss this screen until you have saved the value as you cannot retrieve it later

1. [] Go back to Set-AIPAuthentication.txt and replace `<key value generated in the \"Web app \/ API\" application\>` with the copied key value.

	> [!ALERT] WARNING: Ensure there is only a single space after the Application Key before -nativeAppId

1. [] Return to the browser and near the top, click on **Contoso - App registrations**.

	!IMAGE[dmubirqp.jpg](dmubirqp.jpg)

1. [] In the Contoso - App registrations blade, click the **+ New application registration** button.

	!IMAGE[xbyh1hme.jpg](xbyh1hme.jpg)
1. [] In the Create blade, use the values in the table below to create the registration and click **Create**.

	| Setting | Value |
	|:--------|:------|
	Name|+++AIPClient+++
	Application type|Native Application
	Sign-on URL|+++http://localhost+++

	^IMAGE[Open Screenshot](vykjnl5w.jpg)
1. [] In the AIPClient blade, hover the mouse over the **Application ID** and click on the **Click to copy icon** when it appears.

	!IMAGE[6vl5hp2t.jpg](6vl5hp2t.jpg)
1. [] Replace `<ID of the "Native" application>` in Set-AIPAuthentication.txt with the copied **Application ID value**. 

	> [!knowledge] The completed command should look like the image below.
	>
	> !IMAGE[hjqegzet.jpg](hjqegzet.jpg)
1. [] Return to the browser and in the AIPClient blade, click on **Settings**.

	^IMAGE[Open Screenshot](mo6cpf8z.jpg)
1. [] In the Settings blade, under API ACCESS, select **Required permissions**.

	^IMAGE[Open Screenshot](u84lproq.jpg)
1. [] On the Required permissions blade, click **Add**.

	^IMAGE[Open Screenshot](k8kn9cnf.jpg)
1. [] In the Add API Access, click **Select an API**.

	^IMAGE[Open Screenshot](3nji2hus.jpg)
1. [] In the search box, type +++AIP+++ and click on **AIPOnBehalfOf**, and then click the **Select** button.

	^IMAGE[Open Screenshot](a9dqzgvw.jpg)
1. [] On the Enable Access blade, check the box next to **AIPOnBehalfOf**, click the **Select** button.

	^IMAGE[Open Screenshot](obm3tlwf.jpg)
1. [] In the Add API access blade, click **Done**.

	^IMAGE[Open Screenshot](bvdlase2.jpg)
1. [] In the Required permissions blade, click **Grant permissions** and in the popup click **Yes**.

	!IMAGE[rb9n8yw3.jpg](rb9n8yw3.jpg)
1. [] Click on the Start menu and type +++PowerShell+++, right-click on the PowerShell program, and click **Run as a different user**.

	!IMAGE[zgt5ikxl.jpg](zgt5ikxl.jpg)
1. [] When prompted, enter the username +++Contoso\AIPScanner+++ and password +++Somepass1+++ and click **OK**.

	^IMAGE[Open Screenshot](0jmr2zbz.jpg)
1. [] Return to Visual Studio Code and **copy the completed command** from Set-AIPAuthentication.txt 
1. [] **Paste the Set-AIPAuthentication command** into the PowerShell window running as **Contoso\AIPScanner** and press **Enter**.
1. [] When prompted, enter the user +++AIPScanner@@lab.CloudCredential(81).TenantName+++ and the password +++Somepass1+++

	^IMAGE[Open Screenshot](qfxn64vb.jpg)
	>[!knowledge] You will a message like the one below in the PowerShell window once complete.
	>
	>!IMAGE[y2bgsabe.jpg](y2bgsabe.jpg)
	^IMAGE[Open Screenshot](y2bgsabe.jpg)

===

# Reviewing Initial Document State

In this task, we will review documents in SharePoint and a file share to show that there is sensitive information sitting unprotected on the network.

1. [] Switch to @lab.VirtualMachine(Client1).SelectLink and (if necessary) log in using the Install account with a password of +++Somepass1+++.
 
2. [] @[Click here](`cmd.exe/c start shell:AppsFolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge -private http://sp1/documents`) to navigate to http://sp1/documents. Provide the credentials +++Install+++ and +++Somepass1+++ if prompted.
 
	^IMAGE[Open Screenshot](hipavcx6.jpg)
3. [] Open one of the Contoso Purchasing Permissions documents or Run For The Cure spreadsheets.
 
 	
	
	> [!NOTE] Observe that although there is Credit Card or SSN data in these documents, they are still unprotected and classified as General. You can also see these same documents on a file share at \\fileserver\documents in their unprotected state.  Close any opened documents **without saving**.
	>
	>!IMAGE[Open Screenshot](htd29fpf.jpg)

=== 

# Reviewing AIP Scanner Status

Now that the AIP Scanner has been fully installed and authenticated, we can take a look at the initial state of the Scanner.

1. [] Switch to @lab.VirtualMachine(SQL1).SelectLink.
1. [] In the open PowerShell window from the previous task, type +++Get-AIPScannerStatus+++ and press **Enter**.

	^IMAGE[Open Screenshot](jltma5e0.jpg)

	> [!NOTE] Notice that the scanner starts in an **Idle** state.
1. [] Next, type +++Get-AIPScannerConfiguration+++ and **Enter**. 

	^IMAGE[Open Screenshot](bso8342f.jpg)

	> [!NOTE] Review the settings here.  Note that **Enforce** is **Off** by default, the **Schedule** is set to **Manual**, and **DiscoverInformationTypes** is set to **PolicyOnly**.  
	>
	>This means that once I configure repositories, if I run`Start-AIPScan`the scanner would **run once**, would **identify sensitive data based only on Automatic conditions I have configured** in the portal, and if it found those, **it would not classify or protect those files**.  Over the next several tasks we will modify these settings to change the functionality of the scanner.
===

# Configuring AIP Scanner Policy

Because we have configured a default label for our Global policy, we will need to create a scoped policy for the AIP scanner to prevent all of the scanned files that do not match a rule from being labeled as General.  

1. [] Open the browser that is logged into the Azure Portal.


1. [] Type the word +++info+++ into the **search bar** and then click on **Azure Information Protection**. 

	^IMAGE[Open Screenshot](2598c48n.jpg)
	
	> [!HINT] If you do not see the search bar at the top of the portal, click on the **Magnifying Glass** icon to expand it.
	>
	> !IMAGE[ny3fd3da.jpg](ny3fd3da.jpg)
1. [] In the Azure Information Protection blade, under **classifications** on the left, click on **Policies** then click on **+ Add a new policy**.

	^IMAGE[Open Screenshot](p48amj5j.jpg)
1. [] On the Policy blade, type +++No Default Label+++ for Policy name then click on **Select which users or groups get this policy. Froups must be mail-enabled**.

	^IMAGE[Open Screenshot](a7wm5ewz.jpg)
1. [] In the AAD Users and Groups blade, click on **Users/Groups**.  
1. [] Then in the second AAD Users and Groups blade, type +++AIP+++ in the Select textbox and check the box next to **AIP Scanner**.
1. [] Click the **Select** button.
1. [] Finally, click **OK**.

	^IMAGE[Open Screenshot](la3ewp05.jpg)
1. [] In the Policy blade, click the drop-down under **Select the default label** and choose **None** and turn **Off** the setting requiring **All documents and emails must have a label**. 

	^IMAGE[Open Screenshot](le4qy36w.jpg)
1. [] Click **Save**, then **OK** to complete creation of the No Default Label Policy.

	^IMAGE[Open Screenshot](skllt8x1.jpg)

===

# Configuring Repositories

In this task, we will configure repositories to be scanned by the AIP scanner.  As previously mentioned, these can be any type of CIFS file shares including NAS devices sharing over the CIFS protocol.  Additionally, On premises SharePoint 2010, 2013, and 2016 document libraries and lists (attachements) can be scanned.  You can even scan entire SharePoint sites by providing the root URL of the site.  There are several optional 

> [!NOTE] SharePoint 2010 is only supported for customers who have extended support for that version of SharePoint.

The next task is to configure repositories to scan.  These can be on-premises SharePoint 2010, 2013, or 2016 document libraries and any accessible CIFS based share.

1. [] In the PowerShell window on SQL1 run the commands below

    ```
    Add-AIPScannerRepository -Path http://sp1/documents
	```
	```
	Add-AIPScannerRepository -Path \\fileserver\documents
    ```
	^IMAGE[Open Screenshot](00niixfd.jpg)
1. [] To verify the repositories configured, run the command below.
	
    ```
    Get-AIPScannerRepository
    ```
	!IMAGE[Open Screenshot](n5hj5e7j.jpg)
===

# Running Sensitive Data Discovery

1. [] Run the commands below to run a discovery cycle.

    ```
	Set-AIPScannerConfiguration -DiscoverInformationTypes All
	```
	```
	Start-AIPScan
    ```

	> [!Knowledge] Note that we used the DiscoverInformationTypes -All switch before starting the scan.  This causes the scanner to use any custom conditions that you have specified for labels in the Azure Information Protection policy, and the list of information types that are available to specify for labels in the Azure Information Protection policy.  Although the scanner will discover documents to classify, it will not do so because the default configuration for the scanner is Discover only mode.
 	
1. [] Right-click on the **Windows** button in the lower left-hand corner and click on **Event Viewer**.
 
	^IMAGE[Open Screenshot](cjvmhaf0.jpg)
1. [] Expand **Application and Services Logs** and click on **Azure Information Protection**.

	^IMAGE[Open Screenshot](dy6mnnpv.jpg)
 
	>[!NOTE] You will see an event like the one below when the scanner completes the cycle.
	>
	>!IMAGE[o46iabfu.jpg](o46iabfu.jpg)
 
1. [] Next, switch to @lab.VirtualMachine(Windows10-CLI4).SelectLink and @[Click here](`start \\sql1\c$\Users\aipscanner\appdata\local\Microsoft\MSIP\Scanner\Reports`) to browse to \\\sql1\c$\users\aipscanner\AppData\Local\Microsoft\MSIP\Scanner\Reports and review the summary txt and detailed csv files available there.  

	>[!Hint] Since there are no Automatic conditions configured yet, the scanner found no matches for the 100 files scanned despite 96 of them having sensitive data.
	>
	>!IMAGE[m79emvr8.jpg](m79emvr8.jpg)
	>
	>The details contained in the DetailedReport.csv can be used to identify the types of sensitive data you need to create AIP rules for in the Azure Portal.
	>
	>!IMAGE[9y52ab7u.jpg](9y52ab7u.jpg)

===

# Configuring Automatic Conditions

Now that we know what types of sensitive data we need to protect, we will configure some automatic conditions (rules) that the scanner can use to classify and protect content.

1. [] Switch back to @lab.VirtualMachine(SQL1).SelectLink and open the browser that is logged into the Azure Portal.

1. [] Under **classifications** on the left, click **Labels** then expand **Confidential**, and click on **Contoso Internal**.

	^IMAGE[Open Screenshot](jyw5vrit.jpg)
1. [] In the Label: Contoso Internal blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	!IMAGE[cws1ptfd.jpg](cws1ptfd.jpg)
1. [] In the Condition blade, in the **Select information types** search box, type +++SSN+++ and check the box next to the 3 **Social Security Number** entries.

	^IMAGE[Open Screenshot](a6dfnuyz.jpg)
1. [] Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](41o5ql2y.jpg)
1. [] In the Label : Contoso Internal blade, under **Select how this label is applied: automatically or recommended to user**, click **Automatic**.

	^IMAGE[Open Screenshot](1ifaer4l.jpg)

1. [] Click **Save** in the Label: Contoso Internal blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](rimezmh1.jpg)
1. [] Press the **X** in the upper right-hand corner to close the Label: Contoso Internal blade.

===

# Enforcing Configured Rules

In this task, we will se the AIP scanner to enforce the conditions we set up in the previous task and have it rerun on all files using the Start-AIPScan -Reset command.

1. [] Run the commands below to run an enforced scan using defined policy.

    ```
	Set-AIPScannerConfiguration -Enforce On -DiscoverInformationTypes PolicyOnly
	```
	```
	Start-AIPScan -Reset
    ```

	> [!HINT] Note that this time we used the DiscoverInformationTypes -PolicyOnly switch before starting the scan. This will have the scanner only evaluate the conditions we have explicitly defined in conditions.  This increases the effeciency of the scanner and thus is much faster.  After reviewing the event log we will see the result of the enforced scan.
	>
	>!IMAGE[k3rox8ew.jpg](k3rox8ew.jpg)
	>
	>If we switch back to @lab.VirtualMachine(Windows10-CLI4).SelectLink and look in the reports directory we opened previously, you will notice that the old scan reports are zipped in the directory and only the most recent results aare showing.  
	>
	>!IMAGE[s8mn092f.jpg](s8mn092f.jpg)
	>
	>Also, the DetailedReport.csv now shows the files that were protected.
	>
	>
	>!IMAGE[6waou5x3.jpg](6waou5x3.jpg)
	>
	>^IMAGE[Open Fullscreen](6waou5x3.jpg)

===

# Reviewing Protected Documents

Now that we have Classified and Protected documents using the scanner, we can review the documents we looked at previously to see their change in status.

1. [] Switch to @lab.VirtualMachine(Client1).SelectLink and (if necessary) log in using the Install account with a password of +++Somepass1+++.
 
2. [] @[Click here](`cmd.exe/c start shell:AppsFolder\Microsoft.MicrosoftEdge_8wekyb3d8bbwe!MicrosoftEdge -private http://sp1/documents`) to navigate to ***http://sp1/documents***. Provide the credentials +++Install+++ and +++Somepass1+++ if prompted.
 
	^IMAGE[Open Screenshot](hipavcx6.jpg)
3. [] Open one of the Contoso Purchasing Permissions documents or Run For The Cure spreadsheets.
 
 	
	
	> [!NOTE] Observe that the same document is now classified as Confidential \ Contoso Internal. You can also see these same documents on a file share at \\fileserver\documents in their new protected state.
	>
	>!IMAGE[s1okfpwu.jpg](s1okfpwu.jpg)


===

# CONGRATULATIONS!

!IMAGE[kt7yaogd.jpg](kt7yaogd.jpg)

!IMAGE[MSIgnite_MVP Speaker_TW.png](MSIgnite_MVP Speaker_TW.png)
