---
title: 将审核内容写入到 VNet 和防火墙后面的存储帐户
description: 配置审核以在虚拟网络和防火墙后面的存储帐户中写入数据库事件
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 03/19/2020
ms.custom: azure-synapse
ms.openlocfilehash: 4f26fa00f78b8564e08b6352d4da31640b13f47f
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402685"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>将审核内容写入到 VNet 和防火墙后面的存储帐户

对 [Azure SQL 数据库](sql-database-technical-overview.md)和 [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) 的审核支持将数据库事件写入到虚拟网络和防火墙后面的 [Azure 存储帐户](../storage/common/storage-account-overview.md)。 

本文介绍了为此选项配置 Azure SQL Server 和 Azure 存储帐户的两种方法。 第一种方法使用 Azure 门户，第二种方法使用 REST。

### <a name="background"></a>背景

[Azure 虚拟网络 (VNet)](../virtual-network/virtual-networks-overview.md) 是 Azure 中专用网络的基础构建基块。 VNet 允许许多类型的 Azure 资源（例如 Azure 虚拟机 (VM)）以安全方式彼此通信、与 Internet 通信，以及与本地网络通信。 VNet 类似于你自己的数据中心内的传统网络，但附带了 Azure 基础设施的其他优势，例如可伸缩性、可用性和隔离。

若要详细了解 VNet 的概念、最佳做法和其他信息，请参阅[什么是 Azure 虚拟网络](../virtual-network/virtual-networks-overview.md)。

若要详细了解如何创建虚拟网络，请参阅[快速入门：使用 Azure 门户创建虚拟网络](../virtual-network/quick-create-portal.md)。

## <a name="prerequisites"></a>先决条件

若要配置审核以写入到 VNet 或防火墙后面的存储帐户，需要满足以下先决条件：

> [!div class="checklist"]
> * 一个常规用途 v2 存储帐户。 如果你有常规用途 v1 或 Blob 存储帐户，请[升级到常规用途 v2 存储帐户](../storage/common/storage-account-upgrade.md)。 有关详细信息，请参阅[存储帐户的类型](../storage/common/storage-account-overview.md#types-of-storage-accounts)。
> * 存储帐户必须位于 Azure SQL 数据库服务器所在的同一订阅和位置。 
> * Azure 存储帐户需要 `Allow trusted Microsoft services to access this storage account`。 请在存储帐户的“防火墙和虚拟网络”中启用此设置。 
> * 你必须对所选存储帐户拥有 `Microsoft.Authorization/roleAssignments/write` 权限。 有关详细信息，请参阅 [Azure 内置角色](../role-based-access-control/built-in-roles.md)。

## <a name="configure-in-azure-portal"></a>在 Azure 门户中配置

使用你的订阅连接到 [Azure 门户](https://portal.azure.com)。 导航到资源组和 Azure SQL 数据库服务器。

1. 单击“安全性”标题下的“审核”。  选择“启用”。 

2. 选择“存储”  。 选择要在其中保存日志的存储帐户。 该存储帐户必须符合[先决条件](#prerequisites)中列出的要求。

3. 打开“存储详细信息”  

  > [!NOTE]
  > 如果所选的存储帐户位于 VNet 后面，你将看到以下消息：
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >如果未看到此消息，则表示存储帐户不在 VNet 后面。

4. 选择保留期天数。 然后单击“确定”。  早于保留期的日志会被删除。

5. 在审核设置中选择“保存”。 

你现在已成功将审核配置为将内容写入到 VNet 或防火墙后面的存储帐户。 

## <a name="configure-with-rest-commands"></a>使用 REST 命令进行配置

如果不使用 Azure 门户，还可以使用 REST 命令来配置审核，以便在 VNet 和防火墙后面的存储帐户中写入数据库事件。 

在运行本部分中的示例脚本之前，你需要先更新这些脚本。 替换脚本中的以下值：

|示例值|示例说明|
|:-----|:-----|
|`<subscriptionId>`| Azure 订阅 ID|
|`<resource group>`| 资源组|
|`<sql database server>`| Azure SQL 数据库服务器名称|
|`<administrator login>`| SQL 数据库管理员帐户 |
|`<complex password>`| 管理员帐户的复杂密码|

若要配置 SQL 审核以将事件写入到 VNet 或防火墙后面的存储帐户，请执行以下操作：

1. 将 Azure SQL 数据库服务器注册到 Azure Active Directory (Azure AD)。 使用 PowerShell 或 REST API。

   **PowerShell**
   
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

2. 打开 [Azure 门户](https://portal.azure.com)。 导航到存储帐户。 找到“访问控制(IAM)”，然后单击“添加角色分配”。   将“存储 Blob 数据参与者”RBAC 角色分配给托管 Azure SQL 数据库且已在上一步骤中注册到 Azure Active Directory (Azure AD) 的 Azure SQL Server。 

   > [!NOTE]
   > 只有具有“所有者”特权的成员能够执行此步骤。 若要了解 Azure 资源的各种内置角色，请参阅 [Azure 内置角色](../role-based-access-control/built-in-roles.md)。

3. 在不指定 storageAccountAccessKey  的情况下配置 [Azure SQL Server 的 Blob 审核策略](/rest/api/sql/server%20auditing%20settings/createorupdate)：

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
- [对数据库服务器使用虚拟网络服务终结点和规则](sql-database-vnet-service-endpoint-rule-overview.md)
