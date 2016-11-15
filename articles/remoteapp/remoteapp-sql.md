---
title: "SQL Azure 与 Azure RemoteApp | Microsoft Docs"
description: "了解如何将 SQL Azure 与 Azure RemoteApp 一起使用。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
editor: 
ms.assetid: 35f81d75-bfd7-4980-807e-00339f2cb2a4
ms.service: remoteapp
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: compute
ms.date: 08/15/2016
ms.author: elizapo
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: cfd3da08a8c8674e686ae2933db331809fb0e34d


---
# <a name="sql-azure-with-azure-remoteapp"></a>SQL Azure 与 Azure RemoteApp
> [!IMPORTANT]
> 正在中断 Azure RemoteApp。 阅读 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 了解详细信息。
> 
> 

通常，客户选择通过 Azure RemoteApp 在云中托管 Windows 应用程序时，他们还想要将数据（如 SQL Server）迁移到云中以便用于整个云部署。 这样就可以随时随地通过任何设备使用 Azure RemoteApp 访问的整个云托管解决方案。 以下列出了帮助完成此过程的链接、引用以及指南。  

## <a name="migrate-your-sql-data"></a>迁移 SQL 数据
从 [将 SQL Server 数据库迁移到 Azure SQL 数据库](../sql-database/sql-database-cloud-migrate.md)开始。 

## <a name="configure-azure-remoteapp"></a>配置 Azure RemoteApp
在 Azure RemoteApp 中托管 Windows 应用程序。 下面是高级别分步步骤：

1. 创建 [Azure RemoteApp 模板 VM](remoteapp-imageoptions.md)。 
2. 在 VM 上安装所需的应用程序。
3. 配置应用程序，连接到 SQL 数据库并确认它能正常工作。
4. Sysprep 并关闭 VM。 将此作为映像捕获，以便与 Azure 一起使用。 **注意：** 需要确保应用程序可以保留通过 sysprep 进程的数据库连接信息。 如果应用程序无法保留数据库连接信息，则可能需要与应用程序的供应商合作，检查如何指定连接字符串。
5. 选择 SQL Azure 部署所在的适当地理位置，将自定义映像导入 Azure RemoteApp 库。 
6. 使用以上模板将同一数据中心中的 RemoteApp 集合部署为 SQL Azure 部署，并发布该应用程序。 将同一数据中心中的 Azure RemoteApp 部署为 SQL Azure 部署有助于确保最快的连接速度并减少延迟。 

## <a name="app-and-sql-configuration-considerations"></a>应用和 SQL 配置注意事项：
将 Azure SQL 与 RemoteApp 一起使用时需考虑以下几点：

了解[如何配置 Azure SQL 数据库防火墙](../sql-database/sql-database-firewall-configure.md)。 文章摘要指出，“最初，防火墙会阻止对 Azure SQL 数据库服务器的所有访问。 为了开始使用你的 Azure SQL 数据库服务器，你必须转到经典门户并且指定使你可以访问 Azure SQL 数据库服务器的一个或多个服务器级防火墙规则。 使用防火墙规则可以指定允许的 Internet 上的 IP 地址范围，以及 Azure 应用程序是否可以尝试连接到你的 Azure SQL 数据库服务器。”

此外，在计算机尝试从 Internet 连接到数据库服务器时，防火墙将根据整个服务器级和（如果必需）数据库级防火墙规则检查该请求的发起 IP 地址。 “如果该请求的 IP 地址位于服务器级防火墙规则中指定的某个范围内，则将连接权限授予 Azure SQL 数据库服务器。 ” 因此，可以使用 IP 范围而不只是单个源 IP 地址。

按照 [如何：使用 Azure 门户在 SQL 数据库上配置防火墙设置](../sql-database/sql-database-configure-firewall-settings.md) 中的分步说明指定 IP 范围。 配置 SQL 防火墙规则时，请提供为 Azure RemoteApp 集合指定的子网 IP 范围。 这应允许 ARA 服务器连接到 SQL 数据库，即使它们具有动态分配的 IP 地址。

## <a name="troubleshooting"></a>故障排除
如果某 SQL 数据库托管于 Azure 或本地上，而使用连接到该数据库的 Azure RemoteApp 中托管的客户端应用程序时反应较慢，可能有几个原因造成这种问题。  

* 从设备到 Azure 的网络延迟较高。 移动到可能的最佳和最快的网络连接以获取最佳性能。 使用 [azurespeed.com](http://azurespeed.com/) 作为常规工具，测试设备与 Azure 数据中心的延迟。  
* 在 Azure RemoteApp 中托管的客户端应用压力过大。 选择一个不同的计费计划（如 Premium 计费）可提高性能。 另一个技巧是监视应用程序正在消耗的资源：在活动会话期间，执行 ctrl-alt-end 按键顺序，启动 SAS 屏幕，再选择“任务管理器”，然后观察应用的资源使用情况。
* SQL 服务器压力过大或未优化。 按照 SQL 指南进行故障排除。 




<!--HONumber=Nov16_HO2-->


