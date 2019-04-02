---
title: 示例-ISO 27001 共享服务蓝图-部署步骤
description: 部署的 ISO 27001 共享服务蓝图示例的步骤。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f49951d0a2ec738df9946edc7f44820c2cde975f
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804326"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>部署 ISO 27001 共享服务蓝图示例

若要部署 Azure 蓝图 ISO 27001 共享服务蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 从示例创建一个新的蓝图
> - 将示例副本标记为“已发布”
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>从示例创建蓝图

首先，通过使用示例作为 starter 在环境中创建一个新的蓝图实现蓝图示例。

1. 选择**所有服务**，搜索并选择**策略**的左窗格中。 上**策略**页上，选择**蓝图**。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。

1. 查找**ISO 27001:共享服务**下的蓝图示例_其他示例_，然后选择**使用此示例**。

1. 输入该蓝图示例的“基本信息”：

   - **蓝图名称**：提供您的 ISO 27001 共享服务蓝图示例副本的名称。
   - **定义位置**：使用省略号并选择要保存到示例的副本的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:项目”。

1. 查看构成蓝图示例的项目列表。 许多项目都有我们将更高版本定义的参数。 查看完蓝图示例后，选择“保存草稿”。

## <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其**发布**，然后才能分配和部署它。 蓝图示例的副本可以进行自定义您的环境和需求，但该修改可能会将其移离 ISO 27001 标准。

1. 选择**所有服务**，搜索并选择**策略**的左窗格中。 上**策略**页上，选择**蓝图**。

1. 在左侧选择“蓝图定义”页。 使用筛选器查找蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。 在右侧新页上，提供**版本**蓝图示例的副本。 以后做出修改时，此属性非常有用。 提供**更改说明**如"第一个版本发布从 ISO 27001 蓝图示例"。 然后选择页面底部的“发布”。

## <a name="assign-the-sample-copy"></a>分配示例副本

成功**发布**蓝图示例的副本后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，需提供参数来使蓝图示例副本的每个部署保持唯一。

1. 选择**所有服务**，搜索并选择**策略**的左窗格中。 上**策略**页上，选择**蓝图**。

1. 在左侧选择“蓝图定义”页。 使用筛选器查找蓝图示例的副本，然后选择它。

1. 选择蓝图定义页面顶部的“分配蓝图”。

