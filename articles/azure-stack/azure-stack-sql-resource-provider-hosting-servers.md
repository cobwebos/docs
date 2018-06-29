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
ms.date: 06/27/2018
ms.author: jeffgilb
ms.openlocfilehash: af820f90c5d8822dbdaa768b16360d534fd47828
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37060036"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>为 SQL 资源提供程序添加托管服务器

你可以在中托管虚拟机 (VM) 上的 SQL 实例[Azure 堆栈](azure-stack-poc.md)，或外部你 Azure 堆栈的环境，只要 SQL 资源提供程序可以连接到的实例的 VM 上。

## <a name="overview"></a>概述

添加 SQL 宿主服务器之前，请查看以下必填字段和常规要求。

**强制性要求**

* 启用 SQL Server 实例上的 SQL 身份验证。 因为 SQL 资源提供程序 VM 不加入域，它可以仅连接到使用 SQL 身份验证的宿主服务器。
* 将 SQL 实例的 IP 地址配置为公开。 资源提供程序和用户，如 Web Apps，通过用户网络通信，因此不需要连接到此网络上的 SQL 实例。

**一般要求**

* 将使用的 SQL 实例专用由资源提供程序和用户工作负荷。 不能使用任何其他使用者正在使用的 SQL 实例。 此限制也适用于应用程序服务。
* 资源提供程序，使用适当的权限级别配置的帐户。
* 你负责管理 SQL 实例和其主机。  例如，资源提供程序不会应用更新、 处理备份，或处理凭据旋转。

### <a name="sql-server-virtual-machine-images"></a>SQL Server 虚拟机映像

SQL IaaS 虚拟机映像可通过应用商店管理功能。 这些映像可以在 Azure 中提供 SQL Vm 相同。

请确保你始终下载最新版本的**SQL IaaS 扩展**部署使用应用商店项目的 SQL VM 之前。 IaaS 扩展和相应的门户增强功能提供了其他功能，例如自动修补和备份。 有关此扩展的详细信息，请参阅[自动执行管理任务在 Azure 虚拟机上使用 SQL Server 代理扩展](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)。

可以使用其他选项部署 SQL VM，包括 [Azure Stack 快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates)中的模板。

> [!NOTE]
> 必须通过用户订阅创建安装在多节点 Azure Stack 上的任何宿主服务器， 而不能通过默认提供商订阅创建。 必须通过用户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器是计费的 Vm，并且必须具有合适的 SQL 许可证。 服务管理员可以是订阅的所有者。

### <a name="required-privileges"></a>所需的特权

以低于 SQL sysadmin 特权，可以创建一个管理用户。 用户只需执行以下操作的权限：

* 数据库： 创建、 更改使用包容 (为 Always On 唯一)、 删除、 备份
* 可用性组：更改、联接、添加/删除数据库
* 登录：创建、选择、更改、删除、吊销
* 选择操作：\[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn)、sys.availability_replicas (AlwaysOn)、sys.databases、\[master\].\[sys\].\[dm_os_sys_memory\]、SERVERPROPERTY、\[master\].\[sys\].\[availability_groups\] (AlwaysOn)、sys.master_files

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>通过连接到独立宿主 SQL 服务器来提供容量。

可以运行使用任何版本的 SQL Server 2014 或 SQL Server 2016 的独立（非 HA）SQL 服务器。 请确保你有具有 sysadmin 权限的帐户的凭据。

若要添加已设置的独立宿主服务器，请按照下列步骤：

1. 服务管理员身份登录到 Azure 堆栈运算符门户。

2. 选择**浏览** &gt; **管理资源** &gt; **SQL 宿主服务器**。

   ![SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   下**SQL 托管服务器**，你可以将 SQL 资源提供程序连接到作为资源提供程序的后端 SQL Server 实例。

   ![SQL 适配器仪表板](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. 上**添加 SQL 宿主服务器**，提供您的 SQL Server 实例的连接详细信息。

   ![添加 SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    （可选） 提供实例名称，并指定端口号，如果此实例不分配给默认端口 1433年。

   > [!NOTE]
   > 只要用户和管理员 Azure 资源管理器可以访问 SQL 实例，资源提供程序就能控制此实例。 SQL 实例__必须__分配以独占方式与资源提供程序。

4. 添加服务器时，你必须将它们分配给现有 SKU 或创建新的 SKU。 下**添加托管服务器**，选择**Sku**。

   * 若要使用现有的 SKU，选择可用的 SKU，然后选择**创建**。
   * 若要创建一种 SKU，选择 **+ 创建新的 SKU**。 在**创建 SKU**，输入所需的信息，然后选择**确定**。

     > [!IMPORTANT]
     > 特殊字符，包括空格和期间，不支持在**名称**字段。 使用以下屏幕截图中的示例输入值**系列**，**层**，和**版本**字段。

     ![创建一种的 SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

      SKU 最长可能需要在一小时后才显示在门户中。 用户无法创建数据库，直到完全创建 SKU。

### <a name="sku-notes"></a>SKU 说明

Sku 可用于区分服务产品。 例如，你可以具有以下特征的 SQL Enterprise 实例：
  
* 高容量
* 高性能
* 高可用性

你可以创建限制对需要高性能数据库的特定组的访问的前面的示例中的 SKU。

>[!TIP]
>使用可反映的 SKU 名描述中的 SKU，例如容量和性能的服务器的功能。 名称均可用作辅助手段来帮助用户将其数据库部署到适当的 SKU。

作为最佳做法是，一种 SKU 中的所有宿主服务器都应具有相同的资源和性能特征。

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>提供使用 SQL Alwayson 可用性组的高可用性

配置 SQL Always On 实例需要额外的步骤和需要三个虚拟机 （或物理计算机。）本文假定你已深入了解的 Always On 可用性组。 有关详细信息，请参阅以下文章：

* [简介 SQL Server Always On 可用性组在 Azure 虚拟机](https://docs.microsoft.com/en-us/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On 可用性组 (SQL Server)](https://docs.microsoft.com/en-us/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> SQL 适配器资源提供程序_仅_支持 SQL 2016 SP1 Enterprise 或更高版本的 Always On 实例。 此适配器配置需要新的 SQL 功能，如自动种子设定。

此外，你必须启用[自动种子设定](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)上每个可用性组的每个 SQL Server 实例。

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

   下**SQL 托管服务器**，你可以将 SQL Server 资源提供程序连接到的 SQL Server 作为资源提供程序的后端的实际实例。

3. 填写表单与您的 SQL Server 实例的连接详细信息。 请确保你使用的 FQDN 地址始终在侦听器 （和可选端口号。）提供配置具有 sysadmin 权限的帐户的信息。

4. 检查相应的 Always On 可用性组框以启用对 SQL Alwayson 可用性组实例的支持。

   ![启用 Alwayson](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. 将 SQL Always On 实例添加到 SKU。

   > [!IMPORTANT]
   > 不能在相同 SKU 的 Always On 实例，并用混合独立服务器。 尝试在添加第一个托管服务器后混合类型会导致错误。

## <a name="make-the-sql-databases-available-to-users"></a>向用户提供的 SQL 数据库

创建计划和套餐，使用户能够使用 SQL 数据库。 添加**Microsoft.SqlAdapter**服务到计划，并添加默认配额，或创建新的配额。

![创建计划和套餐以包含数据库](./media/azure-stack-sql-rp-deploy/sqlrp-newplan.png)

## <a name="next-steps"></a>后续步骤

[添加数据库](azure-stack-sql-resource-provider-databases.md)
