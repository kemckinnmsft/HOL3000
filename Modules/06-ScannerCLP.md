===
# Classification, Labeling, and Protection with the Azure Information Protection Scanner
[:arrow_left: Home](#azure-information-protection)

The Azure Information Protection scanner allows you to  classify and protect sensitive information stored in on-premises CIFS file shares and SharePoint sites.  

In this exercise, you will change the condition we created previously from a recommended to an automatic classification rule.  After that, we will run the AIP Scanner in enforce mode to classify and protect the identified sensitive data. This Exercise will walk you through the items below.

- [Configuring Automatic Conditions](#configuring-automatic-conditions)
- [Enforcing Configured Rules](#enforcing-configured-rules)
- [Reviewing Protected Documents](#reviewing-protected-documents)
- [Reviewing the Dashboards](#reviewing-the-dashboards)

---

## Configuring Automatic Conditions

Now that we know what types of sensitive data we need to protect, we will configure some automatic conditions (rules) that the scanner can use to classify and protect content.

1. [] Switch back to @lab.VirtualMachine(Client01).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
2. [] Open the browser that is logged into the Azure Portal.

3. [] Under **Classifications** on the left, click **Labels** then expand **Confidential**, and click on **Contoso Internal**.

4. [] In the Label : Contoso Internal blade, under **Select how this label is applied: automatically or recommended to user**, click **Automatic**.

	^IMAGE[Open Screenshot](\Media\1ifaer4l.jpg)

1. [] Click **Save** in the Label: Contoso Internal blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\rimezmh1.jpg)

1. [] Press the **X** in the upper right-hand corner to close the Label: Contoso Internal blade.

---

## Enforcing Configured Rules
[:arrow_up: Top](#exercise-5-classification-labeling-and-protection-with-the-azure-information-protection-scanner)

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
	>
	>If we switch back to @lab.VirtualMachine(Client01).SelectLink and look in the reports directory we opened previously at ```\\Scanner01.contoso.azure\c$\users\aipscanner\AppData\Local\Microsoft\MSIP\Scanner\Reports```, you will notice that the old scan reports are zipped in the directory and only the most recent results are showing.  
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

---

## Reviewing Protected Documents
[:arrow_up: Top](#exercise-5-classification-labeling-and-protection-with-the-azure-information-protection-scanner)

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

    > [!NOTE] Observe that the document is classified as Confidential \ Contoso Internal. 
    >
    >!IMAGE[s1okfpwu.jpg](\Media\s1okfpwu.jpg)

4. [] Next, in the same documents folder, open one of the pdf files.
5. [] When prompted by Adobe, enter ```AdamS@@lab.CloudCredential(82).TenantName``` and press OK.
6. [] Check the box to save credentials and press OK.

	> [!NOTE] The PDF will now open and display the sensitivity across the top of the document.

	> [!Knowledge] The latest version of Acrobat Reader DC and the MIP Plugin have been installed on this system prior to the lab. Additionally, the sensitivity does not display by default in Adobe Acrobat Reader DC.  You must make the modifications below to the registry to make this bar display.
	>
	> In **HKEY_CURRENT_USER\Software\Adobe\Acrobat Reader\DC\MicrosoftAIP**, create a new **DWORD** value of **bShowDMB** and set the **Value** to **1**.
	>
	> !IMAGE[1547416250228](\Media\1547416250228.png)

---
## Reviewing the Dashboards
[:arrow_up: Top](#exercise-5-classification-labeling-and-protection-with-the-azure-information-protection-scanner)

We can now go back and look at the dashboards and observe how they have changed.

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
	
