---
title: 创建 Azure IoT Central 的个人仪表板 |Microsoft Docs
description: 作为用户，了解如何创建和管理你的个人仪表板。
author: dominicbetts
ms.author: dobett
ms.date: 02/13/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: fb74669dcaa802ad06a9c4dff3ffdf25726f518c
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2019
ms.locfileid: "57316050"
---
# <a name="create-and-manage-personal-dashboards"></a>创建和管理个人仪表板

**仪表板**是首次导航到你的应用程序时加载的页。 一个**生成器**在应用程序中定义的所有用户的默认应用程序仪表板。 可以使用你自己，个性化的应用程序仪表板替换此默认的仪表板。 可以有多个仪表板，以显示不同的数据以及它们之间进行切换。

## <a name="create-dashboard"></a>创建仪表板

下面的屏幕截图显示在从创建的应用程序中的仪表板**示例 Contoso**模板。 可以使用个人仪表板替换默认应用程序仪表板。 若要执行此操作，请选择 **+ 新建**顶部页面右上角。

![基于"示例 Contoso"模板的应用程序的仪表板](media/howto-personalize-dashboard/defaultdashboard.png)

选择 **+ 新建**，将打开仪表板编辑器。 在编辑器中，您可以为你的仪表板提供一个名称，并从库中选择项。 库中包含的磁贴和仪表板基元可用于自定义仪表板。

![仪表板库](media/howto-personalize-dashboard/dashboardeditor.png)

例如，您可以添加**的设备设置和属性**磁贴以显示一个所管理的设备的设置和属性值。 为此，请先选择“设备模板”，然后选择“设备实例”。 然后，为该磁贴标题和选择**设置**或**属性**显示。 以下屏幕截图显示**风扇速度**选择要添加到磁贴设置。 选择**完成**若要将更改保存到你的仪表板。

![带有设置和属性详细信息的“配置设备详细信息”窗体](media/howto-personalize-dashboard/dashboardsetting.png)

现在，当查看你的个人仪表板时，看到的新磁贴**风扇速度**为设备设置：

![带有显示的磁贴设置和属性的“仪表板”选项卡](media/howto-personalize-dashboard/personaldashboard.png)

你可以浏览库中其他磁贴类型，若要了解如何进一步自定义你的个人仪表板。

## <a name="manage-dashboards"></a>管理仪表板

可以有多个个人仪表板和它们之间进行切换，或选择默认应用程序仪表板：

![切换仪表板](media/howto-personalize-dashboard/switchdashboards.png)

可以编辑你的个人仪表板，并删除那些不再需要：

![删除仪表板](media/howto-personalize-dashboard/managedashboards.png)

## <a name="next-steps"></a>后续步骤

现在，已了解如何创建和管理个人仪表板，你可以：

> [!div class="nextstepaction"]
> [了解如何管理应用程序首选项](howto-manage-preferences.md)
