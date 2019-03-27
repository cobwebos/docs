---
title: 创建 Azure IoT Central 应用程序 | Microsoft Docs
description: 创建新的 Azure IoT Central 应用程序。 使用应用程序模板创建试用版或即用即付应用程序。
author: viv-liu
ms.author: viviali
ms.date: 02/20/2019
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 5df798f0f5f9588b1f2cd792e99d6409e565337e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098070"
---
# <a name="create-an-azure-iot-central-application"></a>创建 Azure IoT Central 应用程序

构建人员可以使用 Azure IoT Central UI 来定义 Microsoft Azure IoT Central 应用程序。 本快速入门介绍如何创建一个包含示例设备模板和模拟设备的 Azure IoT Central 应用程序。

导航到 Azure IoT Central 的[应用程序管理器](https://aka.ms/iotcentral)页。 需要使用 Microsoft 个人、工作或学校帐户登录。

若要开始创建新的 Azure IoT Central 应用程序，请选择“新建应用程序”。 此时将转到“创建应用程序”页。

![Azure IoT Central 的“创建应用程序”页](media/quick-deploy-iot-central/iotcentralcreate.png)

若要创建新的 Azure IoT Central 应用程序：

1. 选择付款计划：
   - **试用版**应用程序免费 7 天，然后过期。 它们可以在到期之前随时转换为即用即付。
   - **即用即付**应用程序按设备收费，前 5 台设备免费。

     可在 [Azure IoT Central 定价页](https://azure.microsoft.com/pricing/details/iot-central/)上了解定价详细信息。

1. 选择一个友好的应用程序名称，例如 **Contoso IoT**。 Azure IoT Central 将会生成唯一的 URL 前缀。 可将此 URL 前缀更改为更容易记住的内容。

1. 选择应用程序模板。 应用程序模板可能包含入门所需的预定义项目（例如设备模板和仪表板）。

    | 应用程序模板 | 说明 |
    | -------------------- | ----------- |
    | 示例 Contoso       | 创建一个应用程序，其中包含已为冷藏食品贩卖机创建的设备模板。 通过此模板来完成 Azure IoT Central 的入门。 |
    | 示例 Devkit       | 创建一个应用程序，其中的设备模板可以用来连接 MXChip 或 Raspberry Pi 设备。 如果你是在这其中任一设备上进行试验的设备开发人员，请使用此模板。 |
    | 自定义应用程序   | 创建一个空的应用程序，以便在其中填充你自己的设备模板和设备。 |

1. 若要创建**即用即付**应用程序，需要选择“目录”、“Azure 订阅”和“区域”。 

   - 目录是用于创建应用程序的 Azure Active Directory (AD)。 它包含用户标识、凭据和其他组织信息。 如果没有 Azure AD，则在创建 Azure 订阅时，系统会为你创建一个 Azure AD。

   - 有了 *Azure 订阅*，就可以创建 Azure 服务的实例。 IoT Central 将在订阅中预配资源。 如果没有 Azure 订阅，则可在 [Azure 注册页](https://aka.ms/createazuresubscription)上创建一个。 创建 Azure 订阅以后，请导航回“创建应用程序”页。 新订阅显示在“Azue 订阅”下拉列表中。

   - *区域*是你想要创建应用程序的物理位置。 通常，你应选择物理上距离设备最近的区域以获得最佳性能。你可以在[可用产品(按区域)](https://azure.microsoft.com/regions/services/)页面上查看提供 Azure IoT Central 的区域。

     > [!Note]
     > 选择一个区域后，就不能在以后将应用程序移到其他区域。

1. 选择“创建”。

## <a name="next-steps"></a>后续步骤

在本快速入门中，创建了 IoT Central 应用程序。 下面是建议的后续步骤：

> [!div class="nextstepaction"]
> [浏览一下 IoT Central](overview-iot-central-tour.md)
