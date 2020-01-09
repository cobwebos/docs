---
title: 将磁贴添加到仪表板 |Microsoft Docs
description: 作为生成器，请了解如何配置默认的 Azure IoT Central 应用程序仪表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: b13349ae4293f6377429e9dc72b6c2cb43f92348
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435116"
---
# <a name="configure-the-application-dashboard"></a>配置应用程序仪表板

**仪表板**是当有权访问应用程序的用户导航到应用程序的 URL 时所加载的页面。 如果是从一个**应用程序模板**创建的应用程序，应用程序将具有开始预定义的仪表板。 如果你是从**旧应用**程序应用程序模板创建的应用程序，你的仪表板将为空，以便启动。

> [!NOTE]
> 除了默认的应用程序仪表板外，用户还可以[创建多个仪表板](howto-create-personal-dashboards.md)。 这些仪表板仅可以是用户的个人，或在应用程序的所有用户之间共享。 

## <a name="add-tiles"></a>添加磁贴

以下屏幕截图显示了从**自定义应用程序**模板创建的应用程序中的仪表板。 若要自定义应用程序的默认仪表板，请选择页面左上角的 "**编辑**"。

> [!div class="mx-imgBorder"]
> 基于 "示例 Contoso" 模板的应用程序 ![仪表板](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

选择 "**编辑**" 将打开 "仪表板库" 面板。 该库包含可用于自定义仪表板的磁贴和仪表板基元。

> [!div class="mx-imgBorder"]
> ![仪表板库](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

例如，可以为设备的当前温度添加**遥测**磁贴。 为此，请执行以下操作：
1. 选择**设备模板**
1. 选择要在仪表板磁贴上查看的设备的**设备实例**。 然后，会看到可在磁贴上使用的设备属性的列表。
1. 若要在仪表板上创建磁贴，请单击 "**温度**" 并将其拖到 "仪表板" 区域。 还可以单击 "**温度**" 旁的复选框，然后单击 "**合并**"。 以下屏幕截图显示了如何选择设备模板和设备实例，然后在仪表板上创建 "温度遥测" 磁贴。
1. 选择左上角的 "**保存**"，将磁贴保存到仪表板。

> [!div class="mx-imgBorder"]
> !["配置设备详细信息" 窗体，其中包含设置和属性的详细信息](media/howto-add-tiles-to-your-dashboard/device-details.png)

现在，当操作员查看默认应用程序仪表板时，将看到新的磁贴，其中包含设备的**温度**。 每个磁贴都有一个预先选择的图形、图表等，创建磁贴时将显示该图块。 但是，用户可以选择编辑和更改此可视化效果。 

> [!div class="mx-imgBorder"]
> !["仪表板" 选项卡，其中显示了磁贴的显示设置和属性](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)


## <a name="edit-tiles"></a>编辑磁贴

若要编辑仪表板上的磁贴，请先单击页面左上角的 "**编辑**"，这将打开仪表板及其所有磁贴的编辑模式。 

> [!div class="mx-imgBorder"]
> 已为所选磁贴激活编辑模式 ![仪表板屏幕](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

然后，单击要编辑的磁贴右上角的**齿轮**图标。 可在此处编辑磁贴的各个方面，包括标题、可视化效果、聚合等。

> [!div class="mx-imgBorder"]
> 图块聚合设置的 ![下拉列表](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

还可以通过单击图块上的**标尺**图标来更改图表可视化效果。

> [!div class="mx-imgBorder"]
> 磁贴可视化设置 ![下拉列表](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>磁贴类型

下表总结了 Azure IoT Central 中磁贴的用法：
 
| 磁贴 | 仪表板 | Description
| ----------- | ------- | ------- |
| 内容 | 应用程序和设备集仪表板 |Markdown 支持的磁贴是可单击的磁贴，用于显示标题和说明文本。 你还可以使用此磁贴作为链接磁贴，以使用户可以导航到与你的应用程序相关的 URL。|
| 图像 | 应用程序和设备集仪表板 |图像磁贴显示自定义图像，并可单击。 使用图像磁贴可以向仪表板添加图形，还可以选择允许用户导航到与应用程序相关的 URL。|
| 标签 | 应用程序仪表板 |标签磁贴显示仪表板上的自定义文本。 您可以选择文本的大小。 使用标签磁贴将相关信息添加到仪表板中，如 "说明"、"联系人详细信息" 或 "帮助"。|
| 映射 | 应用程序和设备集仪表板 |地图磁贴显示设备在地图上的位置和状态。 例如，可以显示设备的位置以及风扇是否已打开。|
| 折线图 | 应用程序和设备仪表板 |折线图磁贴显示一段时间内设备的聚合度量图表。 例如，可以显示一个折线图，该图显示最近一小时内设备的平均温度和压力。|
| 条形图 | 应用程序和设备仪表板 |条形图磁贴显示一段时间内设备的聚合度量图表。 例如，可以显示一个条形图，显示最近一小时内设备的平均温度和压力。|
| 饼图 | 应用程序和设备集仪表板 |饼图磁贴显示一段时间内设备的聚合度量图表。|
| 热度地图 | 应用程序和设备集仪表板 |热度地图磁贴显示有关设备集的信息，以颜色表示。|
| 事件历史记录 | 应用程序和设备仪表板 |事件历史记录磁贴显示一段时间内设备的事件。 例如，你可以使用它来显示最近一小时内设备的所有温度变化。|
| 状态历史记录 | 应用程序和设备仪表板 |状态历史记录磁贴显示时间段的度量值。 例如，你可以使用它来显示设备在最近一小时内的温度值。|
| KPI | 应用程序和设备仪表板 | KPI 磁贴显示时间段的聚合遥测或事件度量。 例如，你可以使用它来显示最近一小时内设备达到的最大温度。|
| 上一个已知值 | 应用程序和设备仪表板 |"最后一个已知值" 磁贴显示遥测或状态度量的最新值。 例如，您可以使用此磁贴显示设备温度、压力和湿度的最新测量值。|

## <a name="next-steps"></a>后续步骤

现在，你已了解如何配置 Azure IoT Central 默认应用程序仪表板，你可以[了解如何准备和上传图像](howto-prepare-images.md)。