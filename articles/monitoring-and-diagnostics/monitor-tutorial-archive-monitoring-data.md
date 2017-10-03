---
title: "Azure 监视数据存档 | Microsoft Docs"
description: "将 Azure 内生成的日志和指标数据存档到存储帐户。"
author: johnkemnetz
manager: orenr
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.service: monitoring-and-diagnostics
ms.topic: tutorial
ms.date: 09/25/2017
ms.author: johnkem
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 445901a740920a74f259aaa9c6b862680c1c807e
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="archive-azure-monitoring-data"></a>Azure 监视数据存档

Azure 环境的很多层都会生成可存档到 Azure 存储帐户的日志和指标数据。 Log Analytics 或 Azure Monitor 中的数据超过保留期之后，你可能需要执行此操作，在不可搜索的低成本存储空间中保留某时间段内监视数据的历史记录。 本教程逐步介绍如何配置 Azure 环境以将数据存档到存储帐户。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com/)。

## <a name="create-a-storage-account"></a>创建存储帐户

首先，需要设置要存档监视数据的存储帐户。 为此，请[按照此处的步骤操作](../storage/common/storage-create-storage-account.md)。

## <a name="route-subscription-logs-to-the-storage-account"></a>将订阅日志路由到存储帐户

现可开始设置 Azure 环境，以便将监视数据路由到存储帐户。 首先配置要路由到存储帐户的订阅级数据（包含在 Azure 活动日志中）。 [**Azure 活动日志**](monitoring-overview-activity-logs.md)提供 Azure 中的订阅级事件的历史记录。 可在 Azure 门户中浏览该日志来确定由谁在何时创建、更新或删除了哪些资源。

1. 单击左侧导航列表中的“监视器”按钮，然后单击“活动日志”。

   ![活动日志部分](media/monitor-tutorial-archive-monitoring-data/activity-log-home.png)

2. 在显示的活动日志部分，单击“导出”按钮。

3. 在显示的“导出活动日志”部分，选中“导出到存储帐户”框，然后单击“选择存储帐户”。

   ![活动日志导出](media/monitor-tutorial-archive-monitoring-data/activity-log-export.png)

4. 在显示的部分中，使用“存储帐户”下拉列表为先前在“创建存储帐户”步骤中创建的存储帐户选择名称，然后单击“确定”。

   ![选择存储帐户](media/monitor-tutorial-archive-monitoring-data/activity-log-storage.png)

5. 将“保留期(天)”滑块移至 30。 此滑块设置监视数据要在存储帐户中保留的天数。 Azure Monitor 会自动删除早于所述天数的数据。 如果保留期为 0 天，则无限期存储数据。

6. 单击“保存”并关闭此部分。

现在，订阅的监视数据将流入到存储帐户。

## <a name="route-resource-data-to-the-storage-account"></a>将资源数据路由到存储帐户

接下来，通过设置资源诊断设置来配置要路由到存储帐户的资源级别数据（资源指标和诊断日志）。

1. 单击左侧导航列表中的“监视器”按钮，然后单击“诊断设置”。 在此处查看订阅中所有资源的列表，这些资源通过 Azure Monitor 生成监视数据。 如果此列表中没有任何资源，可在处理前[创建逻辑应用](../logic-apps/logic-apps-create-a-logic-app.md)，以拥有可在其中配置诊断设置的资源。

2. 单击列表中的某个资源，然后单击“启用诊断”。
   
   ![启用诊断](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-turn-on.png)

   如果已配置了设置，则转而显示现有设置和“添加诊断设置”按钮。 单击此按钮。

   资源诊断设置是描述应从特定资源中路由哪个监视数据以及此监视数据应传输到何处的一种定义。

3. 在显示的部分中，提供设置的“名称”，并勾选“存档到存储帐户”框。

   ![诊断设置部分](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-home.png)

4. 单击“存档到存储帐户”下的“配置”按钮，并选择在上一部分中创建的存储帐户。 单击 **“确定”**。

   ![诊断设置存储帐户](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-storage.png)

5. 勾选“日志”和“指标”下的所有框。 可能仅有下述选项之一，这具体取决于资源类型。 这些复选框可控制向所选目标（本例中为存储帐户）发送此资源类型可用的哪些日志和指标数据类别。

   ![诊断设置类别](media/monitor-tutorial-archive-monitoring-data/diagnostic-settings-categories.png)
   
6. 将“保留期(天)”滑块移至 30。 此滑块设置监视数据要在存储帐户中保留的天数。 Azure Monitor 会自动删除早于所述天数的数据。 如果保留期为 0 天，则无限期存储数据。

7. 单击“保存” 。

现在，资源的监视数据将流入到存储帐户。

## <a name="route-virtual-machine-guest-os-data-to-the-storage-account"></a>将虚拟机（来宾 OS）数据路由到存储帐户

1. 如果订阅中没有虚拟机，请[创建虚拟机](../virtual-machines/windows/quick-create-portal.md)。

