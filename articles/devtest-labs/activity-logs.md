---
title: Azure 开发测试实验室中的活动日志 |Microsoft Docs
description: 本文介绍了查看 Azure 开发测试实验室的活动日志的步骤。
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: 24b5eb25f1c8c9044ad6e77545381a43a11714f9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87095913"
---
# <a name="view-activity-logs-for-labs-in-azure-devtest-labs"></a>查看 Azure 开发测试实验室中实验室的活动日志 
创建一个或多个实验室后，你可能想要监视实验室的访问方式和时间，以及更改者。 Azure 开发测试实验室使用 Azure Monitor （具体为 **活动日志**）为这些操作提供有关实验室的信息。 

本文介绍如何在 Azure 开发测试实验室中查看实验室的活动日志。

## <a name="view-activity-log-for-a-lab"></a>查看实验室的活动日志

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择“所有服务”****，然后在“DEVOPS”**** 部分中选择“开发测试实验室”****。 如果在“DEVOPS”**** 部分中选择了“开发测试实验室”**** 旁边的 *（星号）， 此操作会将“开发测试实验室”**** 添加到左侧导航菜单中，以便你下次可以轻松访问它。 然后，可以在左侧导航菜单中选择“开发测试实验室”****。

    ![所有服务 - 选择“开发测试实验室”](./media/devtest-lab-create-lab/all-services-select.png)
1. 在实验室列表中，选择实验室。
1. 在实验室的 "主页" 页上，选择左侧菜单上的 " **配置和策略** "。 

    :::image type="content" source="./media/activity-logs/configuration-policies-link.png" alt-text="选择左侧菜单中的 &quot;配置和策略&quot;&quot;:::
1. 在 &quot;**配置和策略**&quot; 页上，在 &quot;**管理**" 下的左侧菜单中选择 "**活动日志**"。 应会看到针对实验室完成的操作的条目。 

    :::image type="content" source="./media/activity-logs/activity-log.png" alt-text="选择左侧菜单中的 &quot;配置和策略&quot;&quot;:::
1. 在 &quot;**配置和策略**&quot; 页上，在 &quot;**管理**" 等信息，以及执行操作的人员。 
    
    :::image type="content" source="./media/activity-logs/stop-vm-event.png" alt-text="选择左侧菜单中的 &quot;配置和策略&quot;&quot;:::
1. 在 &quot;**配置和策略**&quot; 页上，在 &quot;**管理**" 选项卡以查看更多详细信息。 在下面的示例中，可以看到 VM 的名称以及在 VM 上完成的操作 (停止) 。

    :::image type="content" source="./media/activity-logs/stop-vm-event-json.png" alt-text="选择左侧菜单中的 &quot;配置和策略&quot;&quot;:::
1. 在 &quot;**配置和策略**&quot; 页上，在 &quot;**管理**" 选项卡以查看更改的历史记录。 在下面的示例中，可以看到已在 VM 上进行的更改。 

    :::image type="content" source="./media/activity-logs/change-history.png" alt-text="选择左侧菜单中的 &quot;配置和策略&quot;&quot;:::
1. 在 &quot;**配置和策略**&quot; 页上，在 &quot;**管理**":::             
1. 选择更改历史记录列表中的更改，查看有关更改的更多详细信息。 

    :::image type="content" source="./media/activity-logs/change-details.png" alt-text="选择左侧菜单中的 &quot;配置和策略&quot;&quot;:::
1. 在 &quot;**配置和策略**&quot; 页上，在 &quot;**管理**":::             

有关活动日志的详细信息，请参阅 [Azure 活动日志](../azure-monitor/platform/activity-log.md)。

## <a name="next-steps"></a>后续步骤

- 若要了解有关如何设置活动日志 **警报** 的信息，请参阅 [创建警报](create-alerts.md)。
- 若要了解有关活动日志的详细信息，请参阅  [Azure 活动日志](../azure-monitor/platform/activity-log.md)。

