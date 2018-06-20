---
title: Azure Stack 上的 SQL 宿主服务器 | Microsoft Docs
description: 如何添加用于通过 SQL 适配器资源提供程序设置的 SQL 实例。
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
ms.date: 06/18/2018
ms.author: jeffgilb
ms.openlocfilehash: 183d9479ae18e557b00d0867cad79600145da7bd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265222"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>为 SQL 资源提供程序添加托管服务器

你可以在中托管虚拟机 (VM) 上的 SQL 实例[Azure 堆栈](azure-stack-poc.md)，或外部你 Azure 堆栈的环境，只要 SQL 资源提供程序可以连接到的实例的 VM 上。

## <a name="overview"></a>概述

SQL 宿主服务器的常规要求如下：

* 由资源提供程序和用户工作负荷，必须使用专用的 SQL 实例。 不能使用任何其他使用者正在使用的 SQL 实例。 此限制也适用于应用程序服务。
* SQL 资源提供程序 VM 不加入域，并且仅可以使用 SQL 身份验证连接。
* 必须配置具有相应特权、可供资源提供程序使用的帐户。
* 资源提供程序和用户（例如 Web 应用）使用用户网络，因此需要连接到此网络上的 SQL 实例。 此要求通常意味着 SQL 实例的 IP 必须在公共网络上。
* SQL 实例和及其主机的管理是由您决定。 例如，资源提供程序不会应用更新、 处理备份，或处理凭据旋转。
* 你可以使用支持的 SQL 丰富功能，如性能和使用 AlwaysOn 的高可用性的不同类的 Sku。

### <a name="sql-server-virtual-machine-images"></a>SQL Server 虚拟机映像

SQL IaaS 虚拟机映像可通过应用商店管理功能。 这些映像可以在 Azure 中提供 SQL Vm 相同。

在使用市场项部署 VM 之前，请确保始终下载最新版本的 **SQL IaaS 扩展**。  IaaS 扩展和相应的门户增强功能提供了其他功能，例如自动修补和备份。

可以使用其他选项部署 SQL VM，包括 [Azure Stack 快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates)中的模板。

> [!NOTE]
> 必须通过用户订阅创建安装在多节点 Azure Stack 上的任何宿主服务器， 而不能通过默认提供商订阅创建。 必须通过用户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器是计费的 Vm，并且必须具有合适的 SQL 许可证。 服务管理员可以是订阅的所有者。

### <a name="required-privileges"></a>所需的特权

你可以创建一个管理用户具有较低权限的 SQL sysadmin。 用户只需执行以下操作的权限：

- 数据库： 创建、 更改使用包容 (为 Always On 唯一)、 删除、 备份
- 可用性组：更改、联接、添加/删除数据库
- 登录：创建、选择、更改、删除、吊销
- 选择操作：\[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn)、sys.availability_replicas (AlwaysOn)、sys.databases、\[master\].\[sys\].\[dm_os_sys_memory\]、SERVERPROPERTY、\[master\].\[sys\].\[availability_groups\] (AlwaysOn)、sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>通过连接到独立宿主 SQL 服务器来提供容量。

可以运行使用任何版本的 SQL Server 2014 或 SQL Server 2016 的独立（非 HA）SQL 服务器。 请确保你有具有 sysadmin 权限的帐户的凭据。

若要添加已设置的独立宿主服务器，请按照下列步骤：

1. 服务管理员身份登录到 Azure 堆栈运算符门户。

2. 选择**浏览** &gt; **管理资源** &gt; **SQL 宿主服务器**。

   ![SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   下**SQL 托管服务器**，你可以将 SQL 资源提供程序连接到作为资源提供程序的后端 SQL Server 实例。

   ![SQL 适配器仪表板](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. 在表单中填写 SQL Server 实例的连接详细信息。

   ![添加 SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    （可选） 可以包含实例名称，并指定端口号，如果此实例不分配给默认端口 1433年。

   > [!NOTE]
   > 只要用户和管理员 Azure 资源管理器可以访问 SQL 实例，资源提供程序就能控制此实例。 SQL 实例__必须__分配以独占方式与资源提供程序。

4. 添加服务器时，必须将它们分配给新的或现有的 SKU，以区分服务产品。 例如，你可以提供的 SQL Enterprise 实例：
  
   - 数据库容量
   - 自动备份
   - 为各个部门保留高性能服务器

   在 SKU 中的所有宿主服务器应具有相同的功能。 **名称**应反映 SKU 的属性，以便用户可以将其数据库部署到适当的 SKU。

   > [!IMPORTANT]
   > 为 SQL 和 MySQL 资源提供程序创建 SKU 时，**系列**或**层级**名称中不支持使用特殊字符（包括空格和句点）。

   例如：

   ![创建 SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

   >[!NOTE]
   > SKU 最长可能需要在一小时后才显示在门户中。 在完全创建 SKU 之前，用户无法创建数据库。

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>提供使用 SQL Alwayson 可用性组的高可用性

配置 SQL Always On 实例需要额外的步骤和要求至少三个虚拟机 （或物理计算机。）本文假定你已深入了解的 Always On 可用性组。 有关详细信息，请参阅：

* [简介 SQL Server Always On 可用性组在 Azure 虚拟机](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On 可用性组 (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> SQL 适配器资源提供程序_仅_支持 SQL 2016 SP1 Enterprise 或更高版本的 Always On 实例。 此适配器配置需要新的 SQL 功能，如自动种子设定。

除了前面列出的要求必须使[自动种子设定](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)上每个可用性组的每个 SQL Server 实例。

若要启用自动种子设定，所有实例上，编辑，然后运行以下 SQL 命令为每个实例：

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON 'InstanceName'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

在辅助实例，编辑，然后运行以下 SQL 命令为每个实例:

  ```
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>若要添加 SQL Always On 宿主服务器

1. 登录到 Azure 堆栈管理门户为服务管理员。

2. 选择**浏览** &gt; **管理资源** &gt; **SQL 宿主服务器** &gt; **+ 添加**.

   下**SQL 托管服务器**你可以将 SQL Server 资源提供程序连接到的 SQL Server 作为资源提供程序的后端的实际实例。

3. 填写表单与您的 SQL Server 实例的连接详细信息。 请确保你使用的 FQDN 地址始终在侦听器 （和可选端口号。）提供配置具有 sysadmin 权限的帐户的信息。

4. 检查相应的 Always On 可用性组框以启用对 SQL Alwayson 可用性组实例的支持。

   ![启用 Alwayson](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. 将 SQL Always On 实例添加到 SKU。

   > [!IMPORTANT]
   > 不能在相同 SKU 的 Always On 实例，并用混合独立服务器。 尝试在添加第一个托管服务器后混合类型会导致错误。

## <a name="make-the-sql-databases-available-to-users"></a>向用户提供的 SQL 数据库

创建计划和产品/服务，使用户能够使用 SQL 数据库。 添加**Microsoft.SqlAdapter**服务到计划，并添加默认配额，或创建新的配额。

![创建计划和产品/服务以包含数据库](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>后续步骤

[添加数据库](azure-stack-sql-resource-provider-databases.md)
