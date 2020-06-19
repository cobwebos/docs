---
title: 向仪表板添加磁贴 | Microsoft Docs
description: 作为构建者，了解如何配置默认 Azure IoT Central 应用程序仪表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 1110f76a792a7e3955d5fd32e01ac1566d467151
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83659077"
---
# <a name="configure-the-application-dashboard"></a>配置应用程序仪表板

“仪表板”是当有权访问应用程序的用户导航到应用程序的 URL 时加载的页面。 如果从其中一个“应用程序模板”创建应用程序，则此应用程序将有一个可以开始使用的预定义仪表板。 如果从“自定义应用程序”应用程序模板创建应用程序，仪表板将显示一些入门提示。

> [!NOTE]
> 除了默认应用程序仪表板外，用户还可以[创建多个仪表板](howto-create-personal-dashboards.md)。 这些仪表板可以仅供用户使用，也可以在应用程序的所有用户之间共享。  

## <a name="add-tiles"></a>添加磁贴

以下屏幕截图显示从“自定义应用程序”模板创建的应用程序中的仪表板。 要自定义应用程序的默认仪表板，请在页面左上角选择“编辑”。

> [!div class="mx-imgBorder"]
> ![基于“示例 Contoso”模板的应用程序的仪表板](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

选择“编辑”可打开仪表板库面板。 库包含可用于自定义仪表板的磁贴和仪表板基元。

> [!div class="mx-imgBorder"]
> ![仪表板库](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

例如，你可以为设备的当前温度添加“遥测”磁贴。 为此，请执行以下操作：

1. 选择“设备模板”
1. 从“设备”中选择要在仪表板磁贴上查看的设备。 然后，你将看到可在磁贴上使用的设备属性的列表。
1. 要在仪表板上创建磁贴，请单击“温度”并将它拖动到仪表板区域。 还可以单击“温度”旁的复选框，然后单击“添加磁贴” 。 以下屏幕截图显示选择设备模板和设备实例，然后在仪表板上创建“温度遥测”磁贴。
1. 选择左上角的“保存”以将磁贴保存到仪表板。

> [!div class="mx-imgBorder"]
> ![带有设置和属性详细信息的“配置设备详细信息”窗体](media/howto-add-tiles-to-your-dashboard/device-details.png)

现在，当操作员查看默认应用程序仪表板时，他们会看到带有设备“温度”的新磁贴。 每个磁贴都有一个预先选择的图形、图表等，创建磁贴后会显示这些效果。 但是，用户可以选择编辑和更改这些可视化效果。  

> [!div class="mx-imgBorder"]
> ![带有显示的磁贴设置和属性的“仪表板”选项卡](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>编辑磁贴

要编辑仪表板上的磁贴，首先单击页面左上角的“编辑”，这将打开仪表板及其所有磁贴的编辑模式。  

> [!div class="mx-imgBorder"]
> ![为所选磁贴激活编辑模式的仪表板屏幕](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

然后，单击要编辑的磁贴右上角的齿轮图标。 在这里，你可以编辑磁贴的各个方面，包括其标题、可视化效果、聚合等。

> [!div class="mx-imgBorder"]
> ![磁贴聚合设置的下拉列表](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

你还可以通过单击磁贴上的标尺图标来更改图表可视化效果。

> [!div class="mx-imgBorder"]
> ![磁贴可视化效果设置的下拉列表](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>磁贴类型

下表总结了 Azure IoT Central 中磁贴的用法：

| 磁贴 | 仪表板 | 说明
| ----------- | ------- | ------- |
| 内容 | 应用程序和设备集仪表板 |Markdown 支持的磁贴是显示标题和描述文本的可单击磁贴。 你还可以使用此磁贴作为链接磁贴，使用户能够导航到与应用程序相关的 URL。|
| 映像 | 应用程序和设备集仪表板 |图像磁贴显示自定义图像，可以单击。 使用图像磁贴将图形添加到仪表板，并可以选择允许用户导航到与应用程序相关的 URL。|
| Label | 应用程序仪表板 |标签磁贴在仪表板上显示自定义文本。 你可以选择文本的大小。 使用标签磁贴向仪表板添加相关信息，例如说明、联系人详细信息或帮助。|
| 映射 | 应用程序和设备仪表板 |地图磁贴在地图上显示设备的位置。 你还可以显示设备位置历史记录的 100 个点。 例如，你可以显示设备过去一周所在的位置的采样路线。|
| 折线图 | 应用程序和设备仪表板 |折线图磁贴显示设备一段时间内的聚合测量图表。 例如，你可以显示设备在过去一小时内的平均温度和压力的折线图。|
| 条形图 | 应用程序和设备仪表板 |条形图磁贴显示设备一段时间内的聚合测量图表。 例如，你可以显示设备在过去一小时内的平均温度和压力的条形图。|
| 饼图 | 应用程序和设备集仪表板 |饼图磁贴显示设备一段时间内的聚合测量图表。|
| 热度地图 | 应用程序和设备集仪表板 |“热度地图”磁贴显示有关设备的信息，并用不同颜色加以展示。|
| 事件历史记录 | 应用程序和设备仪表板 |“事件历史记录”磁贴显示设备在一段时间内的事件。 例如，你可以使用它显示设备在过去一小时内的所有温度变化。|
| 状态历史记录 | 应用程序和设备仪表板 |状态历史记录磁贴显示一段时间内的测量值。 例如，你可以使用它显示设备在过去一小时内的温度值。|
| KPI | 应用程序和设备仪表板 | KPI 磁贴显示一段时间内的聚合遥测或事件度量。 例如，你可以使用它显示设备在过去一小时内达到的最高温度。|
| 上一个已知值 | 应用程序和设备仪表板 |上一个已知值磁贴显示遥测或状态测量的最新值。 例如，你可以使用此磁贴显示设备温度、压力和湿度的最新测量值。 |
| properties | 应用程序和设备仪表板 | 属性磁贴显示设备属性和云属性的当前值。 例如，你可以使用此磁贴显示设备属性，如设备的制造商或固件版本。 |

### <a name="customizing-visualizations"></a>自定义可视化效果

对于折线图、条形图和饼图，你可以自定义图表中不同遥测显示的颜色。 为此，选择要自定义的遥测旁边的调色板图标，然后选择一种颜色。

> [!div class="mx-imgBorder"]
> ![遥测颜色显示设置的下拉列表](media/howto-add-tiles-to-your-dashboard/color-customization.png)

对于字符串类型的遥测或属性，你可以选择可视化文本的方式。 例如，如果设备发送 URL 作为字符串遥测，则可以将该 URL 可视化为可单击的链接。 如果 URL 引用图像，则可以在上一个已知值或属性磁贴中呈现图像。 可以通过选择遥测名称旁边的齿轮来更改字符串遥测的显示方式。 通过这种方式，你可以将文本显示为文本、链接或图像。

> [!div class="mx-imgBorder"]
> ![字符串可视化效果设置的下拉列表](media/howto-add-tiles-to-your-dashboard/string-viz-customization.png)

## <a name="next-steps"></a>后续步骤

现在，你已了解如何配置 Azure IoT Central 默认应用程序仪表板，接下来可以[了解如何创建个人仪表板](howto-create-personal-dashboards.md)。
