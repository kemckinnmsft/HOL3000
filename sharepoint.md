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