---
title: 将图像上传到 Azure IoT Central 应用程序 | Microsoft Docs
description: 了解如何以开发者的身份准备图像并将其上传到 Azure IoT Central 应用程序。
services: iot-central
author: tanmaybhagwat
ms.author: tanmayb
ms.date: 04/16/2018
ms.topic: article
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: a43f2dd780604235ada1d8e3ae8a20563042fbaa
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200225"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>准备图像并将其上传到 Azure IoT Central 应用程序

本文介绍如何以开发者的身份上传自定义图像，以便对 Microsoft Azure IoT Central 应用程序进行自定义。 例如，可以使用设备图像自定义设备仪表板。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下各项：

1. Azure IoT Central 应用程序。 有关详细信息，请参阅[创建 Azure IoT Central 应用程序](howto-create-application.md)。
1. 用于缩放图像文件和重设其大小的工具。

## <a name="choose-where-to-use-custom-images"></a>选择在何处使用自定义图像

可以将自定义图像添加到 Azure IoT Central 应用程序中的以下位置：

* “应用程序管理器”页

    ![“应用程序管理器”页上的图像](media/howto-prepare-images/applicationmanager.png)

* 主页

    ![主页上的图像](media/howto-prepare-images/homepage.png)

* 设备模板

    ![设备模板上的图像](media/howto-prepare-images/devicetemplate.png)

* 设备仪表板上的磁贴

    ![设备磁贴上的图像](media/howto-prepare-images/devicetile.png)

* 设备集仪表板上的磁贴

    ![设备集磁贴上的图像](media/howto-prepare-images/devicesettile.png)

## <a name="prepare-the-images"></a>准备图像

在所有四个位置中，可以使用 PNG、GIF 或 JPEG 图像。

下表汇总了可以使用的图像大小：

| Location | 大小 |
| -------- | ------ |
| **应用程序管理器** | 268x160 像素 |
| 设备模板 | 64x64 像素 |
| 主页和仪表板磁贴 | 最小的磁贴为 200x200 像素，较大的磁贴可以是由多个小磁贴组成的正方形或矩形。 例如，200x400 像素、400x200 像素或 400x400 像素 |

若要确保在应用程序中的最佳显示效果，创建的图像应与上表中显示的尺寸匹配。

## <a name="upload-the-images"></a>上传图像

以下部分介绍如何上传在不同位置使用的图像：

### <a name="application-manager"></a>应用程序管理器

若要上传在**应用程序管理器**上使用的图像，请导航到“管理”部分的“应用程序设置”页。 必须是管理员才能完成此任务：

![上传应用程序图像](media/howto-prepare-images/uploadapplicationmanager.png)

单击“上传图像”，然后选择要从本地计算机上传的文件。

### <a name="home-page"></a>主页

若要上传在主页上使用的图像，请导航到应用程序的“主页”，然后将设计模式切换到“启用”。 必须是开发者才能完成此任务：

![上传主页图像](media/howto-prepare-images/uploadhomepage.png)

单击“上传图像”，然后选择要从本地计算机上传的文件。

图像上传以后，可以在设计模式切换到“启用”的情况下重设图像大小。

### <a name="device-template"></a>设备模板

若要上传在设备模板上使用的图像，请导航到“设备资源管理器”，选择设备模板和设备，然后将设计模式切换到“启用”。 必须是开发者才能完成此任务：

![上传设备模板图像](media/howto-prepare-images/uploaddevicetemplate.png)

单击“上传图像”，然后选择要从本地计算机上传的文件。

### <a name="device-dashboard"></a>设备仪表板

若要上传在设备仪表板上使用的图像，请导航到“设备资源管理器”，选择设备模板和设备。 然后选择“仪表板”页并将设计模式切换到“启用”。 必须是开发者才能完成此任务：

![上传设备仪表板图像](media/howto-prepare-images/uploaddevicedashboard.png)

单击“上传图像”，然后选择要从本地计算机上传的文件。

图像上传以后，可以在“设计模式”切换到“启用”的情况下重设图像大小和重调图像位置。

### <a name="device-set-dashboard"></a>设备集仪表板

若要上传在设备集仪表板上使用的图像，请导航到“设备集”，选择设备集和设备。 然后选择“仪表板”页并将“设计模式”切换到“启用”：

![上传设备集仪表板图像](media/howto-prepare-images/uploaddevicesetdashboard.png)

单击“上传图像”，然后选择要从本地计算机上传的文件。

图像上传以后，可以在设计模式切换到“启用”的情况下重设图像大小和重调图像位置。

## <a name="next-steps"></a>后续步骤

了解如何准备图像并将其上传到 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [在 Azure IoT Central 应用程序中管理设备](howto-manage-devices.md)