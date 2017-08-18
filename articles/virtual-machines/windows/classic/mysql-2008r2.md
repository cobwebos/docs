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
ms.translationtype: HT
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 11850e5ce20efae88a7af9c1d2e4761ed2b70cd7
ms.contentlocale: zh-cn
ms.lasthandoff: 08/11/2017

---
# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2016"></a>在使用经典部署模型创建的运行 Windows Server 2016 的虚拟机上安装 MySQL
[MySQL](https://www.mysql.com) 是一种常用的开源 SQL 数据库。 本教程说明如何安装并运行 **MySQL 5.7.18 社区版**作为运行 **Windows Server 2016** 的虚拟机上的 MySQL 服务器。 其他版本的 MySQL 或 Windows Server 所带来的体验可能略有不同。

有关如何在 Linux 上安装 MySQL 的说明，请参阅：[如何在 Azure 上安装 MySQL](../../linux/mysql-install.md)。

> [!IMPORTANT]
> Azure 提供两个不同的部署模型用于创建和处理资源：[Resource Manager 和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用 Resource Manager 模型。

## <a name="create-a-virtual-machine-running-windows-server-2016"></a>创建运行 Windows Server 2016 的虚拟机
如果还没有一台运行 Windows Server 2016 的 VM，可使用本[教程](./tutorial.md)创建虚拟机。

## <a name="attach-a-data-disk"></a>附加数据磁盘
创建虚拟机后，可有选择地附加数据磁盘。 添加数据磁盘是生产工作负荷的推荐操作，这样做还可以避免包含操作系统的 OS 驱动器 (C:) 空间不足。

请参阅[如何将数据磁盘附加到 Windows 虚拟机](../attach-managed-disk-portal.md)，然后按说明附加一个空磁盘。 将主机缓存设置设为“无”或“只读”。

## <a name="log-on-to-the-virtual-machine"></a>登录到虚拟机
接下来，将[登录到虚拟机](./connect-logon.md)，以便安装 MySQL。

## <a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>在虚拟机上安装和运行 MySQL Community Server
按照下列步骤操作可安装、配置和运行社区版 MySQL Server：

> [!NOTE]
> 使用 Internet Explorer 下载项时，可以将 IE“增强的安全配置”设置为“关闭”，来简化下载过程。 从“开始”菜单中，依次单击“开始”/“管理工具”/“服务器管理器”/“本地服务器”，然后单击 IE“增强的安全配置”，并将该配置设置为“关闭”）。
>
>

1. 使用远程桌面连接到该虚拟机后，从“开始”屏幕单击“Internet Explorer”。
2. 选择右上角的“工具”按钮（齿轮图标），并单击“Internet 选项”。 依次单击“安全”选项卡、“受信任的站点”图标、“站点”按钮。 将 http://*.mysql.com 添加到受信任站点列表中。 单击“关闭”，并单击“确定”。
3. 在 Internet Explorer 的地址栏中，键入 https://dev.mysql.com/downloads/mysql/。
4. 使用 MySQL 站点查找和下载最新版本的用于 Windows 的 MySQL 安装程序。 选择 MySQL 安装程序时，请下载包含完整文件集的版本（例如 mysql-installer-community-5.7.18.0.msi，文件大小为 352.8 MB），并保存该安装程序。
5. 当安装程序下载完成后，单击“运行”以启动安装程序。
6. 在“许可协议”页上接受许可协议，并单击“下一步”。
7. 在“选择安装类型”页上单击所需的安装类型，并单击“下一步”。 以下步骤假定选择了“仅服务器”安装类型。
8. 如果显示“检查要求”页，请单击“执行”，以便让安装程序安装缺少的任何组件。 遵循显示的任何说明，例如 C++ Redistributable 运行时。
9. 在“安装”页上，单击“执行”。 安装完成后，单击“下一步”。

10. 在“产品配置”页上，单击“下一步”。

11. 在“类型和网络”页上，指定所需的配置类型和连接选项，包括 TCP 端口（如果需要）。 选择“显示高级选项”，并单击“下一步”。
    ![](./media/mysql-2008r2/MySQL_TypeNetworking.png)

12. 在“帐户和角色”页上，指定强 MySQL 根密码。 根据需要添加额外的 MySQL 用户帐户，并单击“下一步”。

    ![](./media/mysql-2008r2/MySQL_AccountsRoles_Filled.png)
13. 在“Windows 服务”页上，指定要对默认设置进行的更改，以便根据需要将 MySQL 服务器作为 Windows 服务运行，然后单击“下一步”。

    ![](./media/mysql-2008r2/MySQL_WindowsService.png)
14. “插件和扩展”页上的选择是可选项。 单击“下一步”以继续。
15. 在“高级选项”页上，指定需要指定对日志记录选项进行的更改，并单击“下一步”。

    ![](./media/mysql-2008r2/MySQL_AdvOptions.png)
16. 在“应用服务器配置”页上，单击“执行”。 完成配置步骤后，单击“完成”。
17. 在“产品配置”页上，单击“下一步”。
18. 在“安装完成”页上，单击“将日志复制到剪贴板”（如果希望在以后检查它），然后单击“完成”。
19. 在“开始”屏幕中键入 **mysql**，并单击“MySQL 5.7 命令行客户端”。
20. 输入在步骤 12 中指定的根密码，此时会显示一个提示，可在其中发出命令以配置 MySQL。 有关命令和语法的详细信息，请参阅 [MySQL 参考手册](https://dev.mysql.com/doc/refman/5.7/en/server-configuration.html)。

    ![](./media/mysql-2008r2/MySQL_CommandPrompt.png)
21. 还可以对服务器配置的默认设置进行配置，例如基本目录和数据目录以及驱动器。 有关详细信息，请参阅 [6.1.2 服务器配置的默认值](https://dev.mysql.com/doc/refman/5.7/en/server-configuration-defaults.html)。

## <a name="configure-endpoints"></a>配置终结点

为了使 MySQL 服务可供 Internet 上的客户端计算机使用，必须为 TCP 端口配置终结点，并创建 Windows 防火墙规则。 MySQL 服务器服务在其上侦听 MySQL 客户端的端口的默认值为 3306。 只要端口与“类型与网络”页上提供的值（前面过程的步骤 11）一致，便可指定其他端口。

> [!NOTE]
> 对于生产用途，请考虑使 MySQL 服务器服务可供 Internet 上的所有计算机使用时的安全隐患。 可以通过访问控制列表 (ACL) 定义一组允许使用终结点的源 IP 地址。 有关详细信息，请参阅[如何对虚拟机设置终结点](setup-endpoints.md)。
>
>

若要配置 MySQL Server 服务终结点，请执行以下操作：

1. 在 Azure 门户中，单击“虚拟机(经典)”、单击 MySQL 虚拟机的名称，并单击“终结点”。
2. 在命令栏中，单击“添加”。
3. 在“添加终结点”页上，为“名称”键入唯一名称。
4. 选择“TCP”作为协议。
5. 在“公用端口”和“专用端口”中键入端口号（例如 **3306**），并单击“确定”。

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>添加 Windows 防火墙规则以允许 MySQL 流量
若要添加 Windows 防火墙规则以允许来自 Internet 的 MySQL 流量，请在 MySQL 服务器虚拟机上_提升的 Windows PowerShell 命令提示符_下运行以下命令。

    New-NetFirewallRule -DisplayName "MySQL57" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public

## <a name="test-your-remote-connection"></a>测试远程连接
若要测试运行 MySQL 服务器服务的 Azure VM 的远程连接，必须提供包含 VN 的云服务的 DNS 名称。

1. 在 Azure 门户中，单击“虚拟机(经典)”、单击 MySQL 服务器虚拟机的名称，并单击“概述”。
2. 从虚拟机仪表板中，记下“DNS 名称”值。 下面是一个示例：

   ![](media/mysql-2008r2/MySQL_DNSName.png)
3. 从运行 MySQL 的本地计算机或 MySQL 客户端上，运行以下命令，以便以 MySQL 用户身份登录。

     mysql -u <yourMysqlUsername> -p -h <yourDNSname>

   例如，使用 MySQL 用户名 _dbadmin3_ 和虚拟机 DNS 名称 _testmysql.cloudapp.net_，可以使用以下命令来启动 MySQL：

     mysql -u dbadmin3 -p -h testmysql.cloudapp.net

## <a name="next-steps"></a>后续步骤
若要了解有关运行 MySQL 的详细信息，请参阅 [MySQL 文档](http://dev.mysql.com/doc/)。

