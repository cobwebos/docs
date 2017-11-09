---
title: "SQL 宿主服务器上 Azure 堆栈 |Microsoft 文档"
description: "如何添加用于通过 SQL 适配器资源提供程序设置的 SQL 实例"
services: azure-stack
documentationCenter: 
author: JeffGoldner
manager: bradleyb
editor: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: JeffGo
ms.openlocfilehash: 58c83b74041e0e2e82729f569c53aca59f3aed43
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="add-hosting-servers-for-use-by-the-sql-adapter"></a>添加 SQL 适配器使用的宿主服务器

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

你可以使用 SQL 实例内的虚拟机上你[Azure 堆栈](azure-stack-poc.md)，或者你提供的资源提供程序的 Azure 堆栈环境外的实例可以连接到它。 常规的要求如下：

* 由 RP 和用户工作负荷，必须使用专用的 SQL 实例。 不能使用任何其他使用者，包括应用程序服务正在使用的 SQL 实例。
* RP 适配器未加入域，并可以仅使用 SQL 身份验证连接。
* 你必须配置具有由 RP 使用的适当权限的帐户。
* 网络流量从 RP to SQL 使用端口 1433，且无法更改。
* RP 和如 Web Apps 的用户使用用户网络，因此不需要连接到此网络上的 SQL 实例。 此要求通常意味着您的 SQL 实例的 IP 必须在公用网络上。
* SQL 实例和及其主机的管理是完全取决于您;RP 不执行修补、 备份，则凭据旋转，等等。
* Sku 可以用于创建不同的 SQL 丰富功能，如性能，类始终，等等。



有多种 SQL IaaS 虚拟机映像都是可通过应用商店管理功能。 请确保你在部署使用应用商店项目的 VM 之前始终下载最新版本的 SQL IaaS 扩展。 SQL 映像是在 Azure 中提供 SQL Vm 相同。 从这些映像，IaaS 扩展创建 SQL 虚拟机，并相应门户增强功能提供功能，例如自动修补和备份功能。

有用于部署 SQL 虚拟机，包括在模板的其他选项[Azure 堆栈快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates)。

> [!NOTE]
> 必须从用户订阅创建多节点 Azure 堆栈上安装任何宿主服务器。 也不能从默认提供程序订阅中创建。 从用户门户或具有合适的登录名的 PowerShell 会话，必须创建它们。 所有宿主服务器是应计费 Vm，并且必须具有合适的 SQL 许可证。 服务管理员_可以_是该订阅的所有者。


### <a name="required-privileges"></a>必需的权限

使用小于完整 sysadmin 特权，可以创建新的管理用户。 需要允许特定操作如下：

- 数据库： 创建、 使用包容 （始终，在仅限），Alter 放，备份
- 可用性组： Alter、 将添加/删除数据库的联接
- 登录名： 创建、 选择、 更改、 删除、 撤消
- 选择操作： \[master\]。\[sys\]。\[availability_group_listeners\] (AlwaysOn) sys.availability_replicas (AlwaysOn)、 sys.databases， \[master\]。\[sys\]。\[dm_os_sys_memory\]，SERVERPROPERTY， \[master\]。\[sys\]。\[availability_groups\] (AlwaysOn) sys.master_files



## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>通过连接到承载 SQL server 的独立提供容量
你可以使用独立 (非-HA) 使用任何版本的 SQL Server 2014 或 SQL Server 2016 的 SQL server。 请确保你有具有系统管理员权限的帐户的凭据。

若要添加独立承载已设置的服务器，请按照下列步骤：

1. 服务管理员身份登录到 Azure 堆栈管理员门户

