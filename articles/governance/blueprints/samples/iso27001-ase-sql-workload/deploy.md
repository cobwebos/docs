---
title: 示例 - ISO 27001 ASE/SQL 工作负荷蓝图 - 部署步骤
description: ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图示例的部署步骤
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: 4804e10488c537dbf997aba0ec49f784bc67457d
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981591"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>部署 ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图示例

若要部署 Azure 蓝图 ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 部署 [ISO 27001 共享服务](../iso27001-shared/index.md)蓝图示例
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布” 
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>部署 ISO 27001 共享服务蓝图示例

在部署此蓝图示例之前，必须先将 [ISO 27001 共享服务](../iso27001-shared/index.md)蓝图示例部署到目标订阅。 如果未成功部署 ISO 27001 共享服务蓝图示例，则此蓝图示例将会缺少基础结构依赖项，并且在部署期间将会失败。

> [!IMPORTANT]
> 必须在 [ISO 27001 共享服务](../iso27001-shared/index.md)蓝图示例所在的同一个订阅中分配此蓝图示例。

## <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。   

1. 在“其他示例”下找到“ISO 27001:  ASE/SQL 工作负荷”蓝图示例，然后选择“使用此示例”。  

1. 输入该蓝图示例的“基本信息”： 

   - **蓝图名称**：提供 ISO 27001 ASE/SQL 工作负荷蓝图示例副本的名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:   项目”。

1. 查看构成蓝图示例的项目列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。 

## <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其**发布**，然后才能分配和部署它。  可根据环境和需求自定义蓝图示例的副本，但这种修改可能会将该副本移出 ISO 27001 标准。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧选择“蓝图定义”页。  使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。  在右侧的新窗格中，提供蓝图示例副本的**版本**。 以后做出修改时，此属性非常有用。 提供**更改注释**，例如，“基于 ISO 27001 蓝图示例发布的第一个版本”。 然后选择页面底部的“发布”。 

## <a name="assign-the-sample-copy"></a>分配示例副本

成功**发布**蓝图示例的副本后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，需提供参数来使蓝图示例副本的每个部署保持唯一。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧选择“蓝图定义”页。  使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择蓝图定义页面顶部的“分配蓝图”。 

1. 提供蓝图分配的参数值：

   - 基础

     - **订阅**：在蓝图示例副本所保存到的管理组中选择一个或多个订阅。 如果选择多个订阅，将使用输入的参数为每个订阅创建一个分配。
     - **分配名称**：系统会根据蓝图的名称预先填充该名称。
       请根据需要更改该名称，或保留原样。
     - **位置**：选择要在其中创建托管标识的区域。 Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **蓝图定义版本**：选择蓝图示例副本的**已发布**版本。

   - 锁分配

     选择环境的蓝图锁定设置。 有关更多信息，请参阅[蓝图资源锁定](../../concepts/resource-locking.md)。

   - 托管标识

     保留默认的系统分配的托管标识选项。 

   - 蓝图参数

     蓝图定义中的许多项目使用本部分定义的参数来提供一致性。

     - **组织名称**：输入组织的短名称。 此属性主要用于为资源命名。
     - **共享服务订阅 ID**：[ISO 27001 共享服务](../iso27001-shared/index.md)蓝图示例所分配到的订阅 ID。
     - **默认子网地址前缀**：虚拟网络默认子网的 CIDR 表示法。
       默认值为 _10.1.0.0/16_。
     - **工作负荷位置**：确定要将项目部署到的位置。 并非所有服务都可在所有位置使用。 部署此类服务的项目会针对该项目要部署到的位置提供一个参数选项。

   - 项目参数

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../../concepts/parameters.md#dynamic-parameters) ，因为它们是在分配蓝图期间定义的。 有关完整列表或项目参数及其说明，请参阅[项目参数表](#artifact-parameters-table) 。

1. 输入所有参数后，选择页面底部的“分配”。  随后将创建蓝图分配，并开始部署项目。 部署过程大约需要一小时。 若要检查部署状态，请打开蓝图分配。

