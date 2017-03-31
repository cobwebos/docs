---
title: "创建运行 MySQL 的经典 Azure VM | Microsoft Docs"
description: "使用经典部署模型创建运行 Windows Server 2012 R2 和 MySQL 数据库的 Azure 虚拟机。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 98fa06d2-9b92-4d05-ac16-3f8e9fd4feaa
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 1451b06a34a13d6d9374a9f05b57c580baf1ca28
ms.lasthandoff: 03/27/2017


---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2012-r2"></a>在使用经典部署模型创建的运行 Windows Server 2012 R2 的虚拟机上安装 MySQL
[MySQL](http://www.mysql.com) 是一种常用的开源 SQL 数据库。 本教程说明如何安装并运行 MySQL 5.6.23 社区版作为运行 Windows Server 2012 R2 的虚拟机上的 MySQL 服务器。 有关如何在 Linux 上安装 MySQL 的说明，请参阅：[如何在 Azure 上安装 MySQL](../../virtual-machines-linux-mysql-install.md)。

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。

## <a name="create-a-virtual-machine-running-windows-server-2012-r2"></a>创建运行 Windows Server 2012 R2 的虚拟机
如果还没有运行 Windows Server 2012 R2 的 VM，则可以使用此[教程](tutorial.md)创建虚拟机。 

## <a name="attach-a-data-disk"></a>附加数据磁盘
创建虚拟机后，你可以选择性地附加其他数据磁盘。 对于生产型工作负荷建议执行此操作，这样做还可以避免包含操作系统的 OS 驱动器 (C:) 空间不足。

请参阅[如何将数据磁盘附加到 Windows 虚拟机](attach-disk.md)，然后按说明附加一个空磁盘。 将主机缓存设置设为“无”或“只读”。

## <a name="log-on-to-the-virtual-machine"></a>登录到虚拟机
接下来，将[登录到虚拟机](connect-logon.md)，以便安装 MySQL。

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>在虚拟机上安装和运行 MySQL Community Server
按照下列步骤操作可安装、配置和运行社区版 MySQL Server：

> [!NOTE]
> 这些步骤适用于 5.6.23.0 社区版 MySQL 和 Windows Server 2012 R2。 不同版本的 MySQL 或 Windows Server 给你带来的体验可能有所不同。
> 
> 

1. 使用远程桌面连接到该虚拟机后，从“开始”屏幕单击“Internet Explorer”。
2. 选择右上角的“工具”按钮（齿轮图标），然后单击“Internet 选项”。 依次单击“安全”选项卡、“受信任的站点”图标、“站点”按钮。 将 http://*.mysql.com 添加到受信任站点列表中。单击“关闭”，然后单击“确定”**。
3. 在 Internet Explorer 的地址栏中，键入 http://dev.mysql.com/downloads/mysql/。
4. 使用 MySQL 站点查找和下载最新版本的用于 Windows 的 MySQL 安装程序。 选择 MySQL 安装程序时，请下载包含完整文件集的版本（例如 mysql-installer-community-5.6.23.0.msi，文件大小为 282.4 MB），并保存该安装程序。
5. 当安装程序下载完成后，单击“运行”以启动安装程序。
6. 在“许可协议”页上接受许可协议，然后单击“下一步”。
7. 在“选择安装类型”页上单击所需的安装类型，然后单击“下一步”。 以下步骤假定选择了“仅服务器”安装类型。
8. 在“安装”页上，单击“执行”。 安装完成后，单击“下一步”。
9. 在“产品配置”页上，单击“下一步”。
10. 在“类型和网络”页上，指定所需的配置类型和连接选项，包括 TCP 端口（如果需要）。 选择“显示高级选项”，然后单击“下一步”。
    
    ![](media/mysql-2008r2/MySQL_TypeNetworking.png)
11. 在“帐户和角色”页上，指定强 MySQL 根密码。 根据需要添加额外的 MySQL 用户帐户，然后单击“下一步”。
    
    ![](media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
12. 在“Windows 服务”页上，指定要对默认设置进行的更改，以便根据需要将 MySQL 服务器作为 Windows 服务运行，然后单击“下一步”。
    
    ![](media/mysql-2008r2/MySQL_WindowsService.png)
13. 在“高级选项”页上，指定需要指定对日志记录选项进行的更改，然后单击“下一步”。
    
    ![](media/mysql-2008r2/MySQL_AdvOptions.png)
14. 在“应用服务器配置”页上，单击“执行”。 完成配置步骤后，单击“完成”。
15. 在“产品配置”页上，单击“下一步”。
16. 在“安装完成”页上，单击“将日志复制到剪贴板”（如果你希望在以后检查它），然后单击“完成”。
17. 在“开始”屏幕中键入“mysql”，然后单击“MySQL 5.6 命令行客户端”。
18. 输入你在步骤 11 中指定的根密码，此时会显示一个提示，你可以在其中发出命令以配置 MySQL。 有关命令和语法的详细信息，请参阅 [MySQL 参考手册](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)。
    
    ![](media/mysql-2008r2/MySQL_CommandPrompt.png)
19. 你还可以使用 C:\Program Files (x86)\MySQL\MySQL Server 5.6\my-default.ini 文件中的条目来配置服务器配置的默认设置，例如基本目录和驱动器以及数据目录和驱动器。 有关详细信息，请参阅 [5.1.2 服务器配置的默认值](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html)。

## <a name="configure-endpoints"></a>配置终结点
如果你希望 MySQL Server 服务可供 Internet 上的 MySQL 客户端计算机使用，则必须为 MySQL Server 服务所侦听的 TCP 端口配置一个终结点，并创建更多 Windows 防火墙规则。 该端口为 TCP 端口 3306，除非在“类型和网络”页上指定了其他端口（前一过程的步骤 10）。

> [!NOTE]
> 你应该仔细考虑这样做的安全隐患，因为这会使 MySQL Server 服务可供 Internet 上的所有计算机使用。 你可以通过访问控制列表 (ACL) 定义一组允许使用终结点的源 IP 地址。 有关详细信息，请参阅[如何对虚拟机设置终结点](setup-endpoints.md)。
> 
> 

若要配置 MySQL Server 服务终结点，请执行以下操作：

1. 在 Azure 经典门户中，依次单击“虚拟机”、你的 MySQL 虚拟机的名称和“终结点”。
2. 在命令栏中，单击“添加”。
3. 在“将终结点添加到虚拟机”页上，单击右箭头。
4. 如果使用的是默认 MySQL TCP 端口 3306，请单击“名称”中的“MySQL”，然后单击复选标记。
5. 如果使用的是其他 TCP 端口，请在“名称”中键入唯一名称。 在协议中选择“TCP”，在“公用端口”和“专用端口”中键入端口号，然后单击复选标记。

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>添加 Windows 防火墙规则以允许 MySQL 流量
若要添加 Windows 防火墙规则以允许来自 Internet 的 MySQL 流量，请在 MySQL Server 虚拟机上提升的 Windows PowerShell 命令提示符下运行以下命令。

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public



## <a name="test-your-remote-connection"></a>测试你的远程连接
若要测试到 MySQL Server 服务（运行在 Azure 虚拟机上）的远程连接，你必须先确定与云服务相对应的 DNS 名称，该云服务包含运行 MySQL Server 的虚拟机。

1. 在 Azure 经典门户中，依次单击“虚拟机”、你的 MySQL 服务器虚拟机的名称和“仪表板”。
2. 在虚拟机仪表板中，记下“速览”部分下面的“DNS 名称”值。 下面是一个示例：
   
   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. 从运行 MySQL 的本地计算机或 MySQL 客户端上，运行以下命令，以便以 MySQL 用户身份登录。
   
     mysql -u <yourMysqlUsername> -p -h <yourDNSname>
   
   例如，如果 MySQL 用户名为 dbadmin3，虚拟机的 DNS 名称为 testmysql.cloudapp.net，则请使用以下命令。
   
     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>后续步骤
若要了解有关运行 MySQL 的详细信息，请参阅 [MySQL 文档](http://dev.mysql.com/doc/)。


