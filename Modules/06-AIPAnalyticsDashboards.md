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

- [AIP Scanner Discovery](#aip-scanner-discovery)
- [Base Configuration](#configuring-azure-information-protection-policy)
- [Bulk Classification](#bulk-classification)
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection)
- [Security and Compliance Center](#security-and-compliance-center)
- [Exchange IRM](#exchange-online-irm-capabilities)

---