1. 提供蓝图分配的参数值：

   - 基本信息

     - **订阅**：在蓝图示例副本所保存到的管理组中选择一个或多个订阅。 如果选择多个订阅，将使用输入的参数为每个订阅创建一个分配。
     - **分配名称**：名称是为你基于名称 blueprint 预填充的。
       根据需要更改或保留原样。
     - **位置**：选择要在其中创建托管标识的区域。 Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../../../../active-directory/managed-identities-azure-resources/overview.md)。
     - **蓝图定义版本**：选取**已发布**副本的蓝图示例版本。

   - 锁定分配

     选择为你的环境设置的蓝图锁。 有关更多信息，请参阅[蓝图资源锁定](../../concepts/resource-locking.md)。

   - 托管标识

     保留默认值_系统分配_托管标识选项。

   - 蓝图参数

     在本部分中定义的参数由许多蓝图定义中的项目中用于提供一致性。

     - **组织名称**:为你的组织输入短名称。 此属性主要用于命名资源。
     - **共享的服务子网地址前缀**:网络已部署的资源一起提供的 CIDR 表示法值。
     - **共享服务的位置**:确定哪些项目部署到的位置。 并非所有服务在所有位置都都可用。 部署此类服务的项目提供一个要部署到该项目的位置的参数选项。
     - **允许的位置 (策略：Iso 27001，从而蓝图计划）**:值，该值指示允许的位置的资源组和资源。
     - **Log Analytics 工作区的 VM 代理 (策略：Iso 27001，从而蓝图计划）**:指定一个工作区的资源 ID。 此参数使用`concat`函数来构造资源 id。

   - 项目参数

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../../concepts/parameters.md#dynamic-parameters)，因为它们是在分配蓝图期间定义的。 有关完整列表或项目参数和及其说明，请参阅[项目参数表](#artifact-parameters-table)。

1. 输入所有参数后，选择页面底部的“分配”。 创建蓝图分配和项目部署开始。 部署需要约一小时。 若要检查部署的状态，请打开蓝图分配。

> [!WARNING]
> Azure 蓝图服务和内置蓝图示例均**免费**。 Azure 资源都[按产品定价](https://azure.microsoft.com/en-us/pricing/)。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/)来估计运行此蓝图示例部署的资源的成本。

## <a name="artifact-parameters-table"></a>项目参数表

下表提供了一系列蓝图项目参数：

|项目名称|项目类型|参数名称|描述|
|-|-|-|-|
|[预览]:部署 Linux VM 规模集 (VMSS) 的 Log Analytics 代理|策略分配|可选：具有支持 Linux OS 将添加到作用域的 VM 映像列表|（可选）默认值是 _["none"]_。|
|[预览]:为 Linux VM 部署 Log Analytics 代理|策略分配|可选：具有支持 Linux OS 将添加到作用域的 VM 映像列表|（可选）默认值是 _["none"]_。|
|[预览]:部署 Windows VM 规模集 (VMSS) 的 Log Analytics 代理|策略分配|可选：具有支持的 Windows 操作系统将添加到作用域的 VM 映像列表|（可选）默认值是 _["none"]_。|
|[预览]:为 Windows VM 部署 Log Analytics 代理|策略分配|可选：具有支持的 Windows 操作系统将添加到作用域的 VM 映像列表|（可选）默认值是 _["none"]_。|
|允许的资源类型|策略分配|允许的资源类型|允许要部署的资源类型的列表。 此列表包含在共享服务中部署的所有资源类型。|
|允许的存储帐户 SKU|策略分配|允许存储 Sku|列表中的诊断日志的存储帐户 Sku 允许。 默认值是 _"Standard_LRS"_。|
|允许的虚拟机 SKU|策略分配|允许要部署的虚拟机 Sku 的列表。 默认值是 _["Standard_DS1_v2"，"Standard_DS2_v2"]_。|
|ISO 27001 蓝图计划|策略分配|资源类型，以审核诊断日志|如果未启用诊断日志设置审核的资源类型的列表。 可接受的值，请参阅[Azure Monitor 诊断日志架构](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type)。|
|Log Analytics 资源组|资源组|姓名|**锁定**-将串联**组织名称**与`-sharedsvsc-log-rg`以使资源组唯一。|
|Log Analytics 资源组|资源组|位置|**锁定**-使用蓝图参数。|
|Log Analytics 模板|Resource Manager 模板|服务层|设置 Log Analytics 工作区的层。 默认值是_PerNode_。|
|Log Analytics 模板|Resource Manager 模板|日志保留期(以天为单位)|以天为单位的数据保留期。 默认值是_365_。|
|Log Analytics 模板|Resource Manager 模板|位置|用于创建 Log Analytics 工作区的区域。 默认值是_美国西部 2_。|
|网络资源组|资源组|姓名|**锁定**-将串联**组织名称**与`-sharedsvcs-net-rg`以使资源组唯一。|
|网络资源组|资源组|位置|**锁定**-使用蓝图参数。|
|Azure 防火墙模板|Resource Manager 模板|Azure 防火墙专用 IP|配置的专用 IP [Azure 防火墙](../../../../firewall/overview.md)。 此值还用作共享的服务子网上的默认路由表。 应为一部分中定义的 CIDR 表示法**Azure 防火墙子网地址前缀**。 默认值是_10.0.4.4_。|
|Azure 防火墙模板|Resource Manager 模板|日志保留期(以天为单位)|以天为单位的数据保留期。 默认值是_365_。|
|网络安全组模板|Resource Manager 模板|日志保留期(以天为单位)|以天为单位的数据保留期。 默认值是_365_。|
|虚拟网络和路由表模板|Resource Manager 模板|虚拟网络地址前缀|虚拟网络的 CIDR 表示法。 默认值是_10.0.0.0/16_。|
|虚拟网络和路由表模板|Resource Manager 模板|启用虚拟网络 DDoS 保护|配置虚拟网络的 DDoS 保护。 默认值是 _，则返回 true_。|
|虚拟网络和路由表模板|Resource Manager 模板|共享服务子网地址前缀|共享服务子网的 CIDR 表示法。 默认值是_10.0.0.0/24_。|
|虚拟网络和路由表模板|Resource Manager 模板|DMZ 子网地址前缀|外围网络子网的 CIDR 表示法。 默认值是_10.0.1.0/24_。|
|虚拟网络和路由表模板|Resource Manager 模板|应用程序网关的子网地址前缀|应用程序网关子网的 CIDR 表示法。 默认值是_10.0.2.0/24_。|
|虚拟网络和路由表模板|Resource Manager 模板|虚拟网络网关的子网地址前缀|虚拟网络网关子网的 CIDR 表示法。 默认值是_10.0.3.0/24_。|
|虚拟网络和路由表模板|Resource Manager 模板|Azure 防火墙子网地址前缀|有关使用 CIDR 表示法[Azure 防火墙](../../../../firewall/overview.md)子网。 应包括**Azure 防火墙的专用 IP**参数。|
|密钥保管库资源组|资源组|姓名|**锁定**-将串联**组织名称**与`-sharedsvcs-kv-rg`以使资源组唯一。|
|密钥保管库资源组|资源组|位置|**锁定**-使用蓝图参数。|
|Key Vault 模板|Resource Manager 模板|Jumpbox 管理员用户名|Jumpbox 的用户名。 中的相同属性值必须匹配**Jumpbox 模板**。 默认值是_jb 管理员用户_。|
|Key Vault 模板|Resource Manager 模板|Jumpbox 管理员 ssh 密钥或密码|密钥或 jumpbox 上的帐户的密码。 中的相同属性值必须匹配**Jumpbox 模板**。 没有默认值，并不能为空。|
|Key Vault 模板|Resource Manager 模板|域管理员用户名|用于访问 Active Directory VM 并将其他虚拟机加入到域的用户名。 必须与匹配**域管理员用户**中的属性值**Active Directory 域服务模板**。 默认值是_域管理员用户_。|
|Key Vault 模板|Resource Manager 模板|域管理员密码|域管理员用户的密码。 没有默认值，并不能为空。|
|Key Vault 模板|Resource Manager 模板|AAD 对象 ID|需要访问 Key Vault 实例的帐户的 AAD 对象标识符。 没有默认值，并不能为空。 若要查找此值在 Azure 门户中，搜索并选择"用户"下_Services_。 使用_名称_框，以筛选的帐户名称并选择该帐户。 上_用户配置文件_页上，选择"单击以复制"图标旁边_对象 ID_。  |
|Key Vault 模板|Resource Manager 模板|日志保留期(以天为单位)|以天为单位的数据保留期。 默认值是_365_。|
|Key Vault 模板|Resource Manager 模板|Key Vault SKU|指定创建的密钥保管库的 SKU。 默认值是_高级_。|
|Jumpbox 资源组|资源组|姓名|**锁定**-将串联**组织名称**与`-sharedsvcs-jb-rg`以使资源组唯一。|
|Jumpbox 资源组|资源组|位置|**锁定**-使用蓝图参数。|
|Jumpbox 模板|Resource Manager 模板|Jumpbox 管理员用户名|用于访问 jumpbox Vm 的用户名。 中的相同属性值必须匹配**密钥保管库模板**。 默认值是_jb 管理员用户_。|
|Jumpbox 模板|Resource Manager 模板|Jumpbox 管理员密码 (密钥保管库资源 ID)|密钥保管库资源 ID。 使用"/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv"并替换`{subscriptionId}`与你的订阅 ID 和`{orgName}`与**组织名称**蓝图参数。|
|Jumpbox 模板|Resource Manager 模板|Jumpbox 管理员密码 （密钥保管库机密名称）|Jumpbox 管理员的用户名中的值必须匹配**密钥保管库模板**属性**Jumpbox 管理员用户名**。|
|Jumpbox 模板|Resource Manager 模板|Jumpbox 操作系统|确定的 jumpbox VM 的操作系统。 默认值是_Windows_。|
|Active Directory 域服务资源组|资源组|姓名|**锁定**-将串联**组织名称**与`-sharedsvcs-adds-rg`以使资源组唯一。|
|Active Directory 域服务资源组|资源组|位置|**锁定**-使用蓝图参数。|
|Active Directory 域服务模板|Resource Manager 模板|域管理员用户名|ADDS jumpbox 的用户名。 中的相同属性值必须匹配**密钥保管库模板**。 默认值是_添加管理员用户_。|
|Active Directory 域服务模板|Resource Manager 模板|域管理员密码 (密钥保管库资源 ID)|密钥保管库资源 ID。 使用"/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv"并替换`{subscriptionId}`与你的订阅 ID 和`{orgName}`与**组织名称**蓝图参数。|
|Active Directory 域服务模板|Resource Manager 模板|域管理员密码 （密钥保管库机密名称）|域管理员的用户名中的值必须匹配**密钥保管库模板**属性**域管理员用户名**。|
|Active Directory 域服务模板|Resource Manager 模板|域名|该示例创建的 Active Directory 的名称。 默认值是_contoso.com_。|
|Active Directory 域服务模板|Resource Manager 模板|域管理员用户|管理 AD 帐户和用于将设备加入到的 AD 域的用户名。 必须与匹配**AD 管理员用户名**中的属性值**密钥保管库模板**。 默认值是_域管理员用户_。|
|Active Directory 域服务模板|Resource Manager 模板|域管理员密码|设置存储密码的密钥保管库详细信息。 没有默认值，并不能为空。|

## <a name="next-steps"></a>后续步骤

现在，已学习了部署 ISO 27001 共享服务蓝图示例的步骤，请访问以下文章了解有关体系结构和控件映射：

> [!div class="nextstepaction"]
> [ISO 27001 共享服务蓝图-概述](./index.md)
> [ISO 27001 共享服务蓝图-控件映射](./control-mapping.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。