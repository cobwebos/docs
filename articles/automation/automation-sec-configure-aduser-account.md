<properties
   pageTitle="配置 Azure AD 用户帐户 | Azure"
   description="本文介绍如何为 Azure 自动化中的 Runbook 配置 Azure AD 用户帐户凭据，以便向 ARM 和 ASM 进行身份验证。"
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="azure active directory 用户, azure 服务管理, azure ad 用户帐户" />
<tags
	ms.service="automation"
	ms.date="05/10/2016"
	wacn.date=""/>

# 使用 Azure 服务管理和 Resource Manager 对 Runbook 进行身份验证

[AZURE.ACOM]{

本文介绍当你为针对 Azure 服务管理 (ASM) 或 Azure Resource Manager (ARM) 资源运行的 Azure 自动化 Runbook 配置 Azure AD 用户帐户时所要执行的步骤。尽管这仍是基于 ARM 的 Runbook 支持的身份验证标识，但建议的方法是使用新的 Azure 运行方式帐户。

[AZURE.ACOM]}

[AZURE.ACN]{

本文介绍当你为针对 Azure 服务管理 (ASM) 或 Azure Resource Manager (ARM) 资源运行的 Azure 自动化 Runbook 配置 Azure AD 用户帐户时所要执行的步骤。

[AZURE.ACN]}

## 创建新的 Azure Active Directory 用户

1. 以你要管理的 Azure 订阅的服务管理员身份登录到 Azure 经典管理门户。
2. 选择“Active Directory”，然后选择组织目录的名称。
3. 选择“用户”选项卡，然后在命令区域中选择“添加用户”。
4. 在“告诉我们有关此用户的信息”页上的“用户类型”下，选择“组织中的新用户”。
5. 输入用户名。
6. 在“Active Directory”页上，选择与你的 Azure 订阅关联的目录名称。
7. 在“用户配置文件”页上，提供名字和姓氏、用户友好名称，并从“角色”列表中选择用户。不要**启用 Multi-Factor Authentication**。
8. 记下该用户的完整名称和临时密码。
9. 选择“设置”>“管理员”>“添加”。
10. 键入你创建的用户的完整用户名。
11. 选择你希望该用户管理的订阅。
12. 从 Azure 注销，然后使用你刚创建的帐户重新登录。系统将提示你更改用户密码。


## 在 Azure 经典管理门户中创建自动化帐户

[AZURE.ACOM]{

在本部分中，你将执行以下步骤以在 Azure 门户中创建一个新的 Azure 自动化帐户，该帐户将用于在 ASM 和 ARM 模式下管理资源的 Runbook 配合使用。

>[AZURE.NOTE] 可以通过 Azure 经典门户、Azure 门户和任何一组 cmdlet 来管理使用 Azure 经典管理门户创建的自动化帐户。创建帐户后，在该帐户中创建和管理资源的方式没有差别。如果你打算继续使用 Azure 经典管理门户，则应该使用它而不是 Azure 门户来创建任何自动化帐户。

[AZURE.ACOM]}

[AZURE.ACN]{

在本部分中，你将执行以下步骤以在 Azure 经典管理门户中创建一个新的 Azure 自动化帐户，该帐户将用于在 ASM 模式下管理资源的 Runbook。

[AZURE.ACN]}

1. 以你要管理的 Azure 订阅的服务管理员身份登录到 Azure 经典管理门户。
2. 选择“自动化”。
3. 在“自动化”页上，选择“创建自动化帐户”。
4. 在“创建自动化帐户”框中键入新自动化帐户的名称，然后从下拉列表中选择**区域**。
5. 单击“确定”接受设置并创建帐户。
6. 帐户在创建后将列在“自动化”页上。
7. 单击该帐户，转到“仪表板”页。
8. 在“自动化仪表板”页上，选择“资产”。
9. 在“资产”页上，选择位于页面底部的“添加设置”。
10. 在“添加设置”页上，选择“添加凭据”。
11. 在“定义凭据”页的“凭据类型”下拉列表中选择“Windows PowerShell 凭据”，并提供凭据名称。
12. 在随后出现的“定义凭据”页上，在“用户名”字段中键入前面创建的 AD 用户帐户的用户名，并在“密码”和“确认密码”字段中键入密码。单击“确定”保存你的更改。

