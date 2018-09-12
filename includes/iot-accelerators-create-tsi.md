---
title: include 文件
description: include 文件
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/20/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 8c114ed137089e70899e601ebdc1d4d39f562601
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382933"
---
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

[Azure 时序见解](../articles/time-series-insights/time-series-insights-overview.md)是一种完全托管的分析、存储和可视化服务，用于管理云中的 IoT 时序数据。 若要创建新的时序见解环境：

1. 登录到 [Azure 门户](http://portal.azure.com/)。

1. 选择“创建资源” > “物联网” > “时序见解”：

    ![新时序见解](./media/iot-accelerators-create-tsi/new-time-series-insights.png)

1. 若要在解决方案加速器所在的同一资源组中创建时序见解环境，请使用下表中的值：

    | 设置 | 值 |
    | ------- | ----- |
    | 环境名称 | 以下屏幕截图使用名称 Contoso-TSI。 完成此步骤后，请选择自己的唯一名称。 |
    | 订阅 | 在下拉列表中选择自己的 Azure 订阅。 |
    | 资源组 | contoso-simulation。 使用解决方案加速器的名称。 |
    | 位置 | 此示例使用美国东部。 在设备模拟加速器所在的同一区域中创建环境。 |
    | SKU |**S1** |
    | Capacity | **1** |

    ![创建时序见解](./media/iot-accelerators-create-tsi/new-time-series-insights-create.png)

    > [!NOTE]
    > 向解决方案加速器所在的同一资源组中添加时序见解环境意味着删除解决方案加速器时将删除此环境。

1. 单击“创建”。 可能需要花费几分钟时间来创建环境。

## <a name="create-event-source"></a>创建事件源

创建新事件源，以连接到 IoT 中心。 使用前面步骤中创建的使用者组。 时序见解事件源要求专用使用者组不被其他服务使用。

1. 在 Azure 门户中，导航到新的时序环境。

1. 在左侧单击“事件源”：

    ![查看事件源](./media/iot-accelerators-create-tsi/time-series-insights-event-sources.png)

1. 单击“添加”：

    ![添加事件源](./media/iot-accelerators-create-tsi/time-series-insights-event-sources-add.png)

1. 若要将 IoT 中心配置为新事件源，请使用下表中的值：

    | 设置 | 值 |
    | ------- | ----- |
    | 事件源名称 | 以下屏幕截图使用名称 contoso-iot-hub。 完成此步骤后，请使用自己的唯一名称。 |
    | Source | **IoT 中心** |
    | 导入选项 | **从可用的订阅使用 IoT 中心** |
    | 订阅 ID | 在下拉列表中选择自己的 Azure 订阅。 |
    | IoT 中心名称 | contoso-simulation7d894。 从设备模拟解决方案加速器中使用 IoT 中心的名称。 |
    | IoT 中心策略名称 | iothubowner |
    | IoT 中心策略密钥 | 此字段会自动填充。 |
    | IoT 中心使用者组 | devicesimulationtsi |
    | 事件序列化格式 | **JSON** |
    | 时间戳属性名称 | 留空 |

    ![创建事件源](./media/iot-accelerators-create-tsi/time-series-insights-event-source-create.png)

1. 单击“创建”。

> [!NOTE]
> 可以[向其他用户授予访问](../articles/time-series-insights/time-series-insights-data-access.md#grant-data-access)时序见解资源管理器的权限。