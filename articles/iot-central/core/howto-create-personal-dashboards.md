---
title: 创建 Azure IoT Central 个人仪表板 | Microsoft Docs
description: 以用户身份了解如何创建和管理个人仪表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f7dca7352a49e668231f64632371a445985e4de3
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83634669"
---
# <a name="create-and-manage-multiple-dashboards"></a>创建和管理多个仪表板

仪表板是第一次导航到应用程序时所加载的页面。 应用程序中的生成器为所有用户定义默认的应用程序仪表板。 此外，还可以创建自己的个性化应用程序仪表板。 可以设置多个仪表板，用于显示不同的数据，并可以在它们之间进行切换。

如果你是应用程序的管理员，还可以创建最多 10 个可与应用程序的其他用户共享的应用程序级别的仪表板。 只有管理员能够创建、编辑和删除应用程序级别的仪表板。  

## <a name="create-dashboard"></a>创建仪表板

以下屏幕截图显示从“自定义应用程序”模板创建的应用程序中的仪表板。 可以将默认的应用程序仪表板替换为个人仪表板，如果你是管理员，可以将其替换为应用程序级别的其他仪表板。 为此，可选择页面左上角“+新建”。

> [!div class="mx-imgBorder"]
> ![基于“自定义应用程序”模板的应用程序仪表板](media/howto-create-personal-dashboards/dashboard-custom-app.png)

选择“+新建”后会打开仪表板编辑器。 在编辑器中，可以为仪表板命名并从库中选择项。 库包含可用于自定义仪表板的磁贴和仪表板基元。

> [!div class="mx-imgBorder"]
> ![仪表板库](media/howto-create-personal-dashboards/dashboard-library.png)

如果你是应用程序的管理员，可以选择创建个人级仪表板或应用程序级别仪表板。 如果你创建了个人级别仪表板，只有你才能看到它。 如果你创建了应用程序级别仪表板，该应用程序的每个用户都可以看到它。 输入标题并选择要创建的仪表板类型后，可以保存并在以后添加磁贴。 如果你现在已准备就绪，并且已添加设备模板和设备实例，可以继续操作，创建第一个磁贴。  

> [!div class="mx-imgBorder"]
> ![“配置设备详细信息”窗体，其中包含“温度”的详细信息](media/howto-create-personal-dashboards/device-details.png)

例如，可以添加用于设备当前温度的“遥测”磁贴。 为此，请执行以下操作：

1. 选择“设备模板”
1. 从“设备”中选择要在仪表板磁贴上查看的设备。 然后，你将看到可在磁贴上使用的设备属性的列表。
1. 要在仪表板上创建磁贴，请单击“温度”并将它拖动到仪表板区域。 还可以单击“温度”旁的复选框，然后单击“添加磁贴” 。 以下屏幕截图显示了选择设备模板和设备，然后在仪表板上创建“温度遥测”磁贴的操作。
1. 选择左上角的“保存”以将所做更改保存到仪表板。

> [!div class="mx-imgBorder"]
> ![“仪表板”选项卡，其中包含“温度”磁贴的详细信息](media/howto-create-personal-dashboards/temperature-tile-edit.png)

现在，当查看个人仪表板时，会看到新的磁贴，其中包含设备的“温度”设置：

> [!div class="mx-imgBorder"]
> ![“仪表板”选项卡，其中包含“温度”磁贴的详细信息](media/howto-create-personal-dashboards/temperature-tile-complete.png)

可以浏览库中的其他磁贴类型，以了解如何进一步自定义个人仪表板。

若要详细了解如何在 Azure IoT Central 中使用磁贴，请参阅[向仪表板添加磁贴](howto-add-tiles-to-your-dashboard.md)。

## <a name="manage-dashboards"></a>管理仪表板

可以设置多个个人仪表板，并在它们之间进行切换，或从默认的应用程序仪表板中进行选择：

> [!div class="mx-imgBorder"]
> ![切换仪表板](media/howto-create-personal-dashboards/switch-dashboards.png)

可以编辑个人仪表板，并删除任何不再需要的仪表板。 如果你是管理员，还可以编辑或删除应用程序级别的仪表板。

> [!div class="mx-imgBorder"]
> ![删除仪表板](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>后续步骤

现在，你已了解如何创建和管理个人仪表板，接下来可以[了解如何管理应用程序首选项](howto-manage-preferences.md)。
