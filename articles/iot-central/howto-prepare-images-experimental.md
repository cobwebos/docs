---
title: 将图像上传到 Azure IoT Central 应用程序 | Microsoft Docs
description: 了解如何以开发者的身份准备图像并将其上传到 Azure IoT Central 应用程序。
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: b975d7dccc85973a42408d87e3c03a91aaf1c450
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812746"
---
# <a name="prepare-and-upload-images-to-your-azure-iot-central-application"></a>准备图像并将其上传到 Azure IoT Central 应用程序

本文介绍如何以开发者的身份上传自定义图像，以便对 Azure IoT Central 应用程序进行自定义。 例如，可以使用设备图像自定义设备仪表板。

## <a name="before-you-begin"></a>开始之前

若要完成本文中的步骤，需要以下各项：

1. Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)。
1. 用于缩放图像文件和重设其大小的工具。

## <a name="choose-where-to-use-custom-images"></a>选择在何处使用自定义图像

可以将自定义图像添加到 Azure IoT Central 应用程序中的以下位置：

* “应用程序管理器”页

    ![“应用程序管理器”页上的图像](media/howto-prepare-images-experimental/applicationmanager.png)

* 主页

    ![主页上的图像](media/howto-prepare-images-experimental/homepage.png)

* 设备模板

    ![设备模板上的图像](media/howto-prepare-images-experimental/devicetemplate.png)

* 设备仪表板上的磁贴

    ![设备磁贴上的图像](media/howto-prepare-images-experimental/devicetile.png)

* 设备集仪表板上的磁贴

    ![设备集磁贴上的图像](media/howto-prepare-images-experimental/devicesettile.png)

## <a name="prepare-the-images"></a>准备图像

在所有四个位置中，可以使用 PNG、GIF 或 JPEG 图像。

下表汇总了可以使用的图像大小：

| 位置 | 大小 |
| -------- | ------ |
| 应用程序管理器 | 268x160 像素 |
| 设备模板 | 64x64 像素 |
| 主页和仪表板磁贴 | 最小的磁贴为 200x200 像素，较大的磁贴可以是由多个小磁贴组成的正方形或矩形。 例如，200x400 像素、400x200 像素或 400x400 像素 |

若要确保在应用程序中的最佳显示效果，创建的图像应与上表中显示的尺寸匹配。

## <a name="upload-the-images"></a>上传图像

以下各部分介绍了如何在不同的位置上传图像：

### <a name="application-manager"></a>应用程序管理器

若要在**应用程序管理器**中上传图像，请导航到“管理”部分的“应用程序设置”页。 必须是管理员才能完成此任务：

![上传应用程序图像](media/howto-prepare-images-experimental/uploadapplicationmanager.png)

单击“应用程序图像”磁贴以从本地计算机上传你准备的图像 (268x160 px)。

### <a name="home-page"></a>主页

若要在主页中上传图像，请导航到应用程序的**主页**，然后单击“编辑”。 必须是开发者才能完成此任务：

![上传主页图像](media/howto-prepare-images-experimental/uploadhomepage.png)

在“配置图像”下，单击“图像”磁贴以从本地计算机上传你准备的图像。 最小的磁贴为 200x200 像素，较大的磁贴可以是由多个小磁贴组成的正方形或矩形。 例如，200x400 像素、400x200 像素或 400x400 像素。

**保存**所上传的图像。 你可以在编辑模式下调整图像大小。 结束后，请单击“完成”。 

### <a name="device-template"></a>设备模板

若要将图像上传到设备模板中，请导航到“设备模板”，选择设备模板。 必须是开发者才能完成此任务：

![上传设备模板图像](media/howto-prepare-images-experimental/uploaddevicetemplate.png)

单击“图像”磁贴以从本地计算机上传你准备的图像 (64x64 px)。 

### <a name="device-dashboard"></a>设备仪表板

若要将图像上传到设备仪表板中，请导航到“设备模板”，选择设备模板。 然后选择“仪表板”选项卡。必须是开发者才能完成此任务：

![上传设备仪表板图像](media/howto-prepare-images-experimental/uploaddevicedashboard.png)

在“配置图像”下，单击“图像”磁贴，然后选择要从本地计算机上传的文件。 最小的磁贴为 200x200 像素，较大的磁贴可以是由多个小磁贴组成的正方形或矩形。 例如，200x400 像素、400x200 像素或 400x400 像素。

**保存**所上传的图像。 你可以在编辑模式下调整图像大小及其位置。 结束后，请单击“完成”。

### <a name="device-set-dashboard"></a>设备集仪表板

若要在设备集仪表板中上传图像，请导航到“设备集”，选择设备集和设备。 然后选择“仪表板”页面并单击“编辑”：

![上传设备集仪表板图像](media/howto-prepare-images-experimental/uploaddevicesetdashboard.png)

在“配置图像”下，单击“图像”磁贴以从本地计算机上传你准备的图像。 最小的磁贴为 200x200 像素，较大的磁贴可以是由多个小磁贴组成的正方形或矩形。 例如，200x400 像素、400x200 像素或 400x400 像素。

**保存**所上传的图像。 你可以在编辑模式下调整图像大小及其位置。 结束后，请单击“完成”。

## <a name="next-steps"></a>后续步骤

了解如何准备图像并将其上传到 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [在 Azure IoT Central 应用程序中管理设备](howto-manage-devices-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)