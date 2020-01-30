---
title: 请求增加 Azure 区域 vCPU 配额限制
description: 如何请求增加 Azure 门户中某个区域的 vCPU 配额限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843678"
---
# <a name="standard-quota-increase-limits-by-region"></a>标准配额：按区域增加限制

Azure 资源管理器为虚拟机支持两种类型的 vCPU 配额：

* 即*用即付 vm*和*保留 vm 实例*受*标准 vCPU 配额*的限制。
* *污点 vm*服从*点 vCPU 配额*。

对于每个区域中的每个订阅，在两个级别上强制实施即用即付和保留虚拟机实例的标准 vCPU 配额：

* 第一层是所有 VM 系列的*区域个 vcpu 限制总计*。
* 第二层是*每个 VM 系列个 vcpu 限制*，如 D 系列个 vcpu。

每当部署新的点 VM 时，该 VM 系列的新的和现有 vCPU 使用情况不得超过该特定 VM 系列的已批准 vCPU 配额。 此外，在所有 VM 系列上部署的新的和现有的个 vcpu 的总数不应超过订阅的总批准区域 vCPU 配额。 如果超过了其中任一配额，则不允许进行 VM 部署。

可以通过使用 Azure 门户来请求提高 VM 系列的 vCPU 配额限制。 VM 序列配额增加会自动增加相同数量的区域 vCPU 限制。

创建新订阅时，默认的区域个 vcpu 总数可能不等于所有单个 VM 序列的总默认 vCPU 配额。 这种差异可能会导致订阅的配额足以满足每个要部署的单个 VM 系列的要求。 但可能没有足够的配额来容纳所有部署的区域个 vcpu。 在这种情况下，必须提交一个请求，以显式增加区域个 vcpu 的总数限制。 区域 vCPU 限制的总量不能超过区域的所有 VM 系列的总批准配额。

若要了解有关标准 vCPU 配额的详细信息，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)、 [Azure 订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

若要了解有关增加位置 VM vCPU 限制的详细信息，请参阅[污点配额：增加所有 VM 序列的限制](low-priority-quota.md)。

可以通过以下两种方式之一请求增加 vCPU 标准配额限制（按区域）。

## <a name="request-a-quota-increase-by-region-from-help--support"></a>请求从 "帮助 + 支持" 区域增加配额

若要从 "**帮助 + 支持**" 中请求按区域增加 vCPU 配额：

1. 从 " [Azure 门户](https://portal.azure.com)" 菜单中，选择 "**帮助 + 支持**"。

   !["帮助 + 支持" 链接](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 "**帮助 + 支持**" 中，选择 "**新建支持请求**"。

    ![新建支持请求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制(配额)”。

   ![选择问题类型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 对于 "**订阅**"，请选择要增加其配额的订阅。

   ![选择一个订阅](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 对于 "**配额类型**"，请选择 "**其他请求**"。

   ![选择配额类型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 选择 "**下一步：** 用于打开**问题详细信息**的解决方案"。 在 "**说明**" 中，提供以下信息：

    1. 对于**部署模型**，请指定**资源管理器**。  
    1. 对于 "**区域**"，请指定所需的区域，例如 "**美国东部 2**"。  
    1. 对于 "**新限制**"，请为区域指定新的 vCPU 限制。 此值不应超过此订阅的单个 SKU 系列的已批准配额的总和。

    ![输入配额请求的详细信息](./media/resource-manager-core-quotas-request/regional-details.png)

1. 选择 "**查看 + 创建**"，继续创建支持请求。

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>请求按区域从订阅增加配额

请求按区域从**订阅**增加 vCPU 配额：

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 "**订阅**"。

   ![在 Azure 门户中，请参阅订阅](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 选择要增加其配额的订阅。

   ![选择要修改的订阅](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左窗格中，选择 "**使用情况 + 配额**"。

   ![跟踪使用情况和配额链接](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角，选择 "**请求增加**"。

   ![选择以增加配额](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 从 "**配额类型**" 中选择 "**其他请求**"。

   ![选择配额类型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 选择 "**下一步：** 用于打开**问题详细信息**的解决方案"。 在 "**描述**" 框中提供以下附加信息：

    1. 对于**部署模型**，请指定**资源管理器**。  
    1. 对于 "**区域**"，请指定所需的区域，例如 "**美国东部 2**"。  
    1. 对于 "**新限制**"，请为区域指定新的 vCPU 限制。 此值不应超过此订阅的单个 SKU 系列的已批准配额的总和。

    ![在详细信息中输入信息](./media/resource-manager-core-quotas-request/regional-details.png)

1. 选择 "**查看 + 创建**"，继续创建支持请求。