[AZURE.ACOM]{

## 在 Azure 门户中创建自动化帐户

在本部分中，你将执行以下步骤以在 Azure 门户中创建一个新的 Azure 自动化帐户，该帐户将用于在 ARM 模式下管理资源的 Runbook 配合使用。

1. 以你要管理的 Azure 订阅的服务管理员身份登录到 Azure 门户。
2. 选择“自动化帐户”。
3. 在“自动化帐户”边栏选项卡中，单击“添加”。<br>![添加自动化帐户](./media/automation-sec-configure-azure-runas-account/add-automation-acct-properties.png)
2. 在“添加自动化帐户”边栏选项卡的“名称”框中，键入新自动化帐户的名称。
5. 如果有多个订阅，请为新帐户指定一个订阅，并指定一个新的或现有的**资源组**以及 Azure 数据中心的**位置**。
3. 针对“创建 Azure 运行方式帐户”选项选择“否”值，然后单击“创建”按钮。

    >[AZURE.NOTE] 如果选择选项“否”以选择不创建运行方式帐户，则“添加自动化帐户”边栏选项卡中会出现一条警告消息。尽管这会创建帐户并将其分配到订阅中的“参与者”角色，但该帐户在订阅目录服务中没有相应的身份验证标识，因此无法访问订阅中的资源。这将导致引用此帐户的任何 Runbook 都无法进行身份验证并针对 ARM 资源执行任务。

    ![添加自动化帐户警报](./media/automation-sec-configure-azure-runas-account/add-automation-acct-properties-error.png)

4. 在 Azure 创建自动化帐户时，你可以在菜单的“通知”下面跟踪进度。

完成创建凭据后，需要创建一个凭据资产，以便将自动化帐户与前面创建的 AD 用户帐户相关联。请记住，我们只创建了自动化帐户，但它并未与身份验证标识相关联。执行 [Credential assets in Azure Automation（Azure 自动化中的凭据资产）](/documentation/articles/automation-credentials/#creating-a-new-credential)一文中所述的步骤，并以**域\\用户**的格式输入“用户名”的值。

[AZURE.ACOM]}

## 在 Runbook 中使用凭据

可以使用 [Get-AutomationPSCredential](/documentation/articles/automation-credentials/) 活动检索 Runbook 中的凭据，然后将它与 [Add-AzureAccount](http://msdn.microsoft.com/zh-cn/library/azure/dn722528.aspx) 结合使用以连接到你的 Azure 订阅。如果该凭据是多个 Azure 订阅的管理员，则你还应使用 [Select-AzureSubscription](http://msdn.microsoft.com/zh-cn/library/dn495203.aspx) 来指定正确的订阅。下面所示的 Windows PowerShell 通常出现在大多数 Azure 自动化 Runbook 的顶部。

    [AZURE.ACOM]{
    $cred = Get-AutomationPSCredential -Name "myuseraccount.onmicrosoft.com"
	Add-AzureAccount -Credential $cred
    [AZURE.ACOM]}
    [AZURE.ACN]{
    $cred = Get-AutomationPSCredential -Name "myuseraccount.partner.onmschina.cn"
	Add-AzureAccount -Environment AzureChinaCloud -Credential $cred
    [AZURE.ACN]}

	Select-AzureSubscription -SubscriptionName "My Subscription"

应该在 Runbook 中任何[检查点](http://technet.microsoft.com/zh-cn/library/dn469257.aspx#bk_Checkpoints)的后面重复这些行。如果 Runbook 已挂起，然后在另一个工作线程上恢复，则它需要再次执行身份验证。

[AZURE.ACOM]{

## 后续步骤
* 查看 [Azure 自动化 Runbook 类型](/documentation/articles/automation-runbook-types/)一文中所述的不同 Runbook 类型和创建自己的 Runbook 所需的步骤

[AZURE.ACOM]}

<!---HONumber=Mooncake_0725_2016-->