===
# Azure Information Protection
[:arrow_left: Home](#introduction)
## Overview

Azure Information Protection (AIP) is a cloud-based solution that can help organizations to protect sensitive information by classifying and (optionally) encrypting documents and emails on Windows, Mac, and Mobile devices. This is done using an organization defined classification taxonomy made up of labels and sub-labels. These labels may be applied manually by users, or automatically by administrators via defined rules and conditions.

The phases of AIP are shown in the graphic below.  

!IMAGE[Phases.png](\Media\Phases.png)

In this lab, we will give you options for addressing each of these phases using various features of AIP.  

The [AIP Scanner Discovery](#aip-scanner-discovery) exercise, will guide you through performing **Discovery using the AIP scanner**. We recommend that everyone complete this exercise first as this step is important to help show the current state of sensitive data in on-premises repositories. This enables you to make data based risk decisions that can help drive appropriate levels of urgency around the rest of your AIP deployment. :clock10: 10-15 min

The [Base Configuration](#base-configuration) exercise, contains information on configuring and testing **Global and Scoped Policy and Labels**. This will also include demonstrating **Recommended and Automatic labeling via the AIP client in Office 365 on Windows 10**. This is the longest exercise in the lab as it requires configuration of policy and the use of multiple clients.  We recommend this exercise if you have minimal experience with AIP. :clock10: 30-45 min

The [Bulk Classification](#bulk-classification) exercise, shows how to manually classify, label, and protect content using the Windows integration features of the AIP client. :clock10: 5 min

The [AIP Scanner Classification, Labeling, and Protection](#aip-scanner-classification-labeling-and-protection) exercise, will show how to use the **AIP scanner in Enforce mode** to take advantage of features like Automatic Conditions to help you **Classify, Label, and Protect** the discovered information easily. This exercise has a dependancy on completion of the AIP Scanner Dicovery exercise. :clock10: 5-10 min

The [Security and Compliance Center](#security-and-compliance-center) exercise, will help you understand how to **Enable and Publish labels in the Security and Compliance Center** so they can be used with Mac, Mobile, ISVs (like Adobe PDF), and other unified clients.  We will demonstrate this functionality using the Adobe PDF reader. :clock10: 5-10 min

In the [AIP Analytics Dashboards](#aip-analytics-dashboards) exercise, we will show how to **Monitor AIP Usage, User Activity, and Data Risk** using the new Azure Log Analytics dashboards built into the AIP Azure Portal. :clock10: 5 min

In the [Exchange IRM](#exchange-online-irm-capabilities) exercise, we will use Exchange PowerShell to create a Mail Flow Rule to prevent sensitive information from leaving your network in the clear.  We will also create a mail flow rule that prevents internal protected messages from accidentally being sent to external recipients who will be unable to open the content. :clock10: 10-15 min

Click on one of the options below to begin. At the end of each section, there will be a summary and links to the other sections so you may continue from that point.

- [AIP Scanner Discovery](#aip-scanner-discovery)
- [Base Configuration](#base-configuration)
- [Bulk Classification](#bulk-classification)
- [AIP Scanner CLP](#aip-scanner-classification-labeling-and-protection)
- [Security and Compliance Center](#security-and-compliance-center)
- [AIP Analytics Dashboards](#aip-analytics-dashboards)
- [Exchange IRM](#exchange-online-irm-capabilities)

---

