---
title: 配置 Azure API 管理实例的自动缩放 | Microsoft Docs
description: 本主题介绍如何设置 Azure API 管理实例的自动缩放行为。
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: a01e50debf11daf2f1163a56726f5574f7e3e379
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/28/2018
ms.locfileid: "52444796"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>自动缩放 Azure API 管理实例  

Azure API 管理服务实例可以根据一组规则自动缩放。 此行为可以通过 Azure Monitor 启用和配置，仅在 Azure API 管理服务的**标准**和**高级**层中受支持。

本文逐步讲解配置自动缩放的过程，并建议自动缩放规则的最佳配置。

## <a name="prerequisites"></a>先决条件

若要执行本文中的步骤，必须：

+ 拥有一个有效的 Azure 订阅。
+ 有一个 Azure API 管理实例。 有关详细信息，请参阅[创建 Azure API 管理实例](get-started-create-service-instance.md)。
+ 了解 [Azure API 管理实例容量](api-management-capacity.md)的概念。
+ 了解 [Azure API 管理实例的手动缩放过程](upgrade-and-scale.md)，包括成本影响。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Azure API 管理自动缩放的限制

在配置自动缩放行为之前，需要考虑到缩放存在的某些限制和造成的后果。

+ 只能为 Azure API 管理服务的**标准**和**高级**层启用自动缩放。
+ 定价层还指定了服务实例的最大单元数。
+ 缩放过程至少需要 20 分钟。
+ 如果服务被另一操作锁定，则缩放请求将会失败并自动重试。
+ 对于使用多区域部署的服务，只能缩放**主要位置**中的单元。 不能缩放其他位置中的单元。

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>为 Azure API 管理服务启用和配置自动缩放

遵循以下步骤为 Azure API 管理服务配置自动缩放：

1. 在 Azure 门户中导航到“Monitor”实例。

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. 在左侧菜单中选择“自动缩放”。

    ![Azure Monitor - 自动缩放 - 资源](media/api-management-howto-autoscale/02.png)

3. 基于下拉菜单中的筛选器找到自己的 Azure API 管理服务。
4. 选择所需的 Azure API 管理服务实例。
5. 在新打开的部分，单击“启用自动缩放”按钮。

    ![Azure Monitor - 自动缩放 - 启用](media/api-management-howto-autoscale/03.png)

6. 在“规则”部分，单击“+ 添加规则”。

    ![Azure Monitor - 自动缩放 - 添加规则](media/api-management-howto-autoscale/04.png)

7. 定义新的横向扩展规则。

   例如，当过去 30 分钟的平均容量指标超过 80% 时，横向扩展规则可以触发添加 Azure API 管理单元的操作。 下表提供了此类规则的配置。

    | 参数             | 值             | 说明                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 指标源         | 当前资源  | 基于当前的 Azure API 管理资源指标定义规则。                                                                                                                                                                                                     |
    | *条件*            |                   |                                                                                                                                                                                                                                                                                 |
    | 时间聚合      | 平均值           |                                                                                                                                                                                                                                                                                 |
    | 指标名称           | 容量          | 容量指标是一个 Azure API 管理标准，用于反映 Azure API 管理实例的资源用量。                                                                                                                                                            |
    | 时间粒度统计信息  | 平均值           |                                                                                                                                                                                                                                                                                 |
    | 运算符              | 大于      |                                                                                                                                                                                                                                                                                 |
    | 阈值             | 80%               | 平均容量指标的阈值。                                                                                                                                                                                                                                 |
    | 持续时间(分钟) | 30                | 要计算平均值的容量指标的时间跨度特定于使用模式。 该时间段越长，做出的反应就越流畅 - 间歇性高峰对横向扩展决策的影响较小。 但是，它还会延迟横向扩展触发器。 |
    | *Action*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operation             | 增加计数 |                                                                                                                                                                                                                                                                                 |
    | 实例计数        | 1                 | 以 1 个单位为增量横向扩展 Azure API 管理实例。                                                                                                                                                                                                                          |
    | 冷却(分钟)   | 60                | 横向扩展 Azure API 管理服务至少需要 20 分钟。在大多数情况下，60 分钟冷却期可以防止触发许多的横向扩展操作。                                                                                                  |

8. 单击“添加”保存规则。

    ![Azure Monitor - 横向扩展规则](media/api-management-howto-autoscale/05.png)

9. 再次单击“+ 添加规则”。

    这次需要定义横向缩减规则。 这可以确保在 API 用量减少时不会浪费资源。

10. 定义新的横向缩减规则。

    例如，当过去 30 分钟的平均容量指标低于 35% 时，横向缩减规则可以触发删除 Azure API 管理单元的操作。 下表提供了此类规则的配置。

    | 参数             | 值             | 说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | 指标源         | 当前资源  | 基于当前的 Azure API 管理资源指标定义规则。                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *条件*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 时间聚合      | 平均值           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 指标名称           | 容量          | 横向扩展规则所用的相同指标。                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | 时间粒度统计信息  | 平均值           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 运算符              | 小于         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | 阈值             | 35%               | 与横向扩展规则类似，此值在很大程度取决于 Azure API 管理的使用模式。 |
    | 持续时间(分钟) | 30                | 横向扩展规则所用的相同值。                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Action*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operation             | 减少计数 | 与横向扩展规则使用的值相反。                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | 实例计数        | 1                 | 横向扩展规则所用的相同值。                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | 冷却(分钟)   | 90                | 横向缩减应该比横向扩展更保守，因此冷却期应该更长。                                                                                                                                                                                                                                                                                                                                                                                                    |

11. 单击“添加”保存规则。

    ![Azure Monitor - 横向缩减规则](media/api-management-howto-autoscale/06.png)

12. 设置 Azure API 管理单元的**最大**数目。

    > [!NOTE]
    > Azure API 管理会限制实例可以横向扩展到单元数。 此限制取决于服务层。

    ![Azure Monitor - 横向缩减规则](media/api-management-howto-autoscale/07.png)

13. 单击“ **保存**”。 现已配置自动缩放。

## <a name="next-steps"></a>后续步骤

+ [如何将 Azure API 管理服务实例部署到多个 Azure 区域](api-management-howto-deploy-multi-region.md)
