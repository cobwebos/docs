---
title: 升级 Log Analytics 仪表板可视化效果
description: 可以从启动查询，并根据需要进行修改
ms.subservice: logs
ms.topic: article
author: rboucher
ms.author: robb
ms.date: 07/01/2020
ms.openlocfilehash: 6fb9f691afc8874b19ca4575408027357f07cc89
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85833530"
---
# <a name="upgrading-your-log-analytics-dashboard-visualizations"></a>升级 Log Analytics 仪表板可视化效果

2020年2月，我们引入了改进的可视化技术。 提高和增强你的可视化查询结果的可视化能力，并快速获得强大的见解。 

有关此升级的详细信息，请参阅此[Azure 更新](https://azure.microsoft.com/updates/azure-monitor-log-analytics-upgraded-results-visualization/)。 

这一新的可视化技术存在了新的和改进的查询结果集体验。 

## <a name="dashboards-in-azure"></a>Azure 中的仪表板

Azure 仪表板是一种可视化整个 Azure surface 区域的状态的方法。 它们旨在为你的 Azure 房地产状态提供单个窗格，并允许使用多种快捷方式执行常见操作。 

有关详细信息，请参阅[Azure 仪表板](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)


## <a name="upgrading-log-analytics-dashboard-parts"></a>升级 Log Analytics 面板部件

新的可视化技术解决了旧实现的一些常见问题，并引入了一些新的功能来固定 Log Analytics 部分： 

- **相同的可用类型**-在 "仪表板" 上，Log Analytics 中的所有可视化效果类型都作为固定部分提供。

- **一致的外观**-对于固定的部分，可视化效果外观与 Log Analytics 中的外观几乎相同。 不同之处在于优化要求视觉对象的数据内容之间的差异。 若要深入了解这些差异，请参阅本文档的注意事项部分。

- **工具提示和标签**-新固定 Log Analytics 可视化效果支持工具提示。 饼图和圆环图现在支持标签。

- **交互式图例**–单击可视化效果图例可在 Log Analytics 中添加/删除固定视觉对象中的维度。

## <a name="stage-1---opt-in-upgrade-message"></a>阶段 1-选择加入升级消息

当可以升级 Log Analytics 固定部件时，将在仪表板中 Log Analytics 固定部分显示新的*选择加入*通知，以允许用户升级其视觉对象。 如果要体验新的可视化效果，以便在其仪表板中升级选定的可视化效果。

 
![边栏](media/dashboard-upgrade/update-message-1.png)
 
![边栏](media/dashboard-upgrade/update-message-2.png)

> [!WARNING]
> 发布仪表板后，升级是不可逆的。 但是，如果离开仪表板而不重新发布，则会放弃更改。  

单击后，可视化将更新为新技术。 可视化效果中的细微差异可能发生在 Log Analytics 的外观上。

升级可视化效果后，需要重新发布仪表板以使更改生效。

![边栏](media/dashboard-upgrade/update-message-3.png)

## <a name="stage-2---migration-of-all-dashboards"></a>阶段 2-迁移所有仪表板

初始选择加入期结束后，Log Analytics 团队将升级系统中的所有仪表板。 通过协调所有 Azure 仪表板，团队可以在整个板中引入更多可视化效果和体验改进。

## <a name="considerations"></a>注意事项

固定到仪表板的 Log Analytics 可视化效果是设计为获得最佳体验的特定行为。 将可视化效果固定到仪表板时，请查看以下设计注意事项。

### <a name="query-time-scope---30-day-limit"></a>查询时间范围-30 天限制

由于仪表板可能包含多个查询的多个可视化效果，因此单个固定查询的时间范围限制为30天。 单个查询只能在小于或等于30天的时间范围内运行。 此限制旨在确保面板加载时间合理。

### <a name="query-data-values---25-values-and-other-grouping"></a>查询数据值-25 个值和其他分组

仪表板可以更丰富且复杂。 为了减少查看仪表板时的认知负载，我们通过将显示限制为25个不同的数据类型来优化可视化。 如果有25个以上的，Log Analytics 会优化数据。 它单独显示25种类型，其中的大多数数据是单独的，然后将其余值分组为 "其他" 值。 下图显示了这种情况。  

![边栏](media/dashboard-upgrade/values-25-limit.png)

### <a name="dashboard-refresh-on-load"></a>负载上的仪表板刷新

加载时，将刷新仪表板。 将执行与仪表板固定 Log Analytics 可视化对象相关的所有查询，并在加载后刷新仪表板。 如果 "仪表板" 页保持打开状态，则仪表板中的数据每60分钟刷新一次。

## <a name="next-steps"></a>后续步骤

[在 Log Analytics 中创建和共享仪表板](../learn/tutorial-logs-dashboards.md)