> [!WARNING]
> Azure 蓝图服务和内置蓝图示例是**免费的**。 Azure 资源[按产品定价](https://azure.microsoft.com/pricing/) 。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/) 可以估算运行此蓝图示例部署的资源所需的成本。

## <a name="artifact-parameters-table"></a>项目参数表

下表提供了蓝图项目参数的列表：

|项目名称|项目类型|参数名称|说明|
|-|-|-|-|
|Log Analytics 资源组|Resource group|Name|**已锁定** - 将**组织名称**与 `-workload-log-rg` 相连接可使资源组名称保持唯一。|
|Log Analytics 资源组|Resource group|位置|**已锁定** - 使用蓝图参数。|
|Log Analytics 模板|资源管理器模板|服务层|设置 Log Analytics 工作区的层。 默认值为 _PerNode_。|
|Log Analytics 模板|资源管理器模板|日志保留期(以天为单位)|日志保留期（以天为单位）。 默认值为 _365_。|
|Log Analytics 模板|资源管理器模板|位置|用于创建 Log Analytics 工作区的区域。 默认值为“美国西部 2”。 |
|网络资源组|Resource group|Name|**已锁定** - 将**组织名称**与 `-workload-net-rg` 相连接可使资源组名称保持唯一。|
|网络资源组|Resource group|位置|**已锁定** - 使用蓝图参数。|
|网络安全组模板|资源管理器模板|日志保留期(以天为单位)|日志保留期（以天为单位）。 默认值为 _365_。|
|虚拟网络和路由表模板|资源管理器模板|Azure 防火墙专用 IP|配置 [Azure 防火墙](../../../../firewall/overview.md)的专用 IP。 应是“ISO 27001:  共享服务”项目参数“Azure 防火墙子网地址前缀”中定义的 CIDR 表示法的一部分。  默认值为 _10.0.4.4_。|
|虚拟网络和路由表模板|资源管理器模板|共享服务订阅 ID|用于启用工作负荷与共享服务之间的 VNET 对等互连的值。|
|虚拟网络和路由表模板|资源管理器模板|虚拟网络地址前缀|虚拟网络的 CIDR 表示法。 默认值为 _10.1.0.0/16_。|
|虚拟网络和路由表模板|资源管理器模板|默认子网地址前缀|虚拟网络默认子网的 CIDR 表示法。 默认值为 _10.1.0.0/16_。|
|虚拟网络和路由表模板|资源管理器模板|ADDS IP 地址|第一个 ADDS VM 的 IP 地址。 此值用作自定义 VNET DNS。|
|密钥保管库资源组|Resource group|Name|**已锁定** - 将**组织名称**与 `-workload-kv-rg` 相连接可使资源组名称保持唯一。|
|密钥保管库资源组|Resource group|位置|**已锁定** - 使用蓝图参数。|
|Key Vault 模板|资源管理器模板|AAD 对象 ID|需要访问 Key Vault 实例的帐户的 AAD 对象标识符。 无默认值，且不能留空。 若要在 Azure 门户中查找此值，请在“服务”下搜索并选择“用户”。  使用“名称”框筛选帐户名，并选择该帐户。  在“用户配置文件”页上，选择“对象 ID”旁边的“单击以复制”图标。  |
|Key Vault 模板|资源管理器模板|日志保留期(以天为单位)|日志保留期（以天为单位）。 默认值为 _365_。|
|Key Vault 模板|资源管理器模板|Key Vault SKU|指定创建的 Key Vault 的 SKU。 默认值为“高级”。 |
|Key Vault 模板|资源管理器模板|Azure SQL Server 管理员用户名|用于访问 Azure SQL Server 的用户名。 必须与 **Azure SQL 数据库模板**中的相同属性值相匹配。 默认值为 _sql-admin-user_。|
|Azure SQL 数据库资源组|Resource group|Name|**已锁定** - 将**组织名称**与 `-workload-azsql-rg` 相连接可使资源组名称保持唯一。|
|Azure SQL 数据库资源组|Resource group|位置|**已锁定** - 使用蓝图参数。|
|Azure SQL 数据库模板|资源管理器模板|Azure SQL Server 管理员用户名|Azure SQL 服务器的用户名。 必须与 **Key Vault 模板**中的相同属性值相匹配。 默认值为 _sql-admin-user_。|
|Azure SQL 数据库模板|资源管理器模板|Azure SQL Server 管理员密码(Key Vault 资源 ID)|Key Vault 的资源 ID。 请使用 "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv"，并将 `{subscriptionId}` 替换为你的订阅 ID，将 `{orgName}` 替换为“组织名称”蓝图参数。 |
|Azure SQL 数据库模板|资源管理器模板|Azure SQL Server 管理员密码(Key Vault 机密名称)|SQL Server 管理员的用户名。必须与 **Key Vault 模板**中的“Azure SQL Server 管理员用户名”属性值相匹配。 |
|Azure SQL 数据库模板|资源管理器模板|日志保留期(以天为单位)|日志保留期（以天为单位）。 默认值为 _365_。|
|Azure SQL 数据库模板|资源管理器模板|AAD 管理员对象 ID|分配为 Active Directory 管理员的用户的 AAD 对象 ID。无默认值，且不能留空。 若要在 Azure 门户中查找此值，请在“服务”下搜索并选择“用户”。  使用“名称”框筛选帐户名，并选择该帐户。  在“用户配置文件”页上，选择“对象 ID”旁边的“单击以复制”图标。  |
|Azure SQL 数据库模板|资源管理器模板|AAD 管理员登录名|目前，无法将 Microsoft 帐户（如 live.com 或 outlook.com）设置为管理员。只能将你组织中的用户和安全组设置为管理员。无默认值，且不能留空。 若要在 Azure 门户中查找此值，请在“服务”下搜索并选择“用户”。  使用“名称”框筛选帐户名，并选择该帐户。  在“用户配置文件”页上，复制“用户名”。  |
|应用服务环境资源组|Resource group|Name|**已锁定** - 将**组织名称**与 `-workload-ase-rg` 相连接可使资源组名称保持唯一。|
|应用服务环境资源组|Resource group|位置|**已锁定** - 使用蓝图参数。|
|应用服务环境模板|资源管理器模板|域名|示例创建的 Active Directory 的名称。 默认值为 _contoso.com_。|
|应用服务环境模板|资源管理器模板|ASE 位置|应用服务环境位置。 默认值为“美国西部 2”。 |
|应用服务环境模板|资源管理器模板|应用程序网关日志保留期(天)|日志保留期（以天为单位）。 默认值为 _365_。|

## <a name="next-steps"></a>后续步骤

了解 ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图示例的部署步骤后，请访问以下文章来了解体系结构和控制映射：

> [!div class="nextstepaction"]
> [ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图 - 概述](./index.md)
> [ISO 27001 应用服务环境/SQL 数据库工作负荷蓝图 - 控制映射](./control-mapping.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。