---
title: 使用 Grafana 可视化 Azure 数据资源管理器中的数据
description: 本文介绍如何将 Azure 数据资源管理器设置为 Grafana 的数据源，然后将示例群集中的数据可视化。
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: a1c52007ea86ca0812c4a73a92ce81db6ddadc7b
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2019
ms.locfileid: "74038041"
---
# <a name="visualize-data-from-azure-data-explorer-in-grafana"></a>在 Grafana 中可视化 Azure 数据资源管理器中的数据

Grafana 是一个分析平台，可用于查询和可视化数据，然后根据可视化结果创建和共享仪表板。 Grafana 提供一个 Azure 数据资源管理器插件，通过该插件可连接到 Azure 数据资源管理器并可视化数据。 本文介绍如何将 Azure 数据资源管理器设置为 Grafana 的数据源，然后将示例群集中的数据可视化。

使用以下视频了解如何使用 Grafana 的 Azure 数据资源管理器插件，如何将 Azure 数据资源管理器设置为 Grafana 的数据源，然后将数据可视化。 

> [!VIDEO https://www.youtube.com/embed/fSR_qCIFZSA]

此外，还可以[配置数据源并将](#configure-the-data-source)[数据可视化](#visualize-data)，如以下文章中所述。

## <a name="prerequisites"></a>先决条件

若要完成本文，需要满足以下条件：

* 适用于操作系统的 [Grafana 版本 5.3.0 或更高版本](https://docs.grafana.org/installation/)

* 适用于 Grafana 的 [Azure 数据资源管理器插件](https://grafana.com/plugins/grafana-azure-data-explorer-datasource/installation)

* 包括 StormEvents 示例数据的群集。 有关详细信息，请参阅[快速入门：创建 Azure 数据资源管理器群集和数据库](create-cluster-database-portal.md)和[将示例数据引入到 Azure 数据资源管理器](ingest-sample-data.md)。

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

[!INCLUDE [data-explorer-configure-data-source](../../includes/data-explorer-configure-data-source.md)]

### <a name="specify-properties-and-test-the-connection"></a>指定属性并测试连接

将服务主体分配给“查看者”角色后，现可在 Grafana 实例中指定属性，并测试与 Azure 数据资源管理器的连接。

1. 在 Grafana 的左侧菜单中，选择齿轮图标，然后选择“数据源”。

    ![数据源](media/grafana/data-sources.png)

1. 选择“添加数据源”。

1. 在“数据源/新建”页面上，输入数据源的名称，然后选择类型“Azure 数据资源管理器数据源”。

    ![连接名称和类型](media/grafana/connection-name-type.png)

1. 输入群集的名称，名称采用 https://{ClusterName}.{Region}.kusto.windows.net 的形式。 输入 Azure 门户或 CLI 中的其他值。 有关映射，请参阅下图中的表。

    ![连接属性](media/grafana/connection-properties.png)

    | Grafana UI | Azure 门户 | Azure CLI |
    | --- | --- | --- |
    | 订阅 ID | 订阅 ID | SubscriptionId |
    | 租户 ID | Directory ID | tenant |
    | 客户端 ID | 应用程序 ID | appId |
    | 客户端机密 | 密码 | password |
    | | | |

1. 选择“保存并测试”。

    如果测试成功，请转到下一部分。 如果遇到任何问题，请检查在 Grafana 中指定的值，并查看前面的步骤。

## <a name="visualize-data"></a>可视化数据

已将 Azure 数据资源管理器配置为 Grafana 的数据源，现在即可进行数据可视化。 这里演示一个基本例子，除例子所示，还有很多其他可执行的操作。 建议查看[为 Azure 数据资源管理器编写查询](write-queries.md)，以获取针对示例数据集运行的其他查询的示例。

1. 在 Grafana 的左侧菜单中，选择加号图标，然后选择“仪表板”。

    ![创建仪表板](media/grafana/create-dashboard.png)

1. 在“添加”选项卡下选择“图形”。

    ![添加图形](media/grafana/add-graph.png)

1. 在图形面板上，选择“面板标题”，然后选择“编辑”。

    ![编辑面板](media/grafana/edit-panel.png)

1. 在面板底部，选择“数据源”，然后选择所配置的数据源。

    ![选择数据源](media/grafana/select-data-source.png)

1. 在查询窗格中，复制以下查询，然后选择“运行”。 查询会按天为示例数据集统计事件计数。

    ```kusto
    StormEvents
    | summarize event_count=count() by bin(StartTime, 1d)
    ```

    ![运行查询](media/grafana/run-query.png)

1. 该图表未显示任何结果，因为默认情况下其显示范围为过去六小时的数据。 在顶部菜单上，选择“过去 6 小时”。

    ![过去 6 小时](media/grafana/last-six-hours.png)

1. 指定涵盖 2007 年的自定义范围，即 StormEvents 示例数据集中包含的年份。 选择“应用”。

    ![自定义日期范围](media/grafana/custom-date-range.png)

    现在，图表显示了 2007 年的数据，按日统计。

    ![已完成的图表](media/grafana/finished-graph.png)

1. 在顶部菜单上，选择保存图标： ![“保存”图标](media/grafana/save-icon.png)。

## <a name="create-alerts"></a>创建警报

1. 在主仪表板中，选择 "**警报**" > **通知通道**创建新的通知通道

    ![创建通知通道](media/grafana/create-notification-channel.png)

1. 创建新的**通知通道**，并**保存**。

    ![新建通知通道](media/grafana/new-notification-channel-adx.png)

1. 在**仪表板**上，从下拉列表中选择 "**编辑**"。

    ![在仪表板中选择编辑](media/grafana/edit-panel-4-alert.png)

1. 选择 "警报电铃" 图标以打开 "**警报**" 窗格。 选择 "**创建警报**"。 在**警报**窗格中完成以下属性。

    ![警报属性](media/grafana/alert-properties.png)

1. 选择 "**保存仪表板**" 图标以保存所做的更改。

## <a name="next-steps"></a>后续步骤

* [Azure 数据资源管理器的编写查询](write-queries.md)
