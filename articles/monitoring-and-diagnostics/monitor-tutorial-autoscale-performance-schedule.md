---
title: "基于性能数据或调度自动缩放 Azure 资源 | Microsoft 文档"
description: "使用指标数据和调度为应用服务计划创建自动缩放设置"
author: anirudhcavale
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: ancav
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 7e8d97657e03b0eaff76365d3988f51c773e3b55
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="create-an-autoscale-setting-for--azure-resources-based-on-performance-data-or-a-schedule"></a>基于性能数据或调度为 Azure 资源创建自动缩放设置 | Microsoft 文档

通过自动缩放设置，你可以根据预设的条件添加/删除服务实例。 这些设置可以通过门户进行创建。 此方法提供一个基于浏览器的用户界面，用于创建和配置自动缩放设置。 此教程将逐步介绍以下过程：

1. 创建应用服务
2. 配置自动缩放设置
3. 触发扩大操作
4. 触发缩小操作

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="log-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-web-app-and-app-service-plan"></a>创建 Web 应用和应用服务计划
1. 在左侧导航窗格单击“新建”选项
2. 搜索并选择“Web 应用”项，然后单击“创建”
3. 选择一个应用名称，例如 MyTestScaleWebApp。 新建资源组 *myResourceGroup，并将其放置在你选择的资源组中。
4. 几分钟内，应会预配你的资源。 在本教程的后面部分，我们将引用刚刚创建的 Web 应用及相对应的应用服务计划。

    ![在门户中新建应用服务](./media/monitor-tutorial-autoscale-performance-schedule/Web-App-Create.png)

## <a name="navigate-to-autoscale-settings"></a>导航到自动缩放设置
1. 在左侧导航窗格选择“监视”选项。 页面加载后，选择“自动缩放”选项卡。
2. 此处将列出你的订阅下支持自动缩放的资源列表。 找出本教程前面部分创建的应用服务计划，并单击它。

    ![导航到自动缩放设置](./media/monitor-tutorial-autoscale-performance-schedule/monitor-blade-autoscale.png)

3. 在自动缩放设置上，单击“启用自动缩放”按钮

