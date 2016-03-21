<properties
	pageTitle="登录到 Windows VM | Microsoft Azure"
	description="使用 Azure 管理门户登录到使用 Resource Manager 部署模型创建的 Windows 虚拟机。"
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.date="02/03/2016"
	wacn.date=""/>


# 使用 Azure 管理门户登录到 Windows 虚拟机


在 Azure 管理门户中，使用“连接”按钮启动远程桌面会话，然后登录到 Windows VM。


[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-classic-include.md)]


## 连接到虚拟机

1. 登录到 Azure 管理门户。

2. 单击“虚拟机”，然后选择虚拟机。

3. 在页面底部的命令栏中，单击“连接”。

4. 单击“连接”创建和下载远程桌面协议文件（.rdp 文件）。单击“打开”以使用此文件。

	![登录到虚拟机](./media/arm_log_on_windows_vm/rm_connect_rdp_file.png)
	
5. 在“远程桌面”窗口中，单击“连接”以继续。

	![继续连接](./media/arm_log_on_windows_vm/rm_connect_unknown.png)

	
6. 在“Windows 安全性”窗口中，键入虚拟机上帐户的凭据，然后单击“确定”。

 	在大多数情况下，凭据是创建虚拟机时指定的本地帐户用户名和密码。域是虚拟机的名称，输入格式为“*vmname&#92;username**”。
	
	如果虚拟机属于你的组织的一个域，请确保用户名包含该域的名称，格式为“*Domain&#92;Username*”。该帐户需要属于管理员组或已被授予 VM 的远程访问权限。
	
	如果虚拟机是域控制器，则键入该域的域管理员帐户的用户名和密码。

7.	单击“是”以验证虚拟机的 ID 并完成登录。

	![验证计算机的标识](./media/arm_log_on_windows_vm/rm_connect_cert.png)

## 后续步骤

如果你在尝试连接时遇到故障，请参阅[解决远程桌面连接到基于 Windows 的 Azure 虚拟机的问题](/documentation/articles/virtual-machines-troubleshoot-remote-desktop-connections)

<!---HONumber=Mooncake_0314_2016-->