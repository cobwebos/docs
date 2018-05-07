---
title: Azure Stack 上的 SQL 宿主服务器 | Microsoft Docs
description: 如何添加 SQL 实例以通过 SQL 适配器资源提供程序进行预配
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: jeffgilb
ms.openlocfilehash: a89e5bf48c24abf72f18ee98f2dcb0eda6db35cd
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2018
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>将宿主服务器添加为 SQL 资源提供程序
可以使用 [Azure Stack](azure-stack-poc.md) 内部 VM 上的 SQL 实例，或者 Azure Stack 环境外部的实例，前提是资源提供程序能够连接到这些实例。 一般要求为：

* SQL 实例必须专用于 RP 和用户工作负荷。 不能使用其他任何用户（包括 应用服务）正在使用的 SQL 实例。
* SQL 资源提供程序 VM 未加入域，并可以仅使用 SQL 身份验证连接。
* 你必须配置具有适当的特权，以供资源提供程序的帐户。
* 资源提供程序和用户，如 Web Apps，使用用户网络，因此不需要连接到此网络上的 SQL 实例。 此要求通常意味着 SQL 实例的 IP 必须在公共网络上。
* SQL 实例和及其主机的管理是完全取决于您;资源提供程序不执行修补、 备份，则凭据旋转，等等。
* 可以使用 SKU 创建各类 SQL 功能，例如性能、Always On 等等。

可通过 Marketplace 管理功能获取许多 SQL IaaS 虚拟机映像。 请确保你始终下载最新版本的**SQL IaaS 扩展**部署使用应用商店项目的 VM 之前。 SQL 映像与 Azure 中提供的 SQL VM 相同。 对于从这些映像创建的 SQL VM，IaaS 扩展和相应的门户增强功能可提供自动修补和备份等功能。

可以使用其他选项部署 SQL VM，包括 [Azure Stack 快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates)中的模板。

> [!NOTE]
> 必须通过用户订阅创建安装在多节点 Azure Stack 上的任何宿主服务器， 而不能通过默认提供商订阅创建。 必须通过用户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器都是可计费的 VM，并且必须具有相应的 SQL 许可证。 服务管理员可以是订阅的所有者。


### <a name="required-privileges"></a>所需的特权

可以创建权限比完全 sysadmin 特权更低的新管理用户。 需要允许的特定操作为：

- 数据库：创建、更改、使用包含（仅限 Always On）、删除、备份
- 可用性组：更改、联接、添加/删除数据库
- 登录：创建、选择、更改、删除、吊销
- 选择操作：\[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn)、sys.availability_replicas (AlwaysOn)、sys.databases、\[master\].\[sys\].\[dm_os_sys_memory\]、SERVERPROPERTY、\[master\].\[sys\].\[availability_groups\] (AlwaysOn)、sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>通过连接到独立宿主 SQL 服务器来提供容量。
可以运行使用任何版本的 SQL Server 2014 或 SQL Server 2016 的独立（非 HA）SQL 服务器。 确保已准备好拥有管理特权的帐户的凭据。

若要添加已预配的独立宿主服务器，请遵循以下步骤：

1. 以服务管理员身份登录到 Azure Stack 管理门户

2. 单击“浏览”&gt;“管理资源”&gt;“SQL 宿主服务器”。

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  在“SQL 宿主服务器”边栏选项卡上，可将 SQL 服务器资源提供程序连接到充当资源提供程序后端的实际 SQL 服务器实例。

  ![宿主服务器](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. 在表单中填写 SQL Server 实例的连接详细信息。

  ![新宿主服务器](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    可以选择性地包含实例名称，如果实例未分配给默认端口 1433，则可以提供端口号。

  > [!NOTE]
  > 只要用户和管理员 Azure 资源管理器可以访问 SQL 实例，资源提供程序就能控制此实例。 必须专门将 SQL 实例分配给 RP。

4. 添加服务器时，必须将它们分配给新的或现有的 SKU，以区分服务产品。 例如，可以分配一个 SQL 企业实例来提供：
  - 数据库容量
  - 自动备份
  - 为各个部门保留高性能服务器
  - 等等。

  SKU 名称应反映属性，使用户能够适当地放置其数据库。 SKU 中的所有宿主服务器应有相同的功能。

> [!IMPORTANT]
> 特殊字符，包括空格和期间，不支持在**系列**或**层**名称时为 SQL 和 MySQL 资源提供程序创建一种的 SKU。

示例：

![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
> SKU 最长可能需要在一小时后才显示在门户中。 在完全创建 SKU 之前，用户无法创建数据库。

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>使用 SQL Always On 可用性组提供容量
配置 SQL Always On 实例需要执行附加的步骤，并至少提供三个 VM（或物理机）。

> [!NOTE]
> 对于 Always On，SQL 适配器 RP 仅支持 SQL 2016 SP1 企业版或更高版本的实例，因为它需要新的 SQL 功能，例如自动种子设定。 除了上面所列的一般要求以外，还必须满足以下要求：

* 除了 SQL Always On 计算机以外，还必须提供一个文件服务器。 有一个 [Azure Stack 快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha)可以创建此环境。 该模板还能引导你构建自己的实例。

* 必须设置 SQL 服务器。 具体而言，必须在每个 SQL Server 实例的每个可用性组上启用[自动种子设定](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)。

```
ALTER AVAILABILITY GROUP [<availability_group_name>]
    MODIFY REPLICA ON 'InstanceName'
    WITH (SEEDING_MODE = AUTOMATIC)
GO
```

在辅助实例上
```
ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
GO

```



若要添加 SQL Always On 宿主服务器，请遵循以下步骤：

1. 以服务管理员身份登录到 Azure Stack 管理门户

2. 单击“浏览”&gt;“管理资源”&gt;“SQL 宿主服务器”&gt;“+添加”。

    在“SQL 宿主服务器”边栏选项卡上，可将 SQL 服务器资源提供程序连接到充当资源提供程序后端的实际 SQL 服务器实例。


3. 在表单中填写 SQL Server 实例的连接详细信息，并确保使用 Always On 侦听器的 FQDN 或 IPv4 地址（和可选的端口号）。 提供配置的、拥有系统管理员特权的帐户的帐户信息。

4. 选中此框可启用 SQL Always On 可用性组实例的支持。

    ![宿主服务器](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. 将 SQL Always On 实例添加到 SKU。 不能在同一 SKU 中混合使用独立服务器与 Always On 实例。 在添加第一个宿主服务器时需确定其中的一项。 之后尝试混合使用类型会导致出错。


## <a name="making-sql-databases-available-to-users"></a>将 SQL 数据库提供给用户使用

创建计划和产品/服务，使用户能够使用 SQL 数据库。 将 Microsoft.SqlAdapter 服务添加到计划，并添加现有的配额或创建新配额。 如果创建配额，请可以指定允许用户使用的容量。

![创建计划和产品/服务以包含数据库](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)


## <a name="next-steps"></a>后续步骤

[添加数据库](azure-stack-sql-resource-provider-databases.md)
