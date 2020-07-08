---
title: 使用 SSMS 配置点到站点连接
titleSuffix: Azure SQL Managed Instance
description: 通过从本地客户端计算机使用点到站点连接，使用 SQL Server Management Studio （SSMS）连接到 Azure SQL 托管实例。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova, jovanpop
ms.date: 03/13/2019
ms.openlocfilehash: 7b9c9fc6259656af77bf1ba1b95ccf190cbd85da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708615"
---
# <a name="quickstart-configure-a-point-to-site-connection-to-azure-sql-managed-instance-from-on-premises"></a>快速入门：配置从本地到 Azure SQL 托管实例的点到站点连接
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

本快速入门演示了如何通过点到站点连接从本地客户端计算机使用[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) （SSMS）连接到 Azure SQL 托管实例。 有关点到站点连接的信息，请参阅[关于点到站点 VPN](../../vpn-gateway/point-to-site-about.md)。

## <a name="prerequisites"></a>先决条件

本快速入门：

- 使用在 "[创建托管实例](instance-create-quickstart.md)" 中创建的资源作为起始点。
- 需要在本地客户端计算机上安装 PowerShell 5.1 和 Azure PowerShell 1.4.0 或更高版本。 如有必要，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps#install-the-azure-powershell-module)的说明。
- 需要在本地客户端计算机上[SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)的最新版本。

## <a name="attach-a-vpn-gateway-to-a-virtual-network"></a>将 VPN 网关附加到虚拟网络

1. 在本地客户端计算机上打开 PowerShell。

2. 复制此 PowerShell 脚本。 此脚本将 VPN 网关附加到在[创建托管实例](instance-create-quickstart.md)快速入门中创建的 SQL 托管实例虚拟网络。 此脚本使用 Azure PowerShell Az 模块，并对基于 Windows 或 Linux 的主机执行以下操作：

   - 在客户端计算机上创建和安装证书
   - 计算未来的 VPN 网关子网 IP 范围
   - 创建网关子网
   - 部署 Azure 资源管理器模板，该模板将 VPN 网关附加到 VPN 子网

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

3. 将该脚本粘贴到 PowerShell 窗口并提供所需的参数。 `<subscriptionId>`、和的值 `<resourceGroup>` `<virtualNetworkName>` 应与用于[创建托管实例](instance-create-quickstart.md)快速入门的值匹配。 `<certificateNamePrefix>` 的值可以是所选字符串。

4. 执行 PowerShell 脚本。

> [!IMPORTANT]
> 完成 PowerShell 脚本之前，请不要继续操作。

## <a name="create-a-vpn-connection"></a>创建 VPN 连接

1. 登录 [Azure 门户](https://portal.azure.com/)。
2. 打开在其中创建了虚拟网络网关的资源组，然后打开虚拟网络网关资源。
3. 选择“点到站点配置”，然后选择“下载 VPN 客户端” 。

    ![下载 VPN 客户端](./media/point-to-site-p2s-configure/download-vpn-client.png)  
4. 在本地客户端计算机上，从 zip 文件中提取文件，然后打开含有提取文件的文件夹。
5. 打开 " **WindowsAmd64** " 文件夹并打开**VpnClientSetupAmd64.exe**的文件。
6. 如果收到“Windows 已保护你的电脑”消息，请单击“更多信息”，然后单击“仍然运行”。  

    ![安装 VPN 客户端](./media/point-to-site-p2s-configure/vpn-client-defender.png)
7. 在“用户帐户控制”对话框中，请单击“是”以继续。
8. 在引用虚拟网络的对话框中，选择 **"是"** 以安装虚拟网络的 VPN 客户端。

## <a name="connect-to-the-vpn-connection"></a>连接到 VPN 连接

1. 在本地客户端计算机上的**网络 & Internet**中转到**VPN** ，然后选择 SQL 托管实例虚拟网络以建立与此 VNet 的连接。 在下图中，VNet 命名为 **MyNewVNet**。

    ![VPN 连接](./media/point-to-site-p2s-configure/vpn-connection.png)  
2. 选择“连接”。
3. 在对话框中，选择“连接”。

    ![VPN 连接](./media/point-to-site-p2s-configure/vpn-connection2.png)  
4. 如果系统提示您连接管理器需要提升的权限来更新路由表，请选择 "**继续**"。
5. 在“用户帐户控制”对话框中选择“是”以继续。

   已与 SQL 托管实例 VNet 建立了 VPN 连接。

    ![VPN 连接](./media/point-to-site-p2s-configure/vpn-connection-succeeded.png)  

## <a name="connect-with-ssms"></a>与 SSMS 连接

1. 在本地客户端计算机上，打开 SQL Server Management Studio。
2. 在 "**连接到服务器**" 对话框中，在 "**服务器名称**" 框中为托管实例输入完全限定的**主机名**。
3. 选择“SQL Server 身份验证”，提供用户名和密码，然后选择“连接”。 

    ![SSMS 连接](./media/point-to-site-p2s-configure/ssms-connect.png)  

连接后，可在数据库节点中查看系统和用户数据库。 还可在安全性、服务器对象、复制、管理、SQL Server 代理和 XEvent Profiler 节点中查看各种对象。

## <a name="next-steps"></a>后续步骤

- 有关介绍如何从 Azure 虚拟机进行连接的快速入门，请参阅[配置点到站点连接](point-to-site-p2s-configure.md)。
- 有关应用程序的连接选项概述，请参阅[将应用程序连接到 SQL 托管实例](connect-application-instance.md)。
- 若要将现有 SQL Server 数据库从本地还原到托管实例，可以使用[Azure 数据库迁移服务进行迁移](../../dms/tutorial-sql-server-to-managed-instance.md)，或使用[t-sql restore 命令](restore-sample-database-quickstart.md)从数据库备份文件还原。
