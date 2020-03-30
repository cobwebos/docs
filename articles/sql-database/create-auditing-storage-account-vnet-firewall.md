---
title: 审核到 VNet 和防火墙后面的存储帐户
description: 配置审核以在虚拟网络和防火墙后面的存储帐户上写入数据库事件
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 6345d210e26747f921595039a2a3c8e11be11fda
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387617"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>将审核写入 VNet 和防火墙后面的存储帐户

[对 Azure SQL 数据库](sql-database-technical-overview.md)和 Azure[同步分析](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)进行审核支持将数据库事件写入虚拟网络和防火墙后面的[Azure 存储帐户](../storage/common/storage-account-overview.md)。 

本文介绍为此选项配置 Azure SQL Server 和 Azure 存储帐户的两种方法。 第一个使用 Azure 门户，第二个使用 REST。

### <a name="background"></a>背景

[Azure 虚拟网络 （VNet）](../virtual-network/virtual-networks-overview.md)是 Azure 中专用网络的基本构建基块。 VNet 允许许多类型的 Azure 资源（例如 Azure 虚拟机 (VM)）以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。 VNet 与您自己的数据中心中的传统网络类似，但也带来了 Azure 基础结构的其他优势，如扩展、可用性和隔离。

要了解有关 VNet 概念、最佳实践等的更多信息，请参阅什么是[Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。

要了解有关如何创建虚拟网络的更多信息，请参阅[快速入门：使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)。

## <a name="prerequisites"></a>先决条件

要将审核写入 VNet 或防火墙后面的存储帐户，需要以下先决条件：

> [!div class="checklist"]
> * 通用 v2 存储帐户。 如果您有通用 v1 或 Blob 存储帐户，则[升级到通用 v2 存储帐户](../storage/common/storage-account-upgrade.md)。 有关详细信息，请参阅[存储帐户的类型](../storage/common/storage-account-overview.md#types-of-storage-accounts)。
> * 存储帐户必须位于与 Azure SQL 数据库服务器相同的订阅和位置。 
> * Azure 存储帐户需要`Allow trusted Microsoft services to access this storage account`。 在存储帐户**防火墙和虚拟网络**上设置。
> * 您必须对选定的`Microsoft.Authorization/roleAssignments/write`存储帐户具有权限。 有关详细信息，请参阅[Azure 内置角色](../role-based-access-control/built-in-roles.md)。

## <a name="configure-in-azure-portal"></a>在 Azure 门户中配置

使用订阅连接到[Azure 门户](https://portal.azure.com)。 导航到资源组和 Azure SQL 数据库服务器。

1. 单击"安全"标题下的**审核**。 选择 **"打开**"。

2. 选择 **“存储”**。 选择将保存日志的存储帐户。 存储帐户必须符合[先决条件](#prerequisites)中列出的要求。

3. 打开**存储详细信息** 

  > [!NOTE]
  > 如果所选存储帐户位于 VNet 后面，您将看到以下消息：
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage: requires an Active Directory admin on the server; enables 'Allow trusted Microsoft services to access this storage account' on the storage account; and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >如果未看到此消息，则存储帐户不在 VNet 后面。

4. 选择保留期的天数。 然后单击“确定”****。 超过保留期日志的日志将被删除。

5. 选择 **"在**审核设置上保存"。

您已成功将审核配置为写入 VNet 或防火墙后面的存储帐户。 

## <a name="configure-with-rest-commands"></a>使用 REST 命令进行配置

作为使用 Azure 门户的替代方法，可以使用 REST 命令配置审核，以在 VNet 和防火墙后面的存储帐户上写入数据库事件。 

本节中的示例脚本要求您在运行脚本之前更新脚本。 替换脚本中的以下值：

|示例值|示例描述|
|:-----|:-----|
|`<subscriptionId>`| Azure 订阅 ID|
|`<resource group>`| 资源组|
|`<sql database server>`| Azure SQL 数据库服务器名称|
|`<administrator login>`| SQL 数据库管理员帐户 |
|`<complex password>`| 管理员帐户的复杂密码|

要配置 SQL 审核以将事件写入 VNet 或防火墙后面的存储帐户，请：

1. 将 Azure SQL 数据库服务器注册到 Azure 活动目录 （Azure AD）。 使用 PowerShell 或 REST API。

   **电源外壳**
   
   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```
   
   [**REST API**](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)：

   示例请求

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   请求正文

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
   }
   ```

2. 打开 [Azure 门户](https://portal.azure.com)。 导航到存储帐户。 找到**访问控制 （IAM），** 然后单击 **"添加角色分配**"。 将**存储 Blob 数据参与者**RBAC 角色分配给 Azure SQL 服务器，该服务器托管在 Azure 活动目录 （Azure AD） 中注册的 Azure SQL 数据库，如上一步骤所述。

   > [!NOTE]
   > 只有具有“所有者”特权的成员能够执行此步骤。 有关 Azure 资源的各种内置角色，请参阅[Azure 内置角色](../role-based-access-control/built-in-roles.md)。

3. 配置[Azure SQL 服务器的 blob 审核策略](/rest/api/sql/server%20auditing%20settings/createorupdate)，而不指定*存储帐户访问密钥*：

   示例请求

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2017-03-01-preview
   ```

   请求正文

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="next-steps"></a>后续步骤

- [使用 PowerShell 创建虚拟网络服务终结点，然后创建 Azure SQL 数据库的虚拟网络规则。](sql-database-vnet-service-endpoint-rule-powershell.md)
- [虚拟网络规则：使用 REST API 的操作](/rest/api/sql/virtualnetworkrules)
- [为数据库服务器使用虚拟网络服务终结点和规则](sql-database-vnet-service-endpoint-rule-overview.md)
