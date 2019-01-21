===
# Azure Information Protection
[:arrow_left: Home](#introduction)
## Overview

Azure Information Protection (AIP) is a cloud-based solution that can help organizations to protect sensitive information by classifying and (optionally) encrypting documents and emails on Windows, Mac, and Mobile devices. This is done using an organization defined classification taxonomy made up of labels and sub-labels. These labels may be applied manually by users, or automatically by administrators via defined rules and conditions.

The phases of AIP are shown in the graphic below.  

!IMAGE[Phases.png](\Media\Phases.png)

In this lab, we will guide you through addressing all of these phases using some of the newest features of AIP.  We first will perform Discovery using the AIP scanner. We recommend that all customers do this step as it can help to show the current state of your sensitive data so you may make informaed risk decisions. 

We will also show how to use the AIP Scanner in Enforce mode to take advantage of features like Automatic Conditions to help you Classify, Label, and Protect the discovered information easily.

We will help you understand how to Enable and Publish labels in the Security and Compliance Center so they can be used with Mac, Mobile, ISVs (like Adobe PDF), and other unified clients.

Finally, we will demonstrate how to use the new AIP Dashboards to leverage Azure Log Analytics to display actionable information on Usage, Activity, and Data Risk.

!IMAGE[Two overlaying screenshots of the Azure Information Protection scanner's blade in the Azure portal. This blade provides dashboards that consolidate information for all deployed Azure Information Protection scanners, including health status, scan results, classification and policy settings, and more.](\Media\8324-image001.png)

### Objectives

> [!ALERT] Please ensure you have completed the steps in the [Lab Environment Configuration](#lab-environment-configuration) before continuing.

There are 2 options for this Lab.  These options contain similar content except for the items called out below.

- The **New to AIP** option will walk through the label and policy creation including scoped policies and demonstrating recommended and automatic labeling in Office applications. This option takes significantly longer and so there is a chance that all sections may not be completed.

- The **Familiar with AIP** option assumes that you are familiar with label and policy creation and that you have seen the operation of conditions in Office applications as these will not be demonstrated.  This option will use the predefined labels and global policy populated in the demo tenants.

Click on one of the options below to begin.

## [New to AIP](#exercise-1-configuring-aip-scanner-for-discovery)

## [Familiar with AIP](#configuring-aip-scanner-for-discovery)
