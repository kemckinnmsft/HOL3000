

- [Workplace Join Clients](#workplace-join-clients)

---
# Workplace Join Clients
[:arrow_up: Top](#lab-environment-configuration)

In this task, we will join 3 systems to the Azure AD tenant to provide SSO capabilities in Office.

1. [] Switch to @lab.VirtualMachine(Client01).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
2. [] Right-click on the start menu and click **Run**.
3. [] In the Run dialog, type ```ms-settings:workplace``` and click **OK**.

	>!IMAGE[mssettings.png](\Media\mssettings.png)

1. [] In the Access Work or School settings menu, click on **+ Connect** and enter the credentials below to workplace join the client.

	```AdamS@@lab.CloudCredential(17).TenantName```

	```pass@word1```
1. [] Click **Done**.
1. [] Switch to @lab.VirtualMachine(Client02).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] Right-click on the start menu and click **Run**.
1. [] In the Run dialog, type ```ms-settings:workplace``` and click **OK**.

	>!IMAGE[mssettings.png](\Media\mssettings.png)

1. [] In the Access Work or School settings menu, click on **+ Connect** and enter the credentials below to workplace join the client.

	```AliceA@@lab.CloudCredential(17).TenantName```

	```pass@word1```
1. [] Click **Done**.
1. [] Switch to @lab.VirtualMachine(Client03).SelectLink and log in with the password +++@lab.VirtualMachine(Client01).Password+++.
1. [] Right-click on the start menu and click **Run**.
1. [] In the Run dialog, type ```ms-settings:workplace``` and click **OK**.

	>!IMAGE[mssettings.png](\Media\mssettings.png)

1. [] In the Access Work or School settings menu, click on **+ Connect** and enter the credentials below to workplace join the client.

	```EvanG@@lab.CloudCredential(17).TenantName```

	```pass@word1```
1. [] Click **Done**.
