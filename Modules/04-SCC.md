===

# Security and Compliance Center
[:arrow_left: Home](#azure-information-protection)

In this exercise, we will migrate your AIP Labels and activate them in the Security and Compliance Center.  This will allow you to see the labels in Microsoft Information Protection based clients such as Office 365 for Mac and Mobile Devices.

Although we will not be demonstrating these capabilities in this lab, you can use the tenant information provided to test on your own devices.

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

1. [] In the Security and Compliance Center, under Classifications, click on **Label policies**.

2. [] In the Label policies pane, click **Publish labels**.

   ^IMAGE[Open Screenshot](\Media\SCC01.png)

3. [] On the Choose labels to publish page, click the **Choose labels to publish** link.

   ^IMAGE[Open Screenshot](\Media\SCC02.png)

4. [] In the Choose labels pane, click the + Add button.

   ^IMAGE[Open Screenshot](\Media\SCC03.png)

5. [] Click the box next to Display name to select all labels, then click the Add button.

   ^IMAGE[Open Screenshot](\Media\SCC04.png)

6. [] Click the Done button.

   ^IMAGE[Open Screenshot](\Media\SCC05.png)

7. [] Back on the Choose labels to publish page, click the Next button.

   ^IMAGE[Open Screenshot](\Media\SCC06.png)

8. [] On the Publish to users and groups page, notice that All users are included by default. If you were creating a scoped policy, you would choose specific users or groups to publish to. Click Next.

   ^IMAGE[Open Screenshot](\Media\SCC07.png)

9. [] On the Policy settings page, select the General label from the drop-down next to Apply this label by default to documents and email.

10. [] Check the box next to Users must provide justification to remove a label or lower classification label and click the Next button.

    !IMAGE[Open Screenshot](\Media\SCC08.png)

11. [] In the Name textbox, type ```Global Policy``` and for the Description type ```This is the default global policy for all users.``` and click the Next button.

    ^IMAGE[Open Screenshot](\Media\SCC09.png)

12. [] Finally, on the Review your settings page, click the Publish button.

    !IMAGE[Open Screenshot](\Media\SCC10.png)
