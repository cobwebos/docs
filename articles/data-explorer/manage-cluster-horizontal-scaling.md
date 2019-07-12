---
title: 横向扩展 Azure 数据资源管理器群集
description: 本文介绍如何根据不断变化的需求采用相关步骤对 Azure 数据资源管理器群集进行横向缩放。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: 29bfcc42462a667850f0b2e1bbda3d29cd1597ab
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571512"
---
# <a name="manage-cluster-horizontal-scaling-to-accommodate-changing-demand"></a>管理群集横向缩放以适应不断变化的需求

调整好群集的大小对于 Azure 数据资源管理器的性能至关重要。 但是，对群集的需求不能绝对准确地进行预测。 静态群集大小可能导致利用不充分或利用过度，二者都不理想。

更好的方法是对群集进行缩放，根据不断变化的需求来添加或删除容量。  有两个工作流进行缩放： 
* 水平缩放，也称为扩大和缩小。
* 垂直缩放，也称为向上和向下缩放。

本文介绍水平缩放工作流。

水平缩放，可自动根据预定义的规则和计划实例计数。 在 Azure 门户中指定群集的自动缩放设置，如本文所述。

## <a name="steps-to-configure-horizontal-scaling"></a>配置水平缩放的步骤

在 Azure 门户中，转到数据资源管理器群集资源。 在“设置”标题下，选择“横向扩展”   。 

选择所需的自动缩放方法：**手动缩放**，**优化自动缩放**或**自定义自动缩放**。

### <a name="manual-scale"></a>手动缩放

手动缩放为默认设置，其中创建群集。 这意味着群集具有静态群集容量，不会自动更改。 可以选择使用栏的静态容量并且不会更改直到下一次将更改群集的横向扩展设置。

   ![手动缩放方法](media/manage-cluster-horizontal-scaling/manual-scale-method.png)

### <a name="optimized-autoscale"></a>优化的自动缩放

优化的自动缩放是建议的自动缩放方法。 若要配置优化自动缩放的步骤：

1. 选择已进行优化的自动缩放选项和选择较低的限制和群集的实例的量的上限，将这些限制之间进行自动缩放。
2. 单击“保存”。

   ![优化的自动缩放方法](media/manage-cluster-horizontal-scaling/optimized-autoscale-method.png)

后单击保存优化自动缩放机制将开始工作，操作会显示群集的活动日志中。 此自动缩放方法优化群集性能和成本： 如果群集将启动进入它将会向外接程序这样的相同和较低的成本，使性能的使用不足的状态，并且群集将启动进入的状态使用过度，它将是向外扩展以确保其运行良好

### <a name="custom-autoscale"></a>自定义自动缩放

自定义自动缩放方法允许你缩放群集动态地基于你指定的指标。 下图显示的流和配置自定义自动缩放的步骤。 图后面提供了更多详细信息。

1. 在“自动缩放设置名称”框中提供一个名称，例如“横向扩展: 缓存使用率”。   

   ![缩放规则](media/manage-cluster-horizontal-scaling/custom-autoscale-method.png)

2. 至于“缩放模式”  ，请选择“基于指标缩放”。  此模式提供动态缩放。 也可选择“缩放为具体实例数”。 

3. 选择“+ 添加规则”  。

4. 在右侧的“缩放规则”部分，提供每项设置的值。 

    **条件**

    | 设置 | 说明和值 |
    | --- | --- |
    | **时间聚合** | 选择聚合条件，例如“平均”。  |
    | **指标名称** | 选择要求缩放操作依赖的指标，例如“缓存使用率”。  |
    | 时间粒度统计信息  | 在 **Average**、**Minimum**、**Maximum** 和 **Sum** 之间选择。 |
    | **“运算符”** | 选择适当的选项，例如“大于或等于”。  |
    | **阈值** | 选择适当的值。 例如，对于缓存使用率，一开始可以选择 80%。 |
    | **持续时间（分钟）** | 选择适当的时间值，以便系统在计算指标时进行回溯。 一开始可将默认值设置为 10 分钟。 |
    |  |  |

    **Action**

    | 设置 | 说明和值 |
    | --- | --- |
    | **操作** | 选择进行横向缩放的适当选项。 |
    | **实例计数** | 选择在符合指标条件的情况下，需要添加或删除的节点或实例的数目。 |
    | 冷却（分钟）  | 选择需要在两项缩放操作之间等待的适当时间间隔。 一开始可将默认值设置为 5分钟。 |
    |  |  |

5. 选择 **添加** 。

6. 在左侧的“实例限制”部分，提供每项设置的值。 

    | 设置 | 说明和值 |
    | --- | --- |
    | **最低** | 群集在缩放时不管使用率如何都不得低于的实例数。 |
    |  最大值 | 群集在缩放时不管使用率如何都不得高于的实例数。 |
    | **默认** | 默认实例数。 在出现资源指标读取问题时使用此设置。 |
    |  |  |

7. 选择**保存**。

现在已为 Azure 数据资源管理器群集配置了横向扩展操作。 添加另一条适用于横向缩减操作的规则。 如果在解决群集缩放问题时需要帮助，请在 Azure 门户中[提交支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="next-steps"></a>后续步骤

* [通过指标监视 Azure 数据资源管理器的性能、运行状况和使用情况](using-metrics.md)
* [管理群集垂直缩放](manage-cluster-vertical-scaling.md)适当调整大小的群集。
