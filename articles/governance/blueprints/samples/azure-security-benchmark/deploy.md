---
title: 部署 Azure 安全基准蓝图示例
description: Azure 安全基准蓝图示例的部署步骤，包括蓝图项目参数详细信息。
ms.date: 05/01/2020
ms.topic: sample
ms.openlocfilehash: 0e967e2217b90e036d809ce7baf500c3bc791c6f
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82702668"
---
# <a name="deploy-the-azure-security-benchmark-blueprint-sample"></a>部署 Azure 安全基准蓝图示例

若要部署 Azure 蓝图 Azure 安全基准蓝图示例，必须执行以下步骤：

> [!div class="checklist"]
> - 基于示例创建新的蓝图
> - 将示例副本标记为“已发布” 
> - 将蓝图副本分配到现有的订阅

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-blueprint-from-sample"></a>基于示例创建蓝图

首先，通过使用示例作为起点在环境中创建新的蓝图，来实现蓝图示例。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”按钮。   

1. 在“其他示例”下找到“Azure 安全基准”蓝图示例，然后选择“单击名称以选择此示例”。  

1. 输入该蓝图示例的“基本信息”： 

   - **蓝图名称**：为你的 Azure 安全基准蓝图示例副本提供一个名称。
   - **定义位置**：使用省略号并选择要将示例副本保存到的管理组。

1. 选择页面顶部的“项目”选项卡，或页面底部的“下一步:   项目”。

1. 查看构成蓝图示例的项目列表。 许多项目包含稍后我们将要定义的参数。 查看完蓝图示例后，选择“保存草稿”。 

## <a name="publish-the-sample-copy"></a>发布示例副本

现已在环境中创建蓝图示例的副本。 该副本在创建后处于“草稿”模式，必须先将其**发布**，然后才能分配和部署它。  可根据环境和需求自定义蓝图示例的副本，但这种修改可能不符合 Azure 安全基准建议。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧选择“蓝图定义”页。  使用筛选器找到蓝图示例的副本，然后选择它。

1. 选择页面顶部的“发布蓝图”。  在右侧的新窗格中，提供蓝图示例副本的**版本**。 以后做出修改时，此属性非常有用。 提供“更改注释”  ，例如，“基于 Azure 安全基准蓝图示例发布的第一个版本”。 然后选择页面底部的“发布”。 

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

   - 项目参数

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../../concepts/parameters.md#dynamic-parameters) ，因为它们是在分配蓝图期间定义的。 有关完整列表或项目参数及其说明，请参阅[项目参数表](#artifact-parameters-table) 。

1. 输入所有参数后，选择页面底部的“分配”。  随后将创建蓝图分配，并开始部署项目。 部署过程大约需要一小时。 若要检查部署状态，请打开蓝图分配。

> [!WARNING]
> Azure 蓝图服务和内置蓝图示例是**免费的**。 Azure 资源[按产品定价](https://azure.microsoft.com/pricing/) 。 使用[定价计算器](https://azure.microsoft.com/pricing/calculator/) 可以估算运行此蓝图示例部署的资源所需的成本。

## <a name="artifact-parameters-table"></a>项目参数表

下表提供了蓝图项目参数的列表：

|项目名称|项目类型|参数名称|说明|
|-|-|-|-|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|
从 Windows VM 管理员组排除的用户的列表|以分号分隔的应从管理员本地组中排除的成员列表。 例如：管理员；myUser1；myUser2|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|必须包含在 Windows VM 管理员组中的用户的列表|以分号分隔的应包括在管理员本地组中的成员列表。 例如：管理员；myUser1；myUser2|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|Windows VM 管理员组只能包含的用户的列表 |以分号分隔的管理员本地组所有预期成员的列表。 例如：管理员；myUser1；myUser2|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|应启用网络观察程序的区域的列表|若要查看区域的完整列表，请使用 Get-AzLocation|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|应当连接 VM 的虚拟网络|示例：/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|虚拟网络应使用的网关|示例：/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|Log Analytics 代理应连接的工作区 ID 列表|Log Analytics 代理应连接到的工作区 ID 的列表（以分号分隔）|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|应启用诊断日志的资源类型列表|审核所选资源类型的诊断设置。|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|最新 PHP 版本|应用服务支持的最新 PHP 版本|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|最新 Java 版本|应用服务支持的最新 Java 版本|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|最新 Windows Python 版本|应用服务支持的最新 Python 版本|
|审核 Azure 安全基准建议，并部署特定的支持性 VM 扩展|策略分配|最新 Linux Python 版本|应用服务支持的最新 Python 版本|

## <a name="next-steps"></a>后续步骤

了解 Azure 安全基准蓝图示例的部署步骤后，请访问以下文章来了解蓝图和控制映射：

> [!div class="nextstepaction"]
> [Azure 安全基准蓝图 - 概述](./index.md)
> [Azure 安全基准蓝图 - 控制映射](./control-mapping.md)

有关蓝图和如何使用这些蓝图的更多文章：

- 了解[蓝图生命周期](../../concepts/lifecycle.md)。
- 了解如何使用[静态和动态参数](../../concepts/parameters.md)。
- 了解如何自定义[蓝图排序顺序](../../concepts/sequencing-order.md)。
- 了解如何利用[蓝图资源锁定](../../concepts/resource-locking.md)。
- 了解如何[更新现有分配](../../how-to/update-existing-assignments.md)。