接下来的几个步骤将帮助你填充自动缩放屏幕，应如下图所示：

   ![保存自动缩放设置](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

 ## <a name="configure-default-profile"></a>配置默认配置文件
1. 输入自动缩放设置的“名称”
2. 在默认配置文件中，确保已将“缩放模式”设置为“缩放为具体实例数”
3. 将实例计数设置为 1。 此设置将确保在没有其他配置文件处于活动状态或有效状态时，默认配置文件将实例计数返回到 1。

  ![导航到自动缩放设置](./media/monitor-tutorial-autoscale-performance-schedule/autoscale-setting-profile.png)


## <a name="create-recurrence-profile"></a>创建重复配置文件

1. 在默认配置文件下，单击“添加缩放条件”链接

2. 将此配置文件的“名称”编辑为“星期一到星期五的配置文件”

3. 确保已将“缩放模式”设置为“基于指标缩放”

4. 对于“实例限制”，将“最小值”设置为“1”，将“最大值”设置为“2”，将“默认值”设置为“1”。 这将确保此配置文件不会将服务计划自动缩放为拥有的实例数少于 1 个或多于 2 个。 如果此配置文件没有足够的数据来用于作出决策，它将使用默认的实例数（在此示例中为 1）。

5. 对于“调度”，选择“重复特定日期”

6. 将此配置文件设置为从星期一到星期五的上午九点到下午六点 (PST) 重复。 这将确保此配置文件仅在星期一到星期五的上午九点到下午六点适用并处于活动状态。 在所有其他时间段，“默认”配置文件是自动缩放设置使用的配置文件。

## <a name="create-a-scale-out-rule"></a>创建扩大规则

1. 在“星期一到星期五的配置文件”中

2. 单击“添加规则”链接

3. 将“指标源”设置为“其他资源”。 将“资源类型”设置为“应用服务”，并将“资源”设置为本教程前面部分创建的 Web 应用。

4. 将“时间聚合”设置为“总计”，将“指标名称”设置为“请求”，并将“时间粒度统计信息”设置为“合计”

5. 将“运算符”设置为“大于”，将“阈值”设置为“10”，并将“持续时间”设置为“5”分钟。

6. 为“操作”选择“增加计数”，将“实例计数”设置为“1”，并将“冷却时间”设置为“5”分钟

7. 单击“添加”按钮

此规则将确保当你的 Web 应用在 5 分钟或更短时间内收到的请求超过 10 个时，将向你的应用服务计划再添加一个实例，以托管负载。

   ![创建扩大规则](./media/monitor-tutorial-autoscale-performance-schedule/Scale-Out-Rule.png)

## <a name="create-a-scale-in-rule"></a>创建缩小规则
建议始终要创建一个缩小规则，以作为扩大规则的伴随项。 拥有这两个规则将确保预配的资源不会过多。 预配过多表示运行的实例数多于处理当前负载所需的实例数。 

1. 在“星期一到星期五的配置文件”中

2. 单击“添加规则”链接

3. 将“指标源”设置为“其他资源”。 将“资源类型”设置为“应用服务”，并将“资源”设置为本教程前面部分创建的 Web 应用。

4. 将“时间聚合”设置为“总计”，将“指标名称”设置为“请求”，并将“时间粒度统计信息”设置为“平均值”

5. 将“运算符”设置为“小于”，将“阈值”设置为“5”，并将“持续时间”设置为“5”分钟。

6. 为“操作”选择“减少计数”，将“实例计数”设置为“1”，并将“冷却时间”设置为“5”分钟

7. 单击“添加”按钮

    ![创建缩小规则](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Rule.png)

8. “保存”自动缩放设置

    ![保存自动缩放设置](./media/monitor-tutorial-autoscale-performance-schedule/Autoscale-Setting-Save.png)

## <a name="trigger-scale-out-action"></a>触发扩大操作
若要触发刚刚创建的自动缩放设置中的扩大条件，Web 应用在 5 分钟内收到的请求数必须超过 10 个。

1. 打开浏览器窗口，并导航到本教程前面部分创建的 Web 应用。 通过导航到你的 Web 应用资源并单击“概述”选项卡中的“浏览”按钮，可以在 Azure 门户中找到你的 Web 应用的 URL。

2. 连续重载页面 10 次以上

3. 在左侧导航窗格选择“监视”选项。 页面加载后，选择“自动缩放”选项卡。

4. 在列表中，选择本教程中使用的应用服务计划

5. 在自动缩放设置上，单击“运行历史记录”选项卡

6. 你将看到一个图表，其中会按照时间的推移反映应用服务计划的实例计数

7. 几分钟后，实例计数应从 1 增加到 2。

8. 在此图表下，你将看到该自动缩放设置执行的每个缩放操作的活动日志条目。

## <a name="trigger-scale-in-action"></a>触发缩小操作
如果在 10 分钟内向 Web 应用发送的请求数少于 5 个，则将触发自动缩放设置中的缩小条件。 

1. 确保没有向 Web 应用发送请求

2. 加载 Azure 门户

3. 在左侧导航窗格选择“监视”选项。 页面加载后，选择“自动缩放”选项卡。

4. 在列表中，选择本教程中使用的应用服务计划

5. 在自动缩放设置上，单击“运行历史记录”选项卡

6. 你将看到一个图表，它会按照时间的推移反映应用服务计划的实例计数。

7. 几分钟后，实例计数应从 2 下降到 1。 此过程至少需要 10 分钟。  

8. 在此图表下是该自动缩放设置执行的每个缩放操作对应的活动日志条目集

    ![查看缩小操作](./media/monitor-tutorial-autoscale-performance-schedule/Scale-In-Chart.png)

## <a name="clean-up-resources"></a>清理资源

1. 在 Azure 门户的左侧菜单中，单击“所有资源”，然后选择本教程创建的 Web 应用。

2. 在资源页上单击“删除”，通过在文本框中键入 yes 来确认删除，然后单击“删除”。

3. 然后选择应用服务计划资源，并单击“删除”

4. 通过在文本框中键入 yes 来确认删除，然后单击“删除”。

## <a name="next-steps"></a>后续步骤

本教程创建了简单的 Web 应用和应用服务计划。 然后创建了自动缩放设置，它将根据 Web 应用收到的请求数缩放应用服务计划。 若要了解有关自动缩放设置的详细信息，请继续查看自动缩放概述。

> [!div class="nextstepaction"]
> [存档监视数据](./monitor-tutorial-archive-monitoring-data.md)