2. 在门户左侧的导航栏列表中，单击“虚拟机”。

3. 在显示的虚拟机列表中，单击已创建的虚拟机。

4. 在显示的部分中，单击左侧导航栏中的“诊断设置”。 在此部分中，可在虚拟机上设置现成的 Azure Monitor 监视扩展并将 Windows 或 Linux 生成的数据路由到存储帐户中。

   ![导航到诊断设置](media/monitor-tutorial-archive-monitoring-data/guest-navigation.png)

5. 在显示的部分中，单击“启用来宾级别监视”。

   ![启用诊断设置](media/monitor-tutorial-archive-monitoring-data/guest-enable.png)

6. 正确保存诊断设置之后，“概述”选项卡会显示一个列表描述所收集的数据和存储位置。 单击“性能计数器”部分，查看正在收集的一组 Windows 性能计数器。

   ![性能计数器设置](media/monitor-tutorial-archive-monitoring-data/guest-perf-counters.png)
   
7. 单击“日志”选项卡，并勾选应用程序和系统日志中的“信息”级别日志复选框。

   ![日志设置](media/monitor-tutorial-archive-monitoring-data/guest-logs.png)

8. 单击“存储帐户”下的“代理”选项卡，并单击所示的存储帐户名称。

   ![更新存储帐户](media/monitor-tutorial-archive-monitoring-data/guest-storage-home.png)

9. 在显示的部分中，选择在之前的“创建存储帐户”步骤中创建的存储帐户。

10. 单击“保存” 。

现在，虚拟机的监视数据将流入到存储帐户。

## <a name="view-the-monitoring-data-in-the-storage-account"></a>查看存储帐户中的监视数据

如果已执行前述步骤，则数据已开始流向存储帐户。

1. 对于某些数据类型（例如活动日志），需要有一些在存储帐户中生成事件的活动。 若要在活动日志中生成活动，请按照[这些说明](./monitor-quick-audit-notify-action-in-subscription.md)操作。 可能最多需等待 5 分钟，事件即会在存储帐户中显示。

2. 在门户中，导航到左侧导航栏上的“存储帐户”部分。

3. 找到并单击上一部分中创建的存储帐户。

4. 单击“Blob”，再单击标记为“insights-operational-logs”的容器，最后单击标记为“name=default”的容器。 这就是包含活动日志的容器。 监视数据依次按资源 ID（仅活动日志的订阅 ID）、日期和时间细分存入容器。 这些 blob 的完整格式为：

   insights-operational-logs/name=default/resourceId=/SUBSCRIPTIONS/{订阅 ID}/y={四位数年份}/m={两位数月份}/d={两位数日期}/h={两位数 24 小时制小时}/m=00/PT1H.json

5. 通过单击容器中的资源 ID、日期和时间，导航到 PT1H.json 文件。 单击 PT1H.json 文件，再单击“下载”。 每个 PT1H.json blob 都包含一个 JSON blob，其中的事件为在 blob URL 中指定的小时（例如 h=12）内发生的。 在当前的小时内发生的事件将附加到 PT1H.json 文件。 分钟值始终为 00 (m=00)，因为日志事件按小时细分成单个 blob。

   现可查看存储帐户中存储的 JSON 事件。 资源诊断日志的 blob 格式为：

   insights-logs-{日志类别名称}/resourceId=/{资源 ID}/y={4 位数年份}/m={2 位数月份}/d={2 位数日期}/h={2 位数 24 小时制小时}/m=00/PT1H.json

6. 来宾 OS 监视数据存储在表中。 导航回到存储帐户主页，并单击“表”。 存在用于指标、性能计数器和事件日志的表格。

现已成功设置要存档到存储帐户的监视数据。

## <a name="clean-up-resources"></a>清理资源

1. 从之前的“将订阅日志路由到存储帐户”步骤导航回到“导出活动日志”部分，并单击“重置”。

2. 导航到“诊断设置”部分，单击在之前的“将资源数据路由到存储帐户”步骤中创建诊断设置所使用的资源，然后找到创建的设置，单击“编辑设置”按钮，并单击“删除”。

3. 在之前“将虚拟机（来宾 OS）数据路由到存储帐户”步骤中配置的虚拟机上导航到“诊断设置”部分，然后在“代理”选项卡下单击“删除”（位于“删除 Azure 诊断代理”部分下）。

4. 导航到之前“创建存储帐户”步骤中所创建的存储帐户，并单击“删除存储帐户”。 键入存储帐户名称，再单击“删除”。

5. 如果已为前述步骤创建虚拟机或逻辑应用，请一并删除。

## <a name="next-steps"></a>后续步骤

在本教程中，学习了如何设置 Azure 环境（订阅、资源和来宾 OS）中要存档到存储帐户的监视数据。 若要更好地利用数据并获得见解，请尝试同时将数据发送到 Log Analytics。

> [!div class="nextstepaction"]
> [Log Analytics 入门](../log-analytics/log-analytics-get-started.md)

