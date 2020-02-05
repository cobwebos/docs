---
title: 通过 Azure 门户管理 IoT Central | Microsoft Docs
description: 本文介绍如何从 Azure 门户创建和管理 IoT Central 应用程序。
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 1e206d9a1b14fc8ff1b92e4eb7217824d81212cf
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990085"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>通过 Azure 门户管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

你可以使用[Azure 门户](https://portal.azure.com)来管理你的应用程序，而不是在[Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建和管理 IoT Central 应用程序。

## <a name="create-iot-central-applications"></a>创建 IoT Central 应用程序

若要创建应用程序，请导航到[Azure 门户](https://ms.portal.azure.com)，然后在左侧的主窗格中选择 "**创建资源**"。

![管理门户：导航菜单](media/howto-manage-iot-central-from-portal/image0.png)

在搜索栏中，键入“IoT Central”。

![管理门户：搜索](media/howto-manage-iot-central-from-portal/image0a1.png)

在搜索结果中选择 " **IoT Central 应用程序**" 行项。

![管理门户：搜索结果](media/howto-manage-iot-central-from-portal/image0b1.png)

现在选择“创建”。

![管理门户：IoT Central 资源](media/howto-manage-iot-central-from-portal/image0c1.png)

填写窗体中的所有字段。 此窗体类似于你填写在[Azure IoT Central 应用程序管理器](https://aka.ms/iotcentral)网站上创建应用程序的窗体。 有关详细信息，请参阅[创建 IoT Central 应用程序](quick-deploy-iot-central.md)快速入门。

可以通过选择 "**旧版应用**程序模板" 来创建 IoT Central 应用程序，该应用程序具有公开的功能，所有其他应用程序模板均使用公共预览功能。

![创建 IoT Central 窗体](media/howto-manage-iot-central-from-portal/image6a.png)

“位置”是你想要创建应用程序的[地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)。 通常，应选择物理上离设备最近的位置以获得最佳性能。 目前，美国、澳大利亚、亚太地区或欧洲提供 Azure IoT Central。  选择一个位置后，之后便不能将应用程序移到其他位置。

> [!NOTE]
> 预览应用程序模板当前仅在**欧洲**和**美国**位置提供。


填写所有字段后，选择 "**创建**"。

## <a name="manage-existing-iot-central-applications"></a>管理现有 IoT Central 应用程序

如果已有 Azure IoT Central 应用程序，可将其删除或移动到 Azure 门户中的其他订阅或资源组。

> [!NOTE]
> 无法查看 Azure 门户中的免费定价计划创建的应用程序，因为它们不与你的订阅相关联。

若要开始，请在左侧窗格中选择 "**所有资源**"。 在搜索框中键入应用程序的名称以在资源列表中将其找到。 然后选择要管理的 IoT Central 应用程序。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image2a.png)

若要导航到应用程序，请选择 "IoT Central 应用程序 URL"。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image3.png)

若要将应用程序移动到其他资源组，请选择资源组旁边的 "**更改**"。 在“移动资源”页上，选取要将此应用程序迁移到其中的资源组。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image4a.png)

若要将应用程序移动到其他订阅，请选择订阅旁边的 "**更改**" 链接。 在显示的对话框中，选取要将此应用程序迁移到其中的订阅。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 门户管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)