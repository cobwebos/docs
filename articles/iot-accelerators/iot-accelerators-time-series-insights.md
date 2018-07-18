---
title: 使用 Azure 时序见解可视化远程监视数据 | Microsoft Docs
description: 了解如何配置时序见解环境，以探索并分析远程监视解决方案的时序数据。
author: philmea
manager: timlt
ms.author: philmea
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: f16fdfca704b8f8cb175de637ad7f3ef143d3ed7
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968949"
---
# <a name="visualize-remote-monitoring-data-with-time-series-insights"></a>使用时序见解可视化远程监视数据

操作员可能想要进一步扩展远程监视预配置解决方案提供的现成数据可视化效果。 我们的解决方案加速器提供与 TSI 的现成集成。 本操作指南介绍如何配置时序见解来分析设备遥测数据和检测异常。

## <a name="prerequisites"></a>先决条件

若要完成本操作指南，需要满足以下条件：

* [部署远程监视预配置解决方案](iot-accelerators-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>创建使用者组

需要在 IoT 中心创建一个专用的使用者组，用于将数据流式传输到时序见解。

> [!NOTE]
> 应用程序使用使用者组从 Azure IoT 中心提取数据。 每个使用者组最多允许五个输出使用者。 应该为每 5 个输出接收器创建一个新的使用者组，最多可以创建 32 个使用者组。

1. 在 Azure 门户中，单击“Cloud Shell”按钮。

1. 执行以下命令创建新的使用者组：

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="create-a-new-time-series-insights-environment"></a>创建新的时序见解环境

Azure 时序见解是一种完全托管的分析、存储和可视化服务，用于管理云中的 IoT 时序数据。 它提供可大规模扩展的时序数据存储，可用于在数秒内探索和分析来自全球各地的数亿个事件流。 可利用时序见解存储和管理万亿字节的时序数据，同时探索和可视化数亿个事件，还可进行根因分析，对多个站点和资产进行比较。

1. 登录到 [Azure 门户](http://portal.azure.com/)。

1. 选择**创建资源** > **物联网** > **时序见解**。

    ![新时序见解](./media/iot-accelerators-time-series-insights/new-time-series-insights.png)

1. 若要创建时序见解环境，请使用下表中的值：

    | 设置 | 值 |
    | ------- | ----- |
    | 环境名称 | 以下屏幕截图使用名称 **contorosrmtsi**。 完成此步骤后，请选择自己的唯一名称。 |
    | 订阅 | 在下拉列表中选择自己的 Azure 订阅。 |
    | 资源组 | **新建**。 本文使用名称 **ContosoRM**。 |
    | 位置 | 本文使用“美国东部”。 在远程监视解决方案所在的同一区域中创建环境。 |
    | SKU |**S1** |
    | Capacity | **1** |
    | 固定到仪表板 | **是** |

    ![创建时序见解](./media/iot-accelerators-time-series-insights/new-time-series-insights-create.png)

1. 单击“创建”。 可能需要花费片刻时间来创建环境。

## <a name="create-event-source"></a>创建事件源

创建新事件源，以连接到 IoT 中心。 确保使用前面步骤中创建的使用者组。 时序见解要求每个服务具有未被其他服务使用的专用使用者组。

1. 导航到新的时序环境。

1. 在左侧选择“事件源”。

    ![查看事件源](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources.png)

1. 单击 **“添加”**。

    ![添加事件源](./media/iot-accelerators-time-series-insights/time-series-insights-event-sources-add.png)

1. 若要将 IoT 中心配置为新事件源，请使用下表中的值：

    | 设置 | 值 |
    | ------- | ----- |
    | 事件源名称 | 以下屏幕截图使用名称 **contosorm-iot-hub**。 完成此步骤后，请使用自己的唯一名称。 |
    | Source | **IoT 中心** |
    | 导入选项 | **从可用的订阅使用 IoT 中心** |
    | 订阅 ID | 在下拉列表中选择自己的 Azure 订阅。 |
    | IoT 中心名称 | **contosorma57a6**。 使用远程监视解决方案中的 IoT 中心名称。 |
    | IoT 中心策略名称 | **iothubowner**确保使用的策略是所有者策略。 |
    | IoT 中心策略密钥 | 此字段会自动填充。 |
    | IoT 中心使用者组 | **timeseriesinsights** |
    | 事件序列化格式 | **JSON**     | 时间戳属性名称 | 留空 |

    ![创建事件源](./media/iot-accelerators-time-series-insights/time-series-insights-event-source-create.png)

1. 单击“创建”。

> [!NOTE]
> 如果需要向其他用户授予对时序见解资源管理器的访问权限，可以使用这些步骤来[授予数据访问权限](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-data-access#grant-data-access)。

## <a name="time-series-insights-explorer"></a>时序见解资源管理器

时序见解资源管理器是一个 Web 应用，可帮助创建数据的可视化效果。

1. 选择“概述”选项卡。

1. 单击“转到环境”，打开时序见解资源管理器 Web 应用。

    ![时序见解资源管理器](./media/iot-accelerators-time-series-insights/time-series-insights-environment.png)

1. 在时间选择面板上，从快速时间菜单中选择“过去 12 小时”，然后单击“搜索”。

    ![时序见解资源管理器中的搜索](./media/iot-accelerators-time-series-insights/time-series-insights-search-time.png)

1. 在左侧的条件面板中，选择“度量”值 **temperature**，以及“拆分依据”值 **iothub-connection-device-id**。

    ![时序见解资源管理器中的查询](./media/iot-accelerators-time-series-insights/time-series-insights-query1.png)

1. 右键单击图表并选择“浏览事件”。

    ![时序见解资源管理器中的事件](./media/iot-accelerators-time-series-insights/time-series-insights-explore-events.png)

1. 事件以表格格式呈现在网格中。

    ![时序见解资源管理器中的表格](./media/iot-accelerators-time-series-insights/time-series-insights-table.png)

1. 单击透视图按钮。

    ![时序见解资源管理器中的透视图](./media/iot-accelerators-time-series-insights/time-series-insights-explorer-perspective.png)

1. 单击“添加”，在透视图中创建新查询。

    ![时序见解资源管理器中的“添加查询”](./media/iot-accelerators-time-series-insights/time-series-insights-new-query.png)

1. 选择快速时间值“过去 12 小时”、“度量”值 **Humidity**，以及“拆分依据”值 **iothub-connection-device-id**。

    ![时序见解资源管理器中的查询](./media/iot-accelerators-time-series-insights/time-series-insights-query2.png)

1. 单击透视图按钮查看设备指标的仪表板。

    ![时序见解资源管理器中的仪表板](./media/iot-accelerators-time-series-insights/time-series-insights-dashboard.png)

## <a name="next-steps"></a>后续步骤

若要了解如何在时序见解资源管理器中浏览和查询数据，请参阅 [Azure 时序见解资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)。
