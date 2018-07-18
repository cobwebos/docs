---
title: Azure Stack 上的 SQL 宿主服务器 | Microsoft Docs
description: 如何添加通过 SQL 适配器资源提供程序进行预配的 SQL 实例。
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
ms.date: 07/10/2018
ms.author: jeffgilb
ms.reviewer: jeffgo
ms.openlocfilehash: de2e1defeff9ab2dd78bdf019009b62955f73b88
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38970545"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>为 SQL 资源提供程序添加托管服务器

可以在中托管的虚拟机 (VM) 上的 SQL 实例[Azure Stack](azure-stack-poc.md)，或外部 Azure Stack 环境，只要 SQL 资源提供程序可以连接到的实例的 VM 上。

## <a name="overview"></a>概述

添加 SQL 宿主服务器之前，请查看以下必需的和常规要求。

### <a name="mandatory-requirements"></a>强制性的要求

* 启用 SQL Server 实例上的 SQL 身份验证。 SQL 资源提供程序 VM 未加入域，因为它只能连接到宿主服务器使用 SQL 身份验证。
* 配置为公用时安装在 Azure Stack 上的 SQL 实例的 IP 地址。 资源提供程序和用户，如 Web 应用通过用户网络通信，因此不需要连接到此网络上的 SQL 实例。

### <a name="general-requirements"></a>一般要求

* 将使用的 SQL 实例专用资源提供程序和用户工作负荷。 不能使用任何其他使用者正在使用的 SQL 实例。 此限制也适用于应用服务。
* 资源提供程序 （如下所述），使用适当的权限级别配置的帐户。
* 您负责管理 SQL 实例和其主机。  例如，资源提供程序不会将更新应用、 处理备份，或处理凭据轮换。

### <a name="sql-server-virtual-machine-images"></a>SQL Server 虚拟机映像

通过 Marketplace 管理功能提供了 SQL IaaS 虚拟机映像。 这些映像可以在 Azure 中提供的 SQL Vm 相同。

请确保您始终下载最新版**SQL IaaS 扩展**部署使用 Marketplace 项的 SQL VM 之前。 IaaS 扩展和相应的门户增强功能提供其他功能，例如自动修补和备份。 有关此扩展的详细信息，请参阅[自动执行管理任务 Azure 虚拟机上使用 SQL Server 代理扩展](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension)。

