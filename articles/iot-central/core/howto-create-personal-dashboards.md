---
title: 创建 Azure IoT Central 个人仪表板 |Microsoft Docs
description: 作为用户，了解如何创建和管理你的个人仪表板。
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 9da7efad816a466eb9d2902e36a95c5ae0fa626b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950660"
---
# <a name="create-and-manage-multiple-dashboards"></a>创建和管理多个仪表板

**仪表板**是第一次导航到应用程序时加载的页面。 应用程序中的**生成器**为所有用户定义默认的应用程序仪表板。 你可以将此默认仪表板替换为你自己的个性化应用程序仪表板。 您可以有多个仪表板，用于显示不同的数据并在它们之间切换。 

如果你是应用程序的**管理员**，则还可以创建最多10个应用程序级别仪表板，以便与应用程序的其他用户共享。 只有**管理员**能够创建、编辑和删除应用程序级别的仪表板。 

## <a name="create-dashboard"></a>创建仪表板

以下屏幕截图显示了从**自定义应用程序**模板创建的应用程序中的仪表板。 可以将默认的应用程序仪表板替换为个人仪表板，如果你是管理员，则可以使用其他应用程序级别仪表板。 为此，请选择页面左上角的 " **+ 新建**"。
 
> [!div class="mx-imgBorder"]
> 基于 "自定义应用程序" 模板的应用程序 ![仪表板](media/howto-create-personal-dashboards/dashboard-custom-app.png)

选择 " **+ 新建**" 将打开仪表板编辑器。 在编辑器中，你可以向仪表板命名并从库中选择项。 该库包含可用于自定义仪表板的磁贴和仪表板基元。

> [!div class="mx-imgBorder"]
> ![仪表板库](media/howto-create-personal-dashboards/dashboard-library.png)

如果你是应用程序的**管理员**，则会向你提供一个选项，用于在要创建个人级仪表板或应用程序级别仪表板时进行切换。 如果你创建了个人级别仪表板，则只有你才能看到它。 如果创建应用程序级别仪表板，则该应用程序的每个用户都可以看到它。 输入标题并选择要创建的仪表板类型后，可以在以后保存和添加磁贴。 或者，如果你现在已准备就绪，并且已添加设备模板和设备实例，你可以继续创建第一个磁贴。 

> [!div class="mx-imgBorder"]
> ![配置设备详细信息 "窗体，其中包含温度](media/howto-create-personal-dashboards/device-details.png) 的详细信息

例如，可以为设备的当前温度添加**遥测**磁贴。 为此，请执行以下操作：
1. 选择**设备模板**
1. 选择要在仪表板磁贴上查看的设备的**设备实例**。 然后，会看到可在磁贴上使用的设备属性的列表。
1. 若要在仪表板上创建磁贴，请单击 "**温度**" 并将其拖到 "仪表板" 区域。 还可以单击 "**温度**" 旁的复选框，然后单击 "**合并**"。 以下屏幕截图显示了如何选择设备模板和设备实例，然后在仪表板上创建 "温度遥测" 磁贴。
1. 选择左上角的 "**保存**"，将磁贴保存到仪表板。

> [!div class="mx-imgBorder"]
> ![仪表板 "选项卡，其中包含温度磁贴的详细信息](media/howto-create-personal-dashboards/temperature-tile-edit.png)

现在，当你查看你的个人仪表板时，将看到新的磁贴，其中包含设备的**温度**设置：

> [!div class="mx-imgBorder"]
> ![仪表板 "选项卡，其中包含温度磁贴的详细信息](media/howto-create-personal-dashboards/temperature-tile-complete.png)

你可以浏览库中的其他磁贴类型，以了解如何进一步自定义你的个人仪表板。

若要详细了解如何在 Azure IoT Central 中使用磁贴，请参阅[将磁贴添加到仪表板](howto-add-tiles-to-your-dashboard.md)。

## <a name="manage-dashboards"></a>管理仪表板

你可以有多个个人仪表板，并在它们之间进行切换，或从默认的应用程序仪表板中进行选择：

> [!div class="mx-imgBorder"]
> 在仪表板之间 ![切换](media/howto-create-personal-dashboards/switch-dashboards.png)

你可以编辑你的个人仪表板，并删除不再需要的任何仪表板。 如果你是**管理员**，还可以编辑或删除应用程序级别的仪表板。

> [!div class="mx-imgBorder"]
> ![删除仪表板](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>后续步骤

现在，你已了解如何创建和管理个人仪表盘，接下来可以[了解如何管理你的应用程序首选项](howto-manage-preferences.md)
