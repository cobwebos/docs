---
title: 将磁贴添加到仪表板 | Microsoft Docs
description: 本文为构建人员介绍如何配置默认的 Azure IoT Central 应用程序仪表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 49b41715d95a5f210e6e70faf09aa016d1478728
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158713"
---
# <a name="configure-the-application-dashboard"></a>配置应用程序仪表板

“仪表板”是当有权访问应用程序的用户导航到应用程序的 URL 时加载的页面。**** 如果应用程序是从某个**应用程序模板**创建的，则该应用程序中预定义了一个要启动的仪表板。 如果从**旧版应用程序**模板创建应用程序，则仪表板将为空。

> [!NOTE]
> 除了默认的应用程序仪表板以外，用户还可以[创建多个仪表板](howto-create-personal-dashboards.md)。 这些仪表板仅供用户个人使用，或者在应用程序的所有用户之间共享。 

## <a name="add-tiles"></a>添加磁贴

以下屏幕截图显示了从**自定义应用程序**模板创建的应用程序中的仪表板。 若要自定义应用程序的默认仪表板，请选择页面左上角的“编辑”。****

> [!div class="mx-imgBorder"]
> ![基于“示例 Contoso”模板的应用程序的仪表板](media/howto-add-tiles-to-your-dashboard/dashboard-sample-contoso.png)

选择“编辑”会打开仪表板库面板。**** 该库包含可用于自定义仪表板的磁贴和仪表板基元。

> [!div class="mx-imgBorder"]
> ![仪表板库](media/howto-add-tiles-to-your-dashboard/dashboard-library.png)

例如，可为设备的当前温度添加一个“遥测”磁贴。**** 为此，请执行以下操作：
1. 选择一个**设备模板**
1. 选择要在仪表板磁贴上显示的设备的**设备实例**。 然后，会看到可在磁贴中使用的设备属性列表。
1. 若要在仪表板上创建磁贴，请单击“温度”并将其拖放到仪表板区域。**** 也可以单击“温度”旁边的复选框，然后单击“组合”。******** 以下屏幕截图显示了用户选择设备模板和设备实例，然后在仪表板上创建“温度遥测”磁贴。
1. 选择左上角的“保存”，将磁贴保存到仪表板。****

> [!div class="mx-imgBorder"]
> ![包含设置和属性详细信息的“配置设备详细信息”窗体](media/howto-add-tiles-to-your-dashboard/device-details.png)

现在，当操作员查看默认的应用程序仪表板时，会看到新磁贴，其中包含设备的“温度”。**** 每个磁贴包含一个预先选择的图形、图表等，创建磁贴时会显示它。 但是，用户可以选择编辑和更改此可视化效果。 

> [!div class="mx-imgBorder"]
> ![包含显示的磁贴设置和属性的“仪表板”选项卡](media/howto-add-tiles-to-your-dashboard/settings-and-properties.png)

## <a name="edit-tiles"></a>编辑磁贴

若要编辑仪表板上的磁贴，请先单击页面左上角的“编辑”，打开仪表板及其所有磁贴的编辑模式。**** 

> [!div class="mx-imgBorder"]
> ![“仪表板”屏幕，其中为所选磁贴激活了编辑模式](media/howto-add-tiles-to-your-dashboard/edit-mode.png)

然后单击要编辑的磁贴右上角的**齿轮**图标。 可在此处编辑磁贴的各个方面，包括标题、可视化效果、聚合等。

> [!div class="mx-imgBorder"]
> ![磁贴聚合设置的下拉菜单](media/howto-add-tiles-to-your-dashboard/aggregation-settings.png)

也可以单击磁贴上的**标尺**图标来更改图表的可视化效果。

> [!div class="mx-imgBorder"]
> ![磁贴可视化效果设置的下拉菜单](media/howto-add-tiles-to-your-dashboard/visualization-settings.png)

## <a name="tile-types"></a>磁贴类型

下表汇总了 Azure IoT Central 中磁贴的用法：
 
| 磁贴 | 仪表板 | 描述
| ----------- | ------- | ------- |
| 内容 | 应用程序和设备集仪表板 |Markdown 支持的磁贴是可单击的磁贴，其中会显示标题和说明文本。 还可以使用此磁贴作为链接磁贴，使用户能够导航到与你的应用程序相关的 URL。|
| 图像 | 应用程序和设备集仪表板 |“图像”磁贴显示自定义图像，是可单击的。 使用图像磁贴可将图形添加到仪表板，有时还使用户能够导航到与你的应用程序相关的 URL。|
| Label | 应用程序仪表板 |“标签”磁贴显示仪表板上的自定义文本。 可以选择文本大小。 使用标签磁贴可将说明、联系人详细信息或帮助等相关信息添加到仪表板。|
| 映射 | 应用程序和设备集仪表板 |“地图”磁贴显示设备在地图上的位置和状态。 例如，可以显示设备的位置，以及其风扇是否已打开。|
| 折线图 | 应用程序和设备仪表板 |“折线图”磁贴显示设备在一段时间内的聚合测量图表。 例如，可以在折线图中显示设备在过去一小时的平均温度和压力。|
| 条形图 | 应用程序和设备仪表板 |“条形图”磁贴显示设备在一段时间内的聚合测量图表。 例如，可以在条形图中显示设备在过去一小时的平均温度和压力。|
| 饼图 | 应用程序和设备集仪表板 |“饼图”磁贴显示设备在一段时间内的聚合测量图表。|
| 热度地图 | 应用程序和设备集仪表板 |“热度地图”磁贴以不同的颜色显示有关设备集的信息。|
| 事件历史记录 | 应用程序和设备仪表板 |“事件历史记录”磁贴显示设备在一段时间内发生的事件。 例如，可以使用此类磁贴来显示设备在过去一小时的所有温度变化。|
| 状态历史记录 | 应用程序和设备仪表板 |“状态历史记录”磁贴显示一段时间内的测量值。 例如，可以使用此类磁贴来显示设备在过去一小时的温度值。|
| KPI | 应用程序和设备仪表板 | “KPI”磁贴显示一段时间内的聚合遥测或事件测量值。 例如，可以使用此类磁贴来显示设备在过去一小时达到的最高温度。|
| 上一个已知值 | 应用程序和设备仪表板 |“上一个已知值”磁贴显示最新的遥测或状态测量值。 例如，可以使用此磁贴显示最近的设备温度、压力和湿度测量值。|

## <a name="next-steps"></a>后续步骤

现在，您已经了解如何配置 Azure IoT 中央默认应用程序仪表板，您可以[了解如何创建个人仪表板](howto-create-personal-dashboards.md)。
