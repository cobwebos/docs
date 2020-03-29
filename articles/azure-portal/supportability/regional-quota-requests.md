---
title: 请求增加 Azure 区域 vCPU 配额限制
description: 如何请求提高 Azure 门户中区域的 vCPU 配额限制。
author: sowmyavenkat86
ms.author: svenkat
ms.date: 01/27/2020
ms.topic: article
ms.service: azure-supportability
ms.assetid: ce37c848-ddd9-46ab-978e-6a1445728a3b
ms.openlocfilehash: 9236989f7bbb4695db24f86a2a049f5bb58f302a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "76843678"
---
# <a name="standard-quota-increase-limits-by-region"></a>标准配额：按区域增加限制

Azure 资源管理器支持虚拟机的两种类型的 vCPU 配额：

* *即用即付 VM*和保留*VM 实例*受*标准 vCPU 配额*的约束。
* *现货 VM*受点*vCPU 配额的约束*。

即用即付和保留虚拟机实例的标准 vCPU 配额在每个区域中的每个订阅的两层执行：

* 第一层是所有 VM 系列*的区域 vCPU 限制*。
* 第二层是*每个 VM 系列 vCPU 限制*，如 D 系列 vCPU。

每当部署新的 spot VM 时，该 VM 系列的新 vCPU 和现有 vCPU 总使用量不得超过该特定 VM 系列的已批准的 vCPU 配额。 此外，在所有 VM 系列中部署的新 vCPU 和现有 vCPU 的总数不应超过订阅的已批准区域 vCPU 配额总数。 如果超过这些配额之一，则不允许 VM 部署。

可以使用 Azure 门户请求提高 VM 系列的 vCPU 配额限制。 VM 系列配额的增加会自动将区域 vCPU 总限制增加相同的数量。

创建新订阅时，默认的区域 vCPU 总数可能不等于所有单个 VM 系列的默认 vCPU 配额总数。 此差异可能导致订阅具有足够的配额，以便部署每个单独的 VM 系列。 但是，可能没有足够的配额来容纳所有部署的总区域 vCPU。 在这种情况下，您必须提交请求，以显式增加区域 vCPU 总数的限制。 区域 vCPU 总限制不能超过该区域所有 VM 系列中批准的总配额。

要了解有关标准 vCPU 配额的更多，请参阅[虚拟机 vCPU 配额](../../virtual-machines/windows/quotas.md)以及 Azure[订阅和服务限制、配额和约束](../../azure-resource-manager/management/azure-subscription-service-limits.md)。

要了解有关增加 spot VM vCPU 限制的更多，请参阅[Spot 配额：增加所有 VM 系列的限制](low-priority-quota.md)。

您可以通过两种方式之一请求按区域增加 vCPU 标准配额限制。

## <a name="request-a-quota-increase-by-region-from-help--support"></a>请求从帮助和支持按区域增加配额

请求按区域从**帮助 + 支持**增加 vCPU 配额：

1. 在[Azure 门户](https://portal.azure.com)菜单中，选择 **"帮助 + 支持**"。

   !["帮助 + 支持"链接](./media/resource-manager-core-quotas-request/help-plus-support.png)

1. 在 **"帮助+支持"** 中，选择 **"新建支持请求**"。

    ![新建支持请求](./media/resource-manager-core-quotas-request/new-support-request.png)

1. 对于“问题类型”，选择“服务和订阅限制(配额)”********。

   ![选择问题类型](./media/resource-manager-core-quotas-request/select-quota-issue-type.png)

1. 对于**订阅**，选择要增加其配额的订阅。

   ![选择一个订阅](./media/resource-manager-core-quotas-request/select-subscription-support-request.png)

1. 对于**配额类型**，请选择 **"其他请求**"。

   ![选择配额类型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 选择 **"下一步"：** 打开**问题详细信息的解决方案**。 在**说明**中，提供以下信息：

    1. 对于**部署模型**，指定**资源管理器**。  
    1. 对于**区域**，指定所需的区域，例如，**东美国 2**。  
    1. 对于 **"新限制"，** 为区域指定新的 vCPU 限制。 此值不应超过此订阅的各个 SKU 系列的已批准配额的总和。

    ![输入配额请求的详细信息](./media/resource-manager-core-quotas-request/regional-details.png)

1. 选择 **"审阅 + 创建**"以继续创建支持请求。

## <a name="request-a-quota-increase-by-region-from-subscriptions"></a>请求按订阅按区域增加配额

要请求按订阅区域增加 vCPU**配额**：

1. 在[Azure 门户](https://portal.azure.com)中，搜索并选择 **"订阅**"。

   ![转到 Azure 门户中的订阅](./media/resource-manager-core-quotas-request/search-for-subscriptions.png)

1. 选择要增加其配额的订阅。

   ![选择要修改的订阅](./media/resource-manager-core-quotas-request/select-subscription-change-quota.png)

1. 在左侧窗格中，选择 **"使用情况 + 配额**"。

   ![关注使用情况和配额链接](./media/resource-manager-core-quotas-request/select-usage-plus-quotas.png)

1. 在右上角，选择 **"请求增加**"。

   ![选择增加配额](./media/resource-manager-core-quotas-request/request-increase-from-subscription.png)

1. 从**配额类型**中，选择 **"其他请求**"。

   ![选择配额类型](./media/resource-manager-core-quotas-request/regional-quotatype.png)

1. 选择 **"下一步"：** 打开**问题详细信息的解决方案**。 在 **"描述"** 框中，提供以下附加信息：

    1. 对于**部署模型**，指定**资源管理器**。  
    1. 对于**区域**，指定所需的区域，例如，**东美国 2**。  
    1. 对于 **"新限制"，** 为区域指定新的 vCPU 限制。 此值不应超过此订阅的各个 SKU 系列的已批准配额的总和。

    ![输入详细信息](./media/resource-manager-core-quotas-request/regional-details.png)

1. 选择 **"审阅 + 创建**"以继续创建支持请求。
