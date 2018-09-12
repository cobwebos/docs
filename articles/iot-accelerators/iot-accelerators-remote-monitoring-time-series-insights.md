---
title: 使用 Azure 时序见解可视化远程监视遥测数据 | Microsoft Docs
description: 了解如何配置时序见解环境以浏览和分析远程监视解决方案加速器所生成的遥测数据。
author: philmea
manager: timlt
ms.author: philmea
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 02b3afbc62be7a7a9c396de888378d762405248a
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382963"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-remote-monitoring-solution-accelerator"></a>使用时序见解可视化从远程监视解决方案加速器发送的遥测数据

作为操作员，你可能想要进一步扩展远程监视解决方案加速器的现有数据可视化功能。 本操作指南演示如何使用时序见解环境来可视化和分析发送到远程监视解决方案加速器的遥测数据。

## <a name="prerequisites"></a>先决条件

若要执行本操作指南中的步骤，需要拥有一个有效的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本操作指南中的步骤假定你已向 Azure 订阅部署了远程监视解决方案加速器。 如果尚未部署解决方案加速器，请执行[部署并运行基于云的远程监视解决方案](quickstart-remote-monitoring-deploy.md)快速入门中的步骤。

本文假设解决方案加速器的名称为 contoso-simulatio。 在完成以下步骤时，将 contoso-simulatio 替换为解决方案加速器的名称。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [iot-accelerators-create-tsi.md](../../includes/iot-accelerators-create-tsi.md)]

## <a name="time-series-insights-explorer"></a>时序见解资源管理器

时序见解资源管理器是一个 Web 应用，可用于可视化遥测数据。

1. 在 Azure 门户中，选择时序见解“概述”选项卡。

1. 若要打开时序见解资源管理器 Web 应用，请单击“转到环境”：

    ![时序见解资源管理器](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-environment.png)

1. 在时间选择面板上，从快速时间菜单中选择“过去 30 分钟”，然后单击“搜索”：

    ![时序见解资源管理器中的搜索](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-search-time.png)

1. 在左侧的条件面板中，选择“温度”作为“度量”值，并且选择“iothub-connection-device-id”作为“拆分依据”值：

    ![时序见解资源管理器中的查询](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query1.png)

1. 右键单击图表并选择“浏览事件”：

    ![时序见解资源管理器中的事件](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explore-events.png)

1. 事件数据显示在一个网格中：

    ![时序见解资源管理器中的表格](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-table.png)

1. 单击透视视图按钮：

    ![时序见解资源管理器中的透视](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-explorer-perspective.png)

1. 单击“+”向透视添加新查询：

    ![时序见解资源管理器中的“添加查询”](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-new-query.png)

1. 选择“过去 30 分钟”作为时间跨度值，选择“湿度”作为“度量值”，并且选择“iothub-connection-device-id”作为“拆分依据”值：

    ![时序见解资源管理器中的查询](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-query2.png)

1. 单击透视视图按钮以查看设备遥测数据仪表板：

    ![时序见解资源管理器中的仪表板](./media/iot-accelerators-remote-monitoring-time-series-insights/time-series-insights-dashboard.png)

[!INCLUDE [iot-accelerators-cleanup-tsi.md](../../includes/iot-accelerators-cleanup-tsi.md)]

## <a name="next-steps"></a>后续步骤

若要了解如何在时序见解资源管理器中浏览和查询数据，请参阅 [Azure 时序见解资源管理器](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)。
