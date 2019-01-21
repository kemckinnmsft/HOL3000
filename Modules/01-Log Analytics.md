===
## Configuring Azure Log Analytics

In order to collect log data from Azure Information Protection clients and services, you must first configure the log analytics workspace.

1. [] On @lab.VirtualMachine(Client01).SelectLink, log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] In the Azure portal, type the word ```info``` into the **search bar** and press **Enter**, then click on **Azure Information Protection**. 

	!IMAGE[2598c48n.jpg](\Media\2598c48n.jpg)
	
	> [!HINT] If you do not see the search bar at the top of the portal, click on the **Magnifying Glass** icon to expand it.
	>
	> !IMAGE[ny3fd3da.jpg](\Media\ny3fd3da.jpg)

	> If you are not already at the Azure portal, open an Edge InPrivate window and navigate to ```https://portal.azure.com/```
	
	>^IMAGE[Open Screenshot](\Media\cznh7i2b.jpg)

	> [!KNOWLEDGE] If necessary, log in using the username and password below:
	>
	>```@lab.CloudCredential(82).Username``` 
	>
	>```@lab.CloudCredential(82).Password```

1. [] In the Azure Information Protection blade, under **Manage**, click **Configure analytics (preview)**.

1. [] Next, click on **+ Create new workspace**.

	!IMAGE[qu68gqfd.jpg](\Media\qu68gqfd.jpg)
1. [] In the Log analytics workspace using the values in the table below and click **OK**.

	|||
	|-----|-----|
	|OMS Workspace|**Type a unique Workspace Name**|
	|Resource Group|```AIP-RG```|
	|Location|**East US** (Or a location near the event)|

	^IMAGE[Open Screenshot](\Media\5butui15.jpg)
1. [] Next, back in the Configure analytics (preview) blade, **check the boxes** next to the **workspace** and to **Enable Content Matches** and click **OK**.

	!IMAGE[gste52sy.jpg](\Media\gste52sy.jpg)
1. [] Click **Yes**, in the confirmation dialog.

	!IMAGE[zgvmm4el.jpg](\Media\zgvmm4el.jpg)
