---
title: 使用 SSMS 配置点到站点连接
titleSuffix: Azure SQL Managed Instance
description: 使用 SQL Server Management Studio (SSMS)，通过点到站点连接从本地客户端计算机连接到 Azure SQL 托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 1bcaaed394d8e802a9660e2fdf0e37994ee795a7
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2020
ms.locfileid: "91617683"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>快速入门：配置从本地到 Azure SQL 托管实例的点到站点连接
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本快速入门演示如何使用 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS)，通过点到站点连接从本地客户端计算机连接到 Azure SQL 托管实例。 有关点到站点连接的信息，请参阅[关于点到站点 VPN](../../vpn-gateway/point-to-site-about.md)

## <a name="prerequisites"></a>先决条件

本快速入门：

- 从[创建托管实例](instance-create-quickstart.md)中创建的资源着手。
- 本地客户端计算机需要 PowerShell 5.1 和 Azure PowerShell 1.4.0 或更高版本。 必要时，请根据说明来[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module)。
- 本地客户端计算机上需要最新版本的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)。

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>将 VPN 网关连接到虚拟网络

1. 在本地客户端计算机上打开 PowerShell。

2. 复制此 PowerShell 脚本。 此脚本将 VPN 网关附加到在[创建托管实例](instance-create-quickstart.md)快速入门中创建的 SQL 托管实例虚拟网络。 此脚本使用 Azure PowerShell Az 模块，并对基于 Windows 或 Linux 的主机执行以下操作：

   - 在客户端计算机上创建并安装证书
   - 计算未来的 VPN 网关子网 IP 范围
   - 创建网关子网
   - 部署可将 VPN 网关附加到 VPN 子网的 Azure 资源管理器模板

     ```powershell
     $scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/attach-vpn-gateway'

     $parameters = @{
       subscriptionId = '<subscriptionId>'
       resourceGroupName = '<resourceGroupName>'
       virtualNetworkName = '<virtualNetworkName>'
       certificateNamePrefix  = '<certificateNamePrefix>'
       }

     Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/attachVPNGateway.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters, $scriptUrlBase
     ```

3. 在 PowerShell 窗口中粘贴脚本，并提供所需参数。 `<subscriptionId>`、`<resourceGroup>`、`<virtualNetworkName>` 的值应匹配用于[创建托管实例](instance-create-quickstart.md)快速入门的值。 `<certificateNamePrefix>` 的值可以是所选字符串。

4. 执行 PowerShell 脚本。

> [!IMPORTANT]
> 在 PowerShell 脚本完成前，请勿继续操作。

## <a name="create-a-vpn-connection"></a>创建 VPN 连接

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 打开在其中创建了虚拟网关的资源组，然后打开虚拟网关资源。
3. 选择“点到站点配置”，然后选择“下载 VPN 客户端”。

    ![下载 VPN 客户端](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. 在本地客户端计算机上，从 zip 文件中提取文件，然后打开包含已提取文件的文件夹。
5. 打开“WindowsAmd64”文件夹，然后打开“VpnClientSetupAmd64.exe”文件 。
6. 如果收到“Windows 已保护你的电脑”消息，请单击“更多信息”，然后单击“仍然运行”。  

    ![安装 VPN 客户端](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. 在“用户帐户控制”对话框中单击“是”，继续下一步。
8. 在引用虚拟网络的对话框中选择“是”，为虚拟网络安装 VPN 客户端。

## <a name="connect-to-the-vpn-connection"></a>连接到 VPN 连接

1. 在本地客户端计算机上的“网络和 Internet”中转到“VPN”，选择“SQL 托管实例”虚拟网络，以便建立到此 VNet 的连接。  在下图中，VNet 名为 **MyNewVNet**。

    ![VPN 连接](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. 选择“连接” 。
3. 在对话框中，选择“连接”。

    ![VPN 连接](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. 当系统提示你连接管理器需要提升的特权才能更新路由表时，请选择“继续”。
5. 在“用户帐户控制”对话框中选择“是”，继续下一步。

   已建立与 SQL 托管实例 VNet 的 VPN 连接。

    ![VPN 连接](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>使用 SSMS 进行连接

1. 在本地客户端计算机上，打开 SQL Server Management Studio。
2. 在“连接到服务器”对话框的“服务器名称”框中，输入托管实例的完全限定主机名。  
3. 选择“SQL Server 身份验证”，提供用户名和密码，然后选择“连接”。 

    ![SSMS 连接](./media/point-to-site-p2s-configure/ssms-connect.png)  

连接后，可以在“数据库”节点中查看系统和用户数据库。 还可以在“安全性”、“服务器对象”、“复制”、“管理”、“SQL Server 代理”和“XEvent 探查器”节点中查看各种对象。

## <a name="next-steps"></a>后续步骤

- 如需介绍如何从 Azure 虚拟机进行连接的快速入门，请参阅[配置点到站点连接](point-to-site-p2s-configure.md)。
- 有关应用程序的连接选项概述，请参阅[将应用程序连接到 SQL 托管实例](connect-application-instance.md)。
- 若要将现有 SQL Server 数据库从本地还原到托管实例，可以使用[用于迁移的 Azure 数据库迁移服务](../../dms/tutorial-sql-server-to-managed-instance.md)，或使用 [T-SQL RESTORE 命令](restore-sample-database-quickstart.md)从数据库备份文件还原。
