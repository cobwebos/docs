---
title: 配置 Azure IoT Central 应用程序的主页 | Microsoft Docs
description: 作为构建者，了解如何配置 Azure IoT Central 应用程序的主页。
author: dominicbetts
ms.author: dobett
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 599616f046b2eccee009e8a08e8a2f51cf968644
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55771778"
---
# <a name="configuring-homepage"></a>配置主页

主页是当有权访问应用程序的用户导航到应用程序的 URL 时加载的页面。 如果在创建应用程序时选择了“示例 Contoso”或“示例 Devkits”应用程序模板，则应用程序将具有预定义的主页。 另一方面，如果选择了“自定义应用程序”应用程序模板，则主页将为空白。

## <a name="add-tiles"></a>添加磁贴

例如，下面是基于“示例 Contoso”模板的应用程序的主页。 若要为应用程序自定义主页，请先在右上角选择“编辑”。 

![基于“示例 Contoso”模板的应用程序的主页](media/howto-configure-homepage-experimental/image1.png)

选择“编辑”将在左侧面板中打开仪表板库。 可以添加许多类型的磁贴和仪表板基元来自定义主页。

![仪表板库](media/howto-configure-homepage-experimental/image2.png)

例如，可以添加“设置和属性”磁贴，以显示所选的当前设置和属性值。 为此，请先选择“设备模板”，然后选择“设备实例”。 之后，为磁贴添加标题，然后选择要显示的**设置**或**属性**。 在此示例中，我们已选择了“设置温度”。 单击“完成”将导致此磁贴显示在主页上。

![带有设置和属性详细信息的“配置设备详细信息”窗体](media/howto-configure-homepage-experimental/image3.png)

现在，当操作员查看主页时，可以看到此磁贴，其中显示了设备的属性或设置：

![带有显示的磁贴设置和属性的“仪表板”选项卡](media/howto-configure-homepage-experimental/image4.png)

使用库中的各种其他磁贴类型来发现如何更深入地自定义应用程序的主页。

## <a name="next-steps"></a>后续步骤

现在你已了解了如何配置 Azure IoT Central 主页，你可以：

> [!div class="nextstepaction"]
> [了解如何准备和上传图像](howto-prepare-images-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json)
