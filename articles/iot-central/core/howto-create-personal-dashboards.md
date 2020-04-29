---
title: 创建 Azure IoT Central 个人仪表板 | Microsoft Docs
description: 本文为用户介绍如何创建和管理个人仪表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 06225e284000d7f10f575be08cd683488abec339
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80985484"
---
# <a name="create-and-manage-multiple-dashboards"></a>创建和管理多个仪表板

**仪表板**是首次导航到应用程序时加载的页面。 应用程序中的**生成器**为所有用户定义默认的应用程序仪表板。 此外，还可以创建自己的个性化应用程序仪表板。 可以创建多个仪表板用于显示不同的数据，并可以切换这些仪表板。

应用程序的**管理员**还可以创建最多 10 个应用程序级别的仪表板并将其与应用程序的其他用户共享。 只有**管理员**能够创建、编辑和删除应用程序级别的仪表板。 

## <a name="create-dashboard"></a>创建仪表板

以下屏幕截图显示了从**自定义应用程序**模板创建的应用程序中的仪表板。 可将默认的应用程序仪表板替换为个人仪表板，而管理员还可将其替换为另一个应用程序级别的仪表板。 为此，请选择页面左上方的“+ 新建”。 
 
> [!div class="mx-imgBorder"]
> ![基于“自定义应用程序”模板的应用程序的仪表板](media/howto-create-personal-dashboards/dashboard-custom-app.png)

选择“+ 新建”会打开仪表板编辑器。  在编辑器中，可以指定仪表板的名称并选择库中的项。 该库包含可用于自定义仪表板的磁贴和仪表板基元。

> [!div class="mx-imgBorder"]
> ![仪表板库](media/howto-create-personal-dashboards/dashboard-library.png)

应用程序的**管理员**可以使用相应的切换选项来指定是要创建个人级别的仪表板，还是应用程序级别的仪表板。 如果你创建个人级别的仪表板，则只有你能够看到它。 如果创建应用程序级别的仪表板，该应用程序的每个用户都可以看到它。 输入标题并选择要创建的仪表板类型后，可以先保存，以后再添加磁贴。 或者，如果你已准备就绪并且已添加设备模板和设备实例，则可以继续创建第一个磁贴。 

> [!div class="mx-imgBorder"]
> ![包含温度详细信息的“配置设备详细信息”窗体](media/howto-create-personal-dashboards/device-details.png)

例如，可为设备的当前温度添加一个“遥测”磁贴。  为此，请执行以下操作：
1. 选择一个**设备模板**
1. 选择要在仪表板磁贴上显示的设备的**设备实例**。 然后，会看到可在磁贴中使用的设备属性列表。
1. 若要在仪表板上创建磁贴，请单击“温度”并将其拖放到仪表板区域。  也可以单击“温度”旁边的复选框，然后单击“组合”。   以下屏幕截图显示了用户选择设备模板和设备实例，然后在仪表板上创建“温度遥测”磁贴。
1. 选择左上角的“保存”，将磁贴保存到仪表板。 

> [!div class="mx-imgBorder"]
> ![包含“温度”磁贴详细信息的“仪表板”选项卡](media/howto-create-personal-dashboards/temperature-tile-edit.png)

现在，在查看个人仪表板时，将会看到新的磁贴，其中包含设备的“温度”设置： 

> [!div class="mx-imgBorder"]
> ![包含“温度”磁贴详细信息的“仪表板”选项卡](media/howto-create-personal-dashboards/temperature-tile-complete.png)

可以浏览库中的其他磁贴类型，以探索如何进一步自定义个人仪表板。

若要详细了解如何在 Azure IoT Central 中使用磁贴，请参阅[将磁贴添加到仪表板](howto-add-tiles-to-your-dashboard.md)。

## <a name="manage-dashboards"></a>管理仪表板

可以创建多个个人仪表板并在它们之间进行切换，或者从某个默认应用程序仪表板中进行选择：

> [!div class="mx-imgBorder"]
> ![切换仪表板](media/howto-create-personal-dashboards/switch-dashboards.png)

可以编辑个人仪表板，并删除不再需要的任何仪表板。 **管理员**还可以编辑或删除应用程序级别的仪表板。

> [!div class="mx-imgBorder"]
> ![删除仪表板](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>后续步骤

了解如何创建和管理个人仪表板后，接下来可以[了解如何管理应用程序首选项](howto-manage-preferences.md)
