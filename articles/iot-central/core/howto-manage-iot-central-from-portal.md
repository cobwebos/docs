---
title: 通过 Azure 门户管理 IoT Central | Microsoft Docs
description: This article describes how to create and manage your IoT Central applications from the Azure portal.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 10/02/2019
ms.topic: conceptual
manager: philmea
ms.openlocfilehash: 2133819ce7c298e2f73fdc5a68b80b64f9e72ea7
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74480430"
---
# <a name="manage-iot-central-from-the-azure-portal"></a>通过 Azure 门户管理 IoT Central

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Instead of creating and managing IoT Central applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website, you can use the [Azure portal](https://portal.azure.com) to manage your applications.

## <a name="create-iot-central-applications"></a>创建 IoT Central 应用程序

To create an application, navigate to the [Azure portal](https://ms.portal.azure.com) and select **Create a resource** in the main pane on the left.

![管理门户：导航菜单](media/howto-manage-iot-central-from-portal/image0.png)

在搜索栏中，键入“IoT Central”。

![管理门户：搜索](media/howto-manage-iot-central-from-portal/image0a1.png)

Select the **IoT Central Application** line-item in the search results.

![管理门户：搜索结果](media/howto-manage-iot-central-from-portal/image0b1.png)

现在选择“创建”。

![管理门户：IoT Central 资源](media/howto-manage-iot-central-from-portal/image0c1.png)

填写窗体中的所有字段。 This form is similar to the form you fill out to create applications on the [Azure IoT Central application manager](https://aka.ms/iotcentral) website. 有关详细信息，请参阅[创建 IoT Central 应用程序](quick-deploy-iot-central.md)快速入门。

You can create IoT Central application with general features by selecting **Sample Contoso**, **Custom application** and **Sample Devkits** as application templates, all the other application templates uses public preview features.

![create IoT Central form](media/howto-manage-iot-central-from-portal/image6a.png)

**Location** is the [geography](https://azure.microsoft.com/global-infrastructure/geographies/) where you’d like to create your application. Typically, you should choose the location that's physically closest to your devices to get optimal performance. Azure IoT Central is currently available in the **United States**, **Australia**, **Asia Pacific**, or in **Europe**.  Once you choose a location, you can't move your application to a different location later.

> [!NOTE]
> The preview application templates are currently only available in the **Europe** and **United States** locations.

![管理门户：创建 IoT Central 资源](media/howto-manage-iot-central-from-portal/image1a.png)  

After filling out all fields, select **Create**.

## <a name="manage-existing-iot-central-applications"></a>管理现有 IoT Central 应用程序

如果已有 Azure IoT Central 应用程序，可将其删除或移动到 Azure 门户中的其他订阅或资源组。

> [!NOTE]
> Azure 门户中不会显示试用版应用程序，因为它们与订阅无关。

To get started, select **All resources** in the main pane on the left. 在搜索框中键入应用程序的名称以在资源列表中将其找到。 Then select the IoT Central application you'd like to manage.

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image2a.png)

To navigate to the application, select the IoT Central Application URL.

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image3.png)

To move the application to a different resource group, select **change** beside the resource group. 在“移动资源”页上，选取要将此应用程序迁移到其中的资源组。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image4a.png)

To move the application to a different subscription, select the **change** link beside the subscription. 在显示的对话框中，选取要将此应用程序迁移到其中的订阅。

![管理门户：资源管理](media/howto-manage-iot-central-from-portal/image5a.png)

## <a name="next-steps"></a>后续步骤

了解如何通过 Azure 门户管理 Azure IoT Central 应用程序后，建议接下来执行以下步骤：

> [!div class="nextstepaction"]
> [管理应用程序](howto-administer.md)