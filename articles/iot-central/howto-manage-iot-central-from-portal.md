---
title: 通过 Azure 门户管理 IoT Central | Microsoft Docs
description: 通过 Azure 门户管理 IoT Central。
services: iot-central
ms.service: iot-central
author: tbhagwat3
ms.author: tanmayb
ms.date: 08/30/2018
ms.topic: conceptual
manager: peterpr
ms.openlocfilehash: 20b1c7500587324f6f7dbb5cc679a3603eff56bd
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2018
ms.locfileid: "52963821"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>通过 Azure 门户管理 IoT Central 
除了从 IoT Central 网站创建和管理 IoT Central 应用程序外，还可通过 Azure 门户管理 IoT Central。 本文将完整介绍可实现的操作以及实现方法。

## <a name="create-iot-central-applications"></a>创建 IoT Central 应用程序
要创建新应用程序，请导航到 [Azure 门户](https://ms.portal.azure.com)，单击左侧主导航菜单中的“创建资源”。 

![管理门户：导航菜单](media/howto-manage-iot-central-from-portal/image0.png)

在搜索栏中，键入“IoT Central”一词。

![管理门户：搜索](media/howto-manage-iot-central-from-portal/image0a.png)

单击搜索结果中的“IoT Central 应用程序”行项。

![管理门户：搜索结果](media/howto-manage-iot-central-from-portal/image0b.png)

现在，单击“创建”按钮，查看需要填写的窗体。

![管理门户：IoT Central 资源](media/howto-manage-iot-central-from-portal/image0c.png)

填写窗体中的所有字段。 此窗体与从 IoT Central 网站创建应用程序时需要填写的窗体类似。 若要了解有关如何填写每个字段的详细信息，请查看[创建 IoT Central 应用程序](quick-deploy-iot-central.md)快速入门。 

![管理门户：创建 IoT Central 资源](media/howto-manage-iot-central-from-portal/image1.png)  

填写所有字段后，单击“创建”按钮。

## <a name="manage-existing-iot-central-applications"></a>管理现有 IoT Central 应用程序
如果已有 Azure IoT Central 应用程序，可将其删除或移动到 Azure 门户中的其他订阅或资源组。 不能在 Azure 门户中看到试用版应用程序，因为没有订阅对这些试用版提供支持。

要想开始，请单击左侧主导航菜单中的“所有资源”。 在搜索框中键入应用程序的名称并在资源列表中将其找到。 然后单击想要管理的 IoT Central 应用程序。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image2.png)

要想导航到应用程序，请单击“IoT Central 应用程序 URL”。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image3.png)

若要将应用程序移动到其他资源组，请单击“资源组”旁边的“更改”链接。 在显示的对话框中，选取要将此应用程序迁移到其中的资源组。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image4.png)

若要将应用程序移动到其他订阅，请单击“订阅”旁边的“更改”链接。 在显示的对话框中，选取要将此应用程序迁移到其中的订阅。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image5.png)

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 门户管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)