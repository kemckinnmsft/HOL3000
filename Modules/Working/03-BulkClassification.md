===
# Bulk Classification
[:arrow_left: Home](#azure-information-protection)

In this task, we will perform bulk classification using the built-in functionality of the AIP client.  This can be useful for users that want to classify/protect many documents that exist in a central location or locations identified by scanner discovery.  Because this is done manually, it is an AIP P1 feature.

1. [] On @lab.VirtualMachine(Scanner01).SelectLink, log in with the password +++@lab.VirtualMachine(Scanner01).Password+++.
2. [] Browse to the **C:\\**.
3. [] Right-click on the PII folder and select **Classify and Protect**.
   
   !IMAGE[CandP.png](\Media\CandP.png)
4. [] When prompted, click use another account and use the credentials below to authenticate:

	```AIPScanner@@lab.CloudCredential(82).TenantName```

	```Somepass1```

1. [] In the AIP client Classify and protect interface, select **Highly Confidential\\All Employees** and press **Apply**. 

	!IMAGE[CandP2.png](\Media\CandP2.png)

> [!Alert] If you are unable to see the **Apply** button due to screen resolution, click **Alt+A** and **Enter** to apply the label to the content.

> [!NOTE] You may review the results in a text file by clicking show results, or simply close the window.

---

===
# Bulk Classifiation Exercise Complete

In this exercise, we performed bulk classification using the built-in functionality of the AIP client.  This can be useful for users that want to classify/protect many documents that exist in a central location or locations identified by scanner discovery.  Choose one of the exercises below or click the Next button to continue sequentially.

- [AIP Scanner Discovery](#aip-scanner-discovery)
- [Base Configuration](#configuring-azure-information-protection-policy)
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection)
- [Security and Compliance Center](#security-and-compliance-center)
- [AIP Analytics Dashboards](#aip-analytics-dashboards)
- [Exchange IRM](#exchange-online-irm-capabilities)

---

