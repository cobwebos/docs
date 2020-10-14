---
title: 服务器信任组
titleSuffix: Azure SQL Managed Instance
description: 了解服务器信任组，以及如何管理 Azure SQL 托管实例之间的信任。
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sasapopo
ms.author: sasapopo
ms.reviewer: sstein, bonova
ms.date: 10/08/2020
ms.openlocfilehash: 6154625f1e943007d0ed4c3341dc1265657f3bfc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92046326"
---
# <a name="use-server-trust-groups-to-set-up-and-manage-trust-between-sql-managed-instances"></a>使用服务器信任组来设置和管理 SQL 托管实例之间的信任
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

服务器信任组是用于管理 Azure SQL 托管实例之间的信任的概念。 通过创建组，将在其成员之间建立基于证书的信任。 此信任可用于不同的跨实例方案。 删除组中的服务器或删除组会删除服务器之间的信任。 若要创建或删除服务器信任组，用户需要对托管实例具有写入权限。
[服务器信任组](https://aka.ms/mi-server-trust-group-arm) 是一个 Azure 资源管理器对象，它在 Azure 门户中标记为 " **SQL 信任组** "。

> [!NOTE]
> 服务器信任组是在 Azure SQL 托管实例之间的分布式事务的公共预览版中引入的，当前存在一些限制，本文稍后将对此进行介绍。

## <a name="server-trust-group-setup"></a>服务器信任组设置

以下部分介绍服务器信任组的设置。

1. 转到 [Azure 门户](https://portal.azure.com/)。

2. 导航到计划添加到新创建的服务器信任组的 Azure SQL 托管实例。

3. 在 " **安全** 设置" 中，选择 " **SQL 信任组** " 选项卡。

   :::image type="content" source="./media/server-trust-group-overview/security-sql-trust-groups.png" alt-text="服务器信任组&quot;:::

4. 在 &quot;服务器信任组配置&quot; 页上，选择 &quot; **新建组** " 图标。

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-new-group.png" alt-text="服务器信任组&quot;:::

4. 在 &quot;服务器信任组配置&quot; 页上，选择 &quot; **新建组** " 边栏选项卡上，设置 **组名称**。 它必须在组成员所在的所有区域中是唯一的。 **信任作用域** 定义通过服务器信任组启用的跨实例方案的类型。 在预览中，唯一适用的信任作用域是 **分布式事务**，因此它是预先选择的，无法更改。 所有 **组成员** 必须属于同一 **订阅** ，但可以位于不同的资源组下。 选择 **资源组** 和 **SQL Server/实例** 以选择将作为组成员的 Azure SQL 托管实例。

   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-create-blade.png" alt-text="服务器信任组&quot;:::

4. 在 &quot;服务器信任组配置&quot; 页上，选择 &quot; **新建组** " **保存**"。

## <a name="server-trust-group-maintenance-and-deletion"></a>服务器信任组的维护和删除

不能编辑服务器信任组。 若要从组中删除托管实例，需要删除组并创建一个新组。

以下部分介绍服务器信任组删除过程。 
1. 转到 Azure 门户。
2. 导航到属于信任组的托管实例。
3. 在 " **安全** 设置" 中，选择 " **SQL 信任组** " 选项卡。
4. 选择要删除的信任组。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-select.png" alt-text="服务器信任组&quot;:::

4. 在 &quot;服务器信任组配置&quot; 页上，选择 &quot; **新建组** " **删除组**"。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete.png" alt-text="服务器信任组&quot;:::

4. 在 &quot;服务器信任组配置&quot; 页上，选择 &quot; **新建组** " **删除**"。
   :::image type="content" source="./media/server-trust-group-overview/server-trust-group-manage-delete-confirm.png" alt-text="服务器信任组&quot;:::

4. 在 &quot;服务器信任组配置&quot; 页上，选择 &quot; **新建组** ":::

> [!NOTE]
> 删除服务器信任组可能不会立即删除两个托管实例之间的信任。 可以通过调用托管实例的 [故障转移](https://docs.microsoft.com/powershell/module/az.sql/Invoke-AzSqlInstanceFailover) 来强制删除信任。 查看此上最新更新的 [已知问题](https://docs.microsoft.com/azure/azure-sql/database/doc-changes-updates-release-notes?tabs=managed-instance#known-issues) 。

## <a name="limitations"></a>限制

在公共预览版期间，对服务器信任组有以下限制。
 * 服务器信任组的名称在其成员为的所有区域中必须是唯一的。
 * 组只能包含 Azure SQL 托管实例，且必须位于同一 Azure 订阅下。
 * 在预览版中，组只能有两个托管实例。 若要跨两个以上的托管实例执行分布式事务，将需要为每对托管实例创建服务器信任组。
 * 对于服务器信任组，分布式事务是唯一适用的作用域。
 * 只能从 Azure 门户管理服务器信任组。 稍后将提供 PowerShell 和 CLI 支持。
 * 无法在 Azure 门户上编辑服务器信任组。 只能创建或删除它。
 * 分布式事务的其他限制可能与你的方案有关。 最值得注意的是，在通过 VNET 或 VNET 对等互连在专用终结点上的托管实例之间必须建立连接。 请确保已了解托管实例的当前 [分布式事务限制](https://docs.microsoft.com/azure/azure-sql/database/elastic-transactions-overview#limitations)。

## <a name="next-steps"></a>后续步骤

* 有关 Azure SQL 托管实例中的分布式事务的详细信息，请参阅 [分布式事务](../database/elastic-transactions-overview.md)。
* 有关发布更新和已知问题的状态，请参阅 [托管实例发行说明](../database/doc-changes-updates-release-notes.md)。
* 如果有功能请求，请将它们添加到 [托管实例论坛](https://feedback.azure.com/forums/915676-sql-managed-instance)。