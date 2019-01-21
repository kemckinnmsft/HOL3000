---

## Defining Recommended and Automatic Conditions
[:arrow_up: Top](#exercise-2-configuring-azure-information-protection-policy)

One of the most powerful features of Azure Information Protection is the ability to guide your users in making sound decisions around safeguarding sensitive data.  This can be achieved in many ways through user education or reactive events such as blocking emails containing sensitive data. 

However, helping your users to properly classify and protect sensitive data at the time of creation is a more organic user experience that will achieve better results long term.  In this task, we will define some basic recommended and automatic conditions that will trigger based on certain types of sensitive data.

1. [] Under **Dashboards** on the left, click on **Data discovery (Preview)** to view the results of the discovery scan we performed previously.

	!IMAGE[Dashboard.png](\Media\Dashboard.png)

	> [!KNOWLEDGE] Notice that there are no labeled or protected files shown at this time.  This uses the AIP P1 discovery functionality available with the AIP Scanner. Only the predefined Office 365 Sensitive Information Types are available with AIP P1 as Custom Sensitive Information Types require automatic conditions to be defined, which is an AIP P2 feature.

	> [!NOTE] Now that we know the sensitive information types that are most common in this environment, we can use that information to create **Recommended** conditions that will help guide user behavior when they encounter this type of data.

	> [!ALERT] If no data is shown, it may still be processing. Continue with the lab and come back to see the results later.

1. [] Under **Classifications** on the left, click **Labels** then expand **Confidential**, and click on **Contoso Internal**.

	^IMAGE[Open Screenshot](\Media\jyw5vrit.jpg)
1. [] In the Label: Contoso Internal blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	!IMAGE[cws1ptfd.jpg](\Media\cws1ptfd.jpg)
1. [] In the Condition blade, in the **Select information types** search box, type ```EU``` and check the boxes next to the **items shown below**.

	!IMAGE[xaj5hupc.jpg](\Media\xaj5hupc.jpg)
1. [] Next, before saving, replace EU in the search bar with ```credit``` and check the box next to **Credit Card Number**.

	^IMAGE[Open Screenshot](\Media\9rozp61b.jpg)
1. [] Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\41o5ql2y.jpg)

	> [!Knowledge] By default the condition is set to Recommended and a policy tip is created with standardized text.
	>
	>  !IMAGE[qdqjnhki.jpg](\Media\qdqjnhki.jpg)

1. [] Click **Save** in the Label: Contoso Internal blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\rimezmh1.jpg)
1. [] Press the **X** in the upper right-hand corner to close the Label: Contoso Internal blade.

	^IMAGE[Open Screenshot](\Media\em124f66.jpg)
1. [] Next, expand **Highly Confidential** and click on the **All Employees** sub-label.

	^IMAGE[Open Screenshot](\Media\2eh6ifj5.jpg)
1. [] In the Label: All Employees blade, scroll down to the **Configure conditions for automatically applying this label** section, and click on **+ Add a new condition**.

	^IMAGE[Open Screenshot](\Media\8cdmltcj.jpg)
1. [] In the Condition blade, select the **Custom** tab and enter ```Password``` for the **Name** and in the textbox below **Match exact phrase or pattern**, type ```pass@word1```.

	!IMAGE[ra7dnyg6.jpg](\Media\ra7dnyg6.jpg)
1. [] Click **Save** in the Condition blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\ie6g5kta.jpg)
1. [] In the Labels: All Employees blade, in the **Configure conditions for automatically applying this label** section, click **Automatic**.

   !IMAGE[245lpjvk.jpg](\Media\245lpjvk.jpg)

	> [!HINT] The policy tip is automatically updated when you switch the condition to Automatic.

1. [] Click **Save** in the Label: All Employees blade and **OK** to the Save settings prompt.

	^IMAGE[Open Screenshot](\Media\gek63ks8.jpg)

1. [] Press the **X** in the upper right-hand corner to close the Label: All Employees blade.

	^IMAGE[Open Screenshot](\Media\wzwfc1l4.jpg)