可以使用其他选项部署 SQL VM，包括 [Azure Stack 快速入门库](https://github.com/Azure/AzureStack-QuickStart-Templates)中的模板。

> [!NOTE]
> 必须通过用户订阅和不是默认提供商订阅创建安装在多节点 Azure Stack 上的任何宿主服务器。 必须通过用户门户或者使用相应的登录名通过 PowerShell 会话来创建这些服务器。 所有宿主服务器都是可计费的 Vm，并且必须具有相应的 SQL 许可证。 服务管理员可以是订阅的所有者。

### <a name="required-privileges"></a>所需的特权

以低于 SQL sysadmin 特权，可以创建一个管理用户。 用户只需为以下操作的权限：

* 数据库： 创建、 更改使用包容 (对于仅限 Always On)、 删除、 备份
* 可用性组：更改、联接、添加/删除数据库
* 登录：创建、选择、更改、删除、吊销
* 选择操作：\[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn)、sys.availability_replicas (AlwaysOn)、sys.databases、\[master\].\[sys\].\[dm_os_sys_memory\]、SERVERPROPERTY、\[master\].\[sys\].\[availability_groups\] (AlwaysOn)、sys.master_files

### <a name="additional-security-information"></a>其他安全信息

以下信息提供了额外的安全指南：

* 所有 Azure Stack 存储使用 BitLocker，因此 Azure Stack 上的任何 SQL 实例将使用都加密的 blob 存储进行都加密。
* SQL 资源提供程序完全支持 TLS 1.2。 确保通过 SQL RP 管理任何 SQL Server 配置 TLS 1.2_仅_，RP 将默认为的。 所有受支持的版本的 SQL Server 支持 TLS 1.2，请参阅[Microsoft SQL Server 的 TLS 1.2 支持](https://support.microsoft.com/en-us/help/3135244/tls-1-2-support-for-microsoft-sql-server)。
* 使用 SQL Server 配置管理器设置**ForceEncryption**选项以确保 SQL server 的所有通信都始终处于加密。 请参阅[若要将服务器配置为强制使用加密的连接](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#ConfigureServerConnections)。
* 请确保任何客户端应用程序还通过加密连接进行通信。
* 配置 RP 信任 SQL Server 实例使用的证书。

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>通过连接到独立宿主 SQL 服务器来提供容量。

可以运行使用任何版本的 SQL Server 2014 或 SQL Server 2016 的独立（非 HA）SQL 服务器。 请确保具有具有 sysadmin 权限的帐户的凭据。

若要添加已设置好的独立宿主服务器，请按照下列步骤：

1. 以服务管理员身份登录到 Azure Stack 运营商门户。

2. 选择**浏览** &gt; **管理资源** &gt; **SQL 宿主服务器**。

   ![SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   下**SQL 宿主服务器**，可以将 SQL 资源提供程序连接到充当资源提供程序的后端 SQL Server 的实例。

   ![SQL 适配器仪表板](./media/azure-stack-sql-rp-deploy/sqladapterdashboard.png)

3. 上**添加 SQL 宿主服务器**，提供您的 SQL Server 实例的连接详细信息。

   ![添加 SQL 宿主服务器](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    （可选） 提供一个实例的名称，并指定端口号，如果实例不分配给默认端口 1433年。

   > [!NOTE]
   > 只要用户和管理员 Azure 资源管理器可以访问 SQL 实例，资源提供程序就能控制此实例。 SQL 实例__必须__到资源提供程序以独占方式分配。

4. 添加服务器时，必须将其分配到现有 SKU 或创建新的 SKU。 下**添加宿主服务器**，选择**Sku**。

   * 若要使用现有的 SKU，选择可用的 SKU，然后选择**创建**。
   * 若要创建的 SKU，请选择 **+ 创建新的 SKU**。 在中**创建 SKU**，输入所需的信息，然后选择**确定**。

     ![创建的 SKU](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>提供使用 SQL Alwayson 可用性组实现高可用性

配置 SQL Always On 实例需要额外的步骤，并且需要三个 Vm （或物理计算机。）本文假定你已有深入了解的 Always On 可用性组。 有关详细信息，请参阅以下文章：

* [引入了 SQL Server Always On 可用性组在 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Always On 可用性组 (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> SQL 适配器资源提供程序_仅_支持 SQL 2016 SP1 企业版或更高版本实例 Always On 可用性组。 该适配器配置要求在新的 SQL 功能，例如自动种子设定。

### <a name="automatic-seeding"></a>自动种子设定

必须启用[自动种子设定](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group)上每个可用性组的每个 SQL Server 实例。

若要启用自动种子设定的所有实例上，编辑，然后在主副本的每个辅助实例上运行以下 SQL 命令：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

请注意，可用性组必须括在方括号中。

在辅助节点上运行以下 SQL 命令：

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>配置包含的数据库身份验证

在将包含的数据库添加到可用性组之前, 请确保包含的数据库身份验证服务器选项设置为 1 上的每个承载可用性组的可用性副本的服务器实例。 有关详细信息，请参阅[contained database authentication 服务器配置选项](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017)。

使用以下命令以设置每个实例包含的数据库身份验证服务器选项：

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>若要添加 SQL Always On 宿主服务器

1. 登录到 Azure Stack 管理门户为服务管理员。

2. 选择**浏览** &gt; **管理资源** &gt; **SQL 宿主服务器** &gt; **+ 添加**.

   下**SQL 宿主服务器**，你可以连接到充当资源提供程序的后端的实际实例的 SQL Server 的 SQL Server 资源提供程序。

3. 填写您的 SQL Server 实例的连接详细信息表。 请确保使用 Always On 侦听器 （和可选端口号和实例名称） 的 FQDN 地址。 提供有关配置具有 sysadmin 权限的帐户的信息。

4. 选中 Always On 可用性组框以启用对 SQL Always On 可用性组实例的支持。

   ![启用 Always On](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. 将 SQL Always On 实例添加到 SKU。

   > [!IMPORTANT]
   > 您不能与 Always On 实例在同一 SKU 中混合使用独立服务器。 尝试在添加第一个托管服务器后混合类型会导致错误。

## <a name="sku-notes"></a>SKU 说明

可以使用 Sku 以区分服务产品。 例如，您可以具有以下特征的 SQL 企业实例：
  
* 高容量
* 高性能
* 高可用性

Sku 不能分配给特定用户或组在此版本中。

 SKU 最长可能需要在一小时后才显示在门户中。 完全创建 SKU 之前，用户无法创建数据库。

>[!TIP]
>使用 SKU 名称，用于反映介绍中的 SKU，例如容量和性能的服务器的功能。 名称均可作为帮助，用于帮助用户将其数据库部署到适当的 SKU。

最佳做法是，在 SKU 中的所有宿主服务器应有相同的资源和性能特征。

## <a name="make-the-sql-databases-available-to-users"></a>将 SQL 数据库提供给用户

创建计划和套餐，使用户能够使用 SQL 数据库。 添加**Microsoft.SqlAdapter**计划到服务并创建新的配额。

## <a name="next-steps"></a>后续步骤

[添加数据库](azure-stack-sql-resource-provider-databases.md)
