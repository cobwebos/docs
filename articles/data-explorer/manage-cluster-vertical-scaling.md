---
title: 根据不断变化的需求纵向扩展 Azure 数据资源管理器群集
description: 本文介绍根据不断变化的需求对 Azure 数据资源管理器群集进行纵向扩展和纵向缩减的步骤。
author: radennis
ms.author: radennis
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/30/2019
ms.openlocfilehash: dc9ca8bb592e699d19835efeafb91e81408ae297
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571525"
---
# <a name="manage-cluster-scale-up-to-accommodate-changing-demand"></a>管理群集纵向扩展以适应不断变化的需求

有两个工作流进行缩放的 Azure 数据资源管理器群集：
1. [水平缩放](manage-cluster-horizontal-scaling.md)，也称为扩大和缩小。
2. 垂直缩放，也称为向上和向下缩放。

本文介绍如何管理群集垂直缩放。

调整好群集的大小对于 Azure 数据资源管理器的性能至关重要。 但是，对群集的需求不能绝对准确地进行预测。 静态群集大小可能导致利用不充分或利用过度，二者都不理想。 更好的方法是对群集进行*缩放*，根据不断变化的需求来添加或删除容量和 CPU 资源。 

## <a name="steps-to-configure-vertical-scaling"></a>配置垂直缩放的步骤

1. 转到你的群集。 在“设置”下选择“纵向扩展”。  

    此时会显示可用 SKU 的列表。 例如，在下图中，只有四个 SKU 可用。

    ![纵向扩展](media/manage-cluster-vertical-scaling/scale-up.png)

    SKU 被禁用是因为它们是当前的 SKU，或者在群集所在区域不可用。

1. 若要更改 SKU，请选择想要的 SKU，然后选择“选择”按钮  。

> [!NOTE]
> 垂直缩放过程可能需要几分钟，并且在该时间段会挂起你的群集。 请注意，缩减可能会损害群集性能。

现在已为 Azure 数据资源管理器群集执行了纵向扩展或纵向缩减操作。

如果在解决群集缩放问题时需要帮助，请在 Azure 门户中[提交支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

## <a name="next-steps"></a>后续步骤

* [管理群集横向缩放](manage-cluster-horizontal-scaling.md)动态扩展实例数基于你指定的度量值的扩展。

* 按下文说明监视资源使用情况：[通过指标监视 Azure 数据资源管理器的性能、运行状况和使用情况](using-metrics.md)。

