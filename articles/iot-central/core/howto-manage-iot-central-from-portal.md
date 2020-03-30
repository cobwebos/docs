---
title: 通过 Azure 门户管理 IoT Central | Microsoft Docs
description: 本文介绍如何通过 Azure 门户创建和管理 IoT Central 应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 02/11/2020
ms.topic: how-to
manager: philmea
ms.openlocfilehash: e7efda8efa27044168386e3ebbc557bf7fb74e8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157919"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>通过 Azure 门户管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

如果不在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建和管理 IoT Central 应用程序，可以使用 [Azure 门户](https://portal.azure.com)来管理应用程序。

## <a name="create-iot-central-applications"></a>创建 IoT Central 应用程序

要创建应用程序，请导航到[Azure 门户](https://ms.portal.azure.com)，然后选择 **"创建资源**"。

**在"搜索市场"** 栏中，键入*IoT 中心*：

![管理门户：搜索](media/howto-manage-iot-central-from-portal/image0a1.png)

在搜索结果中选择**IoT 中央应用程序**磁贴：

![管理门户：搜索结果](media/howto-manage-iot-central-from-portal/image0b1.png)

现在，选择 **"创建**"

![管理门户：IoT Central 资源](media/howto-manage-iot-central-from-portal/image0c1.png)

填写窗体中的所有字段。 此窗体与在 [Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站创建应用程序时需要填写的窗体类似。 有关详细信息，请参阅[创建 IoT Central 应用程序](quick-deploy-iot-central.md)快速入门。

![创建 IoT 中心窗体](media/howto-manage-iot-central-from-portal/image6a.png)

“位置”是你想要创建应用程序的[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)****。 通常，应选择物理上离设备最近的位置以获得最佳性能。 Azure IoT 中心目前**在澳大利亚、****亚太地区**、**欧洲**、**美国**、**英国****和日本**地区提供。 选择一个位置后，之后便不能将应用程序移到其他位置。

填写所有字段后，选择“创建”****。

## <a name="manage-existing-iot-central-applications"></a>管理现有 IoT Central 应用程序

如果已有 Azure IoT Central 应用程序，可将其删除或移动到 Azure 门户中的其他订阅或资源组。

> [!NOTE]
> 您无法在 Azure 门户中查看在免费定价计划上创建的应用程序，因为它们与订阅无关。

要开始，请选择门户中的所有**资源**。 选择 **"显示隐藏类型**"，然后**开始在"筛选器"中按名称**键入应用程序的名称以查找它。 然后选择想要管理的 IoT Central 应用程序。

要导航到应用程序，请选择**IoT 中央应用程序 URL**：

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image3.png)

若要将应用程序移动到其他资源组，请选择资源组旁边的“更改”****。 在 **"移动资源"** 页上，选择要将此应用程序移动到以下资源组：

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image4a.png)

要将应用程序移动到其他订阅，请选择订阅旁边的**更改**。 在 **"移动资源"** 页上，选择要将此应用程序移动到：

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 门户管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)