2. 单击**浏览** &gt; **管理资源** &gt; **SQL 宿主服务器**。

  ![](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

  **SQL 托管服务器**边栏选项卡是其中你可以连接到的 SQL Server 作为资源提供程序的后端的实际实例的 SQL Server 资源提供程序。

  ![宿主服务器](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. 使用 SQL Server 实例的连接详细信息来填充窗体。

  ![新的宿主服务器](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

  > [!NOTE]
  > 只要可以按用户和管理 Azure 资源管理器访问 SQL 实例，它可以放置在控制之下的资源提供程序。 SQL 实例__必须__分配以独占方式给 RP。

4. 添加服务器时，你必须为他们分配一种新的或现有的 SKU 来区分服务产品。 例如，你可以 SQL Enterprise 实例提供：
  - 数据库容量
  - 自动备份
  - 预留各部门的高性能服务器
  - 等等。

  SKU 名称应反映的属性，以便用户可以适当地将其数据库。 在 SKU 中的所有宿主服务器应具有相同的功能。

    示例：

    ![SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

>[!NOTE]
Sku 可以花费一个小时的时间无法在门户中显示。 只有完全创建 SKU，才能创建数据库。

## <a name="provide-capacity-using-sql-always-on-availability-groups"></a>提供使用 SQL Alwayson 可用性组的容量
配置 SQL Always On 实例需要额外的步骤，并且涉及在至少三个虚拟机 （或物理计算机）。

> [!NOTE]
> SQL 适配器 RP_仅_为 Always On 支持 SQL 2016 SP1 Enterprise 或更高版本实例，因为它需要新的 SQL 功能，如自动种子设定。 除了前面常见列出的要求：

* 你必须提供除了 SQL Always On 计算机的文件服务器。 没有[Azure 堆栈快速入门模板](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-ha)，可为你创建此环境。 它还可以充当以构建您自己的实例的指南。

* 你必须将 SQL server 设置。 具体而言，必须启用[自动种子设定](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)上每个可用性组的每个 SQL Server 实例。

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



若要添加 SQL Always On 宿主服务器，请按照下列步骤：

1. 以服务管理员身份登录到 Azure 堆栈管理员门户

2. 单击**浏览** &gt; **管理资源** &gt; **SQL 宿主服务器** &gt; **+ 添加**.

    **SQL 托管服务器**边栏选项卡是其中你可以连接到的 SQL Server 作为资源提供程序的后端的实际实例的 SQL Server 资源提供程序。


3. 使用 SQL Server 实例，确保使用始终上侦听器的 FQDN 或 IPv4 地址的连接详细信息来填充窗体。 提供配置与系统管理员权限的帐户的帐户信息。

4. 选中此框以启用对 SQL Alwayson 可用性组实例的支持。

    ![宿主服务器](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. 将 SQL Always On 实例添加到一种的 SKU。 不能在相同 SKU 的 Always On 实例，并用混合独立服务器。 添加的第一个托管服务器时，已将确定的。 尝试之后混合类型将导致错误。


## <a name="making-sql-databases-available-to-users"></a>使 SQL 数据库可供用户

创建计划，并提供可使 SQL 数据库可供用户使用。 将 Microsoft.SqlAdapter 服务添加到计划，并添加了现有的配额，或创建一个新。 如果你创建的配额，则可以指定的容量以允许用户。

![创建计划，并提供要包括数据库](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="maintenance-of-the-sql-adapter-rp"></a>维护的 SQL 适配器 RP

维护的 SQL 实例未涵盖，但密码旋转信息除外。 你负责修补和备份/恢复的数据库服务器与 SQL 适配器一起使用。

### <a name="patching-and-updating"></a>修补和更新
 作为 Azure 堆栈的一部分不得到响应 SQL 适配器，因为它是一个外接程序组件。 向 SQL 适配器根据需要，Microsoft 将提供更新。 在实例化 SQL 适配器_用户_默认提供程序订阅下的虚拟机。 因此，有必要提供 Windows 修补程序、 防病毒签名，等等。Windows 更新形式的修补程序更新周期的一部分可用于将更新应用到 Windows 虚拟机提供的包。 当发布已更新的适配器后时，提供了一个脚本来应用此更新。 此脚本创建一个新的 RP VM，并将你已有任何状态迁移。

 ### <a name="backuprestoredisaster-recovery"></a>备份/还原/灾难恢复
 SQL 适配器不会备份 Azure 堆栈业务连续性灾难恢复过程，因为它是一个外接程序组件。 将提供脚本，以便：
- （存储在一个 Azure 堆栈的存储帐户） 的必要状态信息的备份
- 事件的完整堆栈恢复有必要，请还原 RP。
（如果有必要） 必须首先恢复数据库服务器，然后还原 RP。

### <a name="updating-sql-credentials"></a>更新 SQL 凭据

你负责创建和维护 SQL server 上的系统管理员帐户。 RP 需要具有这些权限来管理代表用户的数据库的帐户-它不需要对这些数据库中的数据的访问。 如果你需要更新你的 SQL server 上的 sa 密码，可以使用 RP 的管理器界面的更新功能将由 RP 使用的存储的密码。 这些密码存储在密钥保管库在 Azure 堆栈实例上。

若要修改设置，请单击**浏览** &gt; **管理资源** &gt; **SQL 托管服务器** &gt; **SQL 登录名**和选择登录名。 SQL 实例上必须首先进行更改 （和任何副本，如有必要）。 在**设置**面板中，单击**密码**。

![更新管理员密码](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)


## <a name="next-steps"></a>后续步骤

[添加数据库](azure-stack-sql-resource-provider-databases.md)
