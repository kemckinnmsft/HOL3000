===
# Configuring Azure Information Protection Policy
[:arrow_left: Home](#azure-information-protection)

This exercise demonstrates using the Azure Information Protection blade in the Azure portal to configure policies and sub-labels.  We will create a new sub-label and configure protection and then modify an existing sub-label.  We will also create a label that will be scoped to a specific group.  

Next, we will configure AIP Global Policy to use the General sub-label as default, and finally, we will configure a scoped policy to use the new scoped label by default for Word, Excel, and PowerPoint while still using General as default for Outlook. This Exercise will walk you through the items below.

- [Creating, Configuring, and Modifying Sub-Labels](#creating-configuring-and-modifying-sub-labels)
- [Configuring Global Policy](#configuring-global-policy)
- [Creating a Scoped Label and Policy](#creating-a-scoped-label-and-policy)
- [Configuring Advanced Policy Settings](#configuring-advanced-policy-settings)
- [Defining Recommended and Automatic Conditions](#defining-recommended-and-automatic-conditions)

---
## Creating, Configuring, and Modifying Sub-Labels

In this task, we will configure a label protected for internal audiences that can be used to help secure sensitive data within your company.  By limiting the audience of a specific label to only internal employees, you can dramatically reduce the risk of unintentional disclosure of sensitive data and help reduce the risk of successful data exfiltration by bad actors.  

However, there are times when external collaboration is required, so we will configure a label to match the name and functionality of the Do Not Forward button in Outlook.  This will allow users to more securely share sensitive information outside the company to any recipient.  By using the name Do Not Forward, the functionality will also be familiar to what previous users of AD RMS or Azure RMS may have used in the past.

1. [] On @lab.VirtualMachine(Client01).SelectLink, log in with the password +++@lab.VirtualMachine(Client01).Password+++.
2. [] In the Azure Information Protection blade, under **Classifications** in the left pane, click on **Labels** to load the Azure Information Protection – Labels blade.

	^IMAGE[Open Screenshot](\Media\mhocvtih.jpg)

1. [] In the Azure Information Protection – Labels blade, right-click on **Confidential** and click **Add a sub-label**.

	^IMAGE[Open Screenshot](\Media\uktfuwuk.jpg)

1. [] In the Sub-label blade, type ```Contoso Internal``` for the **Label display name** and for **Description** enter text similar to ```Confidential data that requires protection, which allows Contoso Internal employees full permissions. Data owners can track and revoke content.```

	^IMAGE[Open Screenshot](\Media\4luorc0u.jpg)

1. [] Then, under **Set permissions for documents and emails containing this label**, click **Protect**, and under **Protection**, click on **Azure (cloud key)**.

	^IMAGE[Open Screenshot](\Media\tp97a19d.jpg)

1. [] In the Protection blade, click **+ Add Permissions**.

	^IMAGE[Open Screenshot](\Media\layb2pvo.jpg)

1. [] In the Add permissions blade, click on **+ Add contoso – All members** and click **OK**.

	^IMAGE[Open Screenshot](\Media\zc0iuoyz.jpg)

1. [] In the Protection blade, click **OK**.

	^IMAGE[Open Screenshot](\Media\u8jv46zo.jpg)

1. [] In the Sub-label blade, scroll down to the **Set visual marking (such as header or footer)** section and under **Documents with this label have a header**, click **On**.

	> Use the values in the table below to configure the Header.

	| Setting          | Value            |
	|:-----------------|:-----------------|
	| Header text      | ```Contoso Internal``` |
	| Header font size | ```24```               |
	| Header color     | Purple           |
	| Header alignment | Center           |

	> [!NOTE] These are sample values to demonstrate marking possibilities and **NOT** a best practice.

	^IMAGE[Open Screenshot](\Media\0vdoc6qb.jpg)

1. [] To complete creation of the new sub-label, click the **Save** button and then click **OK** in the Save settings dialog.

	^IMAGE[Open Screenshot](\Media\89nk9deu.jpg)

1. [] In the Azure Information Protection - Labels blade, expand **Confidential** (if necessary) and then click on **Recipients Only**.

	^IMAGE[Open Screenshot](\Media\eiiw5zbg.jpg)

1. [] In the Label: Recipients Only blade, change the **Label display name** from **Recipients Only** to ```Do Not Forward```.

	^IMAGE[Open Screenshot](\Media\v54vd4fq.jpg)

1. [] Next, in the **Set permissions for documents and emails containing this label** section, under **Protection**, click **Azure (cloud key): User defined**.

	^IMAGE[Open Screenshot](\Media\qwyranz0.jpg)

1. [] In the Protection blade, under **Set user-defined permissions (Preview)**, verify that only the box next to **In Outlook apply Do Not Forward** is checked, then click **OK**.

	^IMAGE[Open Screenshot](\Media\16.png)

	> [!knowledge] Although there is no action added during this step, it is included to show that this label will only display in Outlook and not in Word, Excel, PowerPoint or File Explorer.

1. [] Click **Save** in the Label: Recipients Only blade and **OK** to the Save settings prompt. 

	^IMAGE[Open Screenshot](\Media\9spkl24i.jpg)

1. []  Click the **X** in the upper right corner of the blade to close.

	^IMAGE[Open Screenshot](\Media\98pvhwdv.jpg)

---

## Configuring Global Policy
[:arrow_up: Top](#exercise-2-configuring-azure-information-protection-policy)

In this task, we will assign the new sub-label to the Global policy and configure several global policy settings that will increase Azure Information Protection adoption among your users and reduce ambiguity in the user interface.

1. [] In the Azure Information Protection blade, under **Classifications** on the left, click **Policies**. 
2. [] Click the **Global** policy.

	^IMAGE[Open Screenshot](\Media\24qjajs5.jpg)

1. [] In the Policy: Global blade, **wait for the labels to load**.

	>[!knowledge] The policies should look like the image below.  If they show as loading, refresh the full browser on this page and go back into the **Global** policy and they should load.
	>
	>!IMAGE[labels.png](\Media\labels.png)

2. [] Below the labels, click **Add or remove labels**.

3. [] In the Policy: Add or remove labels blade, ensure that the **boxes** next to **all labels including the new Contoso Internal label** are **checked** and click **OK**.

4. [] In the Policy: Global blade, under the **Configure settings to display and apply on Information Protection end users** section, configure the policy to match the settings shown in the table and image below.

	| Setting | Value |
	|:--------|:------|
	| Select the default label | General |
	|All documents and emails must have a label…|On
	Users must provide justification to set a lower…|On
	For email messages with attachments, apply a label…|Automatic
	Add the Do Not Forward button to the Outlook ribbon|Off

	!IMAGE[Open Screenshot](\Media\mtqhe3sj.jpg)

1. [] Click **Save**, then **OK** to complete configuration of the Global policy.

	^IMAGE[Open Screenshot](\Media\1p1q4pxe.jpg)

1. [] Click the **X** in the upper right corner to close the Policy: Global blade.

	^IMAGE[Open Screenshot](\Media\m6e4r2u2.jpg)

---

## Creating a Scoped Label and Policy
[:arrow_up: Top](#exercise-2-configuring-azure-information-protection-policy)

Now that you have learned how to work with global labels and policies, we will create a new scoped label and policy for the Legal team at Contoso.  

1. [] Under **Classifications** on the left, click **Labels**.

	^IMAGE[Open Screenshot](\Media\50joijwb.jpg)

1. [] In the Azure Information Protection – Labels blade, right-click on **Highly-Confidential** and click **Add a sub-label**.

	^IMAGE[Open Screenshot](\Media\tasz9t0i.jpg)

1. [] In the Sub-label blade, enter ```Legal Only``` for the **Label display name** and for **Description** enter ```Data is classified and protected. Legal department staff can edit, forward and unprotect.```.

	^IMAGE[Open Screenshot](\Media\lpvruk49.jpg)

1. [] Then, under **Set permissions for documents and emails containing this label**, click **Protect** and under **Protection**, click **Azure (cloud key)**.

	^IMAGE[Open Screenshot](\Media\6ood4jqu.jpg)

1. [] In the Protection blade, under **Protection settings**, click the **+ Add permissions** link.

	!IMAGE[ozzumi7l.jpg](\Media\ozzumi7l.jpg)

1. [] In the Add permissions blade, click **+ Browse directory**.

	^IMAGE[Open Screenshot](\Media\2lvwim24.jpg)

1. [] In the AAD Users and Groups blade, **wait for the names to load**, then check the boxes next to **Adam Smith** and **Alice Anderson**, and click the **Select** button.

	^IMAGE[Open Screenshot](\Media\uishk9yh.jpg)

	> [!Note] In a production environment, you will typically use a synced or Azure AD Group rather than choosing individuals.

1. [] In the Add permissions blade, click **OK**.

	^IMAGE[Open Screenshot](\Media\stvnaf4f.jpg)

1. [] In the Protection blade, under **Allow offline access**, reduce the **Number of days the content is available without an Internet connection** value to ```3``` and press **OK** .

	> [!Knowledge] This value determines how many days a user will have offline access from the time a document is opened, and an initial Use License is acquired.  While this provides convenience for users, it is recommended that this value be set appropriately based on the sensitivity of the content.

	^IMAGE[Open Screenshot](\Media\j8masv1q.jpg)

1. [] Click **Save** in the Sub-label blade and **OK** to the Save settings prompt to complete the creation of the Legal Only sub-label.

	^IMAGE[Open Screenshot](\Media\dfhoii1x.jpg)

1. [] In the Azure Information Protection blade, under **Classifications** on the left, click **Policies** then click the **+Add a new policy** link.

	^IMAGE[Open Screenshot](\Media\ospsddz6.jpg)

1. [] In the Policy blade, for Policy name, type ```No Default Label Scoped Policy``` and click on **Select which users or groups get this policy. Groups must be email-enabled.**

	!IMAGE[1sjw3mc7.jpg](\Media\1sjw3mc7.jpg)

1. [] In the AAD Users and Groups blade, click on **Users/Groups**.  
1. [] Then in the second AAD Users and Groups blade, **wait for the names to load** and check the boxes next to **AIPScanner**, **Adam Smith**, and **Alice Anderson**.

	>[!NOTE] The **AIPScanner** account is added here to prevent all scanned documents from being labeled with a default label.
1. [] Click the **Select** button.
1. [] Finally, click **OK**.

	^IMAGE[Open Screenshot](\Media\onne7won.jpg)

1. [] In the Policy blade, under the labels, click on **Add or remove labels** to add the scoped label.

	!IMAGE[b6e9nbui.jpg](\Media\b6e9nbui.jpg)

1. [] In the Policy: Add or remove labels blade, check the box next to **Legal Only** and click **OK**.

	^IMAGE[Open Screenshot](\Media\c2429kv9.jpg)

1. [] In the Policy blade, under **Configure settings to display and apply on Information Protection end users** section, under **Select the default label**, select **None** as the default label for this scoped policy.

	!IMAGE[4mxceage.jpg](\Media\4mxceage.jpg)

1. [] Click **Save**, then **OK** to complete creation of the No Default Label Scoped Policy.

	^IMAGE[Open Screenshot](\Media\41jembjf.jpg)

1. [] Click on the **X** in the upper right-hand corner to close the policy.

---

## Configuring Advanced Policy Settings
[:arrow_up: Top](#exercise-2-configuring-azure-information-protection-policy)

There are many advanced policy settings that are useful to tailor your Azure Information Protection deployment to the needs of your environment.  In this task, we will cover one of the settings that is very complimentary when using scoped policies that have no default label or a protected default label.  Because the No Default Label Scoped Policy we created in the previous task uses a protected default label, we will be adding an alternate default label for Outlook to provide a more palatable user experience for those users.

1. [] In the Azure Information Protection blade, under **Classifications** on the left, click on **Labels** and then click on the **General** label.

    ^IMAGE[Open Screenshot](\Media\rvn4xorx.jpg)

1. [] In the Label: General blade, scroll to the bottom and copy the **Label ID** and close the blade using the **X** in the upper right-hand corner.

    !IMAGE[8fi1wr4d.jpg](\Media\8fi1wr4d.jpg)

1. [] In the AIP Portal, under **Classifications** on the left, click on **Policies**. 
1. [] **Right-click** on the **No Default Label Scoped Policy** and click on **Advanced settings**.

    ^IMAGE[Open Screenshot](\Media\2jo71ugb.jpg)

1. [] In the Advanced settings blade, in the textbox under **VALUE**, paste the **Label ID** for the **General** label you copied previously. In the textbox under **NAME**, type ```OutlookDefaultLabel```, then click **Save and close**.

    > [!ALERT] CAUTION: Please check to ensure that there are **no spaces** before or after the **Label ID** when pasting as this will cause the setting to not apply.

    !IMAGE[ezt8sfs3.jpg](\Media\ezt8sfs3.jpg)

	> [!HINT] This and additional Advanced Policy Settings can be found at [https://docs.microsoft.com/en-us/azure/information-protection/rms-client/client-admin-guide-customizations ](https://docs.microsoft.com/en-us/azure/information-protection/rms-client/client-admin-guide-customizations)
