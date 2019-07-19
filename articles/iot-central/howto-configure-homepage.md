---
title: 配置 Azure IoT Central 应用程序仪表板 |Microsoft Docs
description: 作为生成器, 请了解如何配置默认的 Azure IoT Central 应用程序仪表板。
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 94ad51ac11687dfe060176132e2030d61b8d4ffc
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850213"
---
# <a name="configure-the-application-dashboard"></a>配置应用程序仪表板

**仪表板**是当有权访问应用程序的用户导航到应用程序的 URL 时所加载的页面。 如果你选择了**示例 Contoso**或**sample Devkits**应用程序模板来创建应用程序, 则你的应用程序将有一个预定义仪表板。 如果选择了 "**自定义应用**程序" 应用程序模板, 则仪表板为空白。

> [!NOTE]
> 用户还可以[创建他们自己的个人仪表盘](howto-personalize-dashboard.md), 而不是使用默认的应用程序仪表板。

## <a name="add-tiles"></a>添加磁贴

以下屏幕截图显示了通过**示例 Contoso**模板创建的应用程序中的仪表板。 若要自定义应用程序的默认仪表板, 请选择页面右上角的 "**编辑**"。

![基于 "示例 Contoso" 模板的应用程序仪表板](media/howto-configure-homepage/image1a.png)

选择 "**编辑**", 打开 "仪表板库" 面板。 该库包含可用于自定义仪表板的磁贴和仪表板基元。

![仪表板库](media/howto-configure-homepage/image2a.png)

例如, 你可以添加 "**设备设置" 和 "属性**" 磁贴, 以显示设备的当前设置和属性值的选择。 为此，请先选择“设备模板”  ，然后选择“设备实例”  。 之后，为磁贴添加标题，然后选择要显示的**设置**或**属性**。 以下屏幕截图显示了选定要添加到磁贴中的设置和属性。 选择 "**完成**" 以将更改保存到仪表板。

![带有设置和属性详细信息的“配置设备详细信息”窗体](media/howto-configure-homepage/image3a.png)

现在, 当操作员查看默认应用程序仪表板时, 他们将看到新的磁贴, 其中包含设备的 "**设置温度**" 设置:

![带有显示的磁贴设置和属性的“仪表板”选项卡](media/howto-configure-homepage/image4a.png)

可以浏览库中的其他磁贴类型, 以了解如何进一步自定义默认应用程序仪表板。

若要详细了解如何在 Azure IoT Central 中使用磁贴, 请参阅[使用仪表板磁贴](howto-use-tiles.md)。

## <a name="next-steps"></a>后续步骤

现在, 你已了解如何配置 Azure IoT Central 默认应用程序仪表板, 你可以:

> [!div class="nextstepaction"]
> [了解如何准备和上传图像](howto-prepare-images.md)
