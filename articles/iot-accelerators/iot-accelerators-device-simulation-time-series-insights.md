---
title: 使用时序见解可视化设备模拟遥测数据 - Azure | Microsoft Docs
description: 了解如何配置时序见解环境以浏览和分析设备模拟解决方案加速器所生成的遥测数据。
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 5d20adc11e0d679e12fd060e719593a50180db8e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65834853"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>使用时序见解可视化从设备模拟解决方案加速器发送的遥测数据

通过设备模拟解决方案加速器，可以从模拟设备生成遥测数据以对 IoT 解决方案进行测试。 本操作指南演示如何使用时序见解环境可视化和分析模拟的遥测数据。

## <a name="prerequisites"></a>必备组件

若要执行本操作指南中的步骤，需要拥有一个有效的 Azure 订阅。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

本操作指南中的步骤假定你已向 Azure 订阅部署了设备模拟解决方案加速器。 如果尚未部署解决方案加速器，请执行[部署并运行基于云的设备模拟解决方案](quickstart-device-simulation-deploy.md)快速入门中的步骤。

本文假设解决方案加速器的名称为 contoso-simulatio。 在完成以下步骤时，将 contoso-simulatio 替换为解决方案加速器的名称。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>创建使用者组

需要在 IoT 中心创建一个专用的使用者组，以便将遥测流式传输到时序见解。 时序见解中的事件源应具有 IoT 中心使用者组的独占使用权限。

以下步骤在 Azure Cloud Shell 中使用 Azure CLI 来创建使用者组：

1. IoT 中心是部署设备模拟解决方案加速器时创建的多个资源之一。 执行以下命令查找 IoT 中心的名称 - 记得使用解决方案加速器的名称：

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    IoT 中心是 Microsoft.Devices/IotHubs 类型的资源。

1. 向中心添加名为 devicesimulationtsi 的使用者组。 在以下命令中，使用中心和解决方案加速器的名称：

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    此时可关闭 Azure Cloud Shell。

## <a name="create-a-new-time-series-insights-environment"></a>创建新的时序见解环境

[Azure 时序见解](../../articles/time-series-insights/time-series-insights-overview.md)是一种完全托管的分析、存储和可视化服务，用于管理云中的 IoT 时序数据。 若要创建新的时序见解环境：

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 选择“创建资源” > “物联网” > “时序见解”：

    ![新建时序见解](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. 若要在解决方案加速器所在的同一资源组中创建时序见解环境，请使用下表中的值：

    | 设置 | 值 |
    | ------- | ----- |
    | 环境名称 | 以下屏幕截图使用名称 Contoso-TSI。 完成此步骤后，请选择自己的唯一名称。 |
    | 订阅 | 在下拉列表中选择自己的 Azure 订阅。 |
    | 资源组 | contoso-simulation。 使用解决方案加速器的名称。 |
    | Location | 此示例使用美国东部。 在设备模拟加速器所在的同一区域中创建环境。 |
    | SKU |**S1** |
    | 容量 | **1** |

    ![创建时序见解](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > 向解决方案加速器所在的同一资源组中添加时序见解环境意味着删除解决方案加速器时将删除此环境。

1. 单击**创建**。 可能需要花费几分钟时间来创建环境。

## <a name="create-event-source"></a>创建事件源

创建新事件源，以连接到 IoT 中心。 使用前面步骤中创建的使用者组。 时序见解事件源要求专用使用者组不被其他服务使用。

1. 在 Azure 门户中，导航到新的时序环境。

1. 在左侧单击“事件源”：

    ![查看事件源](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. 单击“添加”：

    ![添加事件源](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. 若要将 IoT 中心配置为新事件源，请使用下表中的值：

    | 设置 | 值 |
    | ------- | ----- |
    | 事件源名称 | 以下屏幕截图使用名称 contoso-iot-hub。 完成此步骤后，请使用自己的唯一名称。 |
    | 源 | **IoT 中心** |
    | 导入选项 | **从可用的订阅使用 IoT 中心** |
    | 订阅 ID | 在下拉列表中选择自己的 Azure 订阅。 |
    | IoT 中心名称 | contoso-simulation7d894。 从设备模拟解决方案加速器中使用 IoT 中心的名称。 |
    | IoT 中心策略名称 | iothubowner |
    | IoT 中心策略密钥 | 此字段会自动填充。 |
    | IoT 中心使用者组 | devicesimulationtsi |
    | 事件序列化格式 | **JSON** |
    | 时间戳属性名称 | 留空 |

    ![创建事件源](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. 单击**创建**。

> [!NOTE]
> 可以[向其他用户授予访问](../../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access)时序见解资源管理器的权限。

## <a name="start-a-simulation"></a>启动模拟

在使用时序见解资源管理器之前，对设备模拟解决方案加速器进行配置以生成一些遥测数据。 下面的屏幕截图显示了一个正在运行的模拟，其中包含了 10 个冷却器设备：

![正在运行的设备模拟](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>时序见解资源管理器

时序见解资源管理器是一个 Web 应用，可用于可视化遥测数据。

1. 在 Azure 门户中，选择时序见解“概述”选项卡。

1. 若要打开时序见解资源管理器 Web 应用，请单击“转到环境”：

    ![时序见解资源管理器](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. 在时间选择面板上，从快速时间菜单中选择“过去 30 分钟”，然后单击“搜索”：

    ![时序见解资源管理器中的搜索](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. 在左侧的条件面板中，选择“温度”作为“度量”值，并且选择“iothub-connection-device-id”作为“拆分依据”值：

    ![时序见解资源管理器中的查询](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. 右键单击图表并选择“浏览事件”：

    ![时序见解资源管理器中的事件](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. 事件数据显示在一个网格中：

    ![时序见解资源管理器中的表格](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. 单击透视视图按钮：

    ![时序见解资源管理器中的透视](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. 单击“+”向透视添加新查询：

    ![时序见解资源管理器中的“添加查询”](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. 选择“过去 30 分钟”作为时间跨度值，选择“湿度”作为“度量值”，并且选择“iothub-connection-device-id”作为“拆分依据”值：

    ![时序见解资源管理器中的查询](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. 单击透视视图按钮以查看设备遥测数据仪表板：

    ![时序见解资源管理器中的仪表板](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>清理资源

如果打算深入探索，请保留部署的解决方案加速器。

如果不再需要该解决方案加速器，请在[预配的解决方案](https://www.azureiotsolutions.com/Accelerators#dashboard)页中选择它，然后单击“删除解决方案”将其删除。

如果已向解决方案加速器的资源组中添加时序见解环境，则在删除解决方案加速器时将自动删除此环境。 否则必须手动从 Azure 门户中删除此时序见解环境。

## <a name="next-steps"></a>后续步骤

若要深入了解如何在时序见解资源管理器中浏览和查询数据，请参阅 [Azure 时序见解资源管理器](../time-series-insights/time-series-insights-explorer.md)。
