---
title: 创建 Azure IoT Central 应用程序 | Microsoft Docs
description: 创建一个新的 Azure IoT Central 应用程序用于管理冷藏食品贩卖设备。 查看模拟设备生成的遥测数据。
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 0199efbc1456206c67efb846b4381cae333e3749
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2018
ms.locfileid: "49309036"
---
# <a name="create-an-azure-iot-central-application"></a>创建 Azure IoT Central 应用程序

构建人员可以使用 Azure IoT Central UI 来定义 Microsoft Azure IoT Central 应用程序。 本快速入门介绍如何创建一个包含示例设备模板和模拟设备的 Azure IoT Central 应用程序。

## <a name="create-the-application"></a>创建应用程序

若要完成本快速入门，需要基于“Contoso 示例”应用程序模板创建一个 Azure IoT Central 应用程序。

导航到 Azure IoT Central 的[应用程序管理器](https://aka.ms/iotcentral)页。 输入用于访问 Azure 订阅的电子邮件地址和密码：

![输入组织帐户](media/quick-deploy-iot-central/sign-in.png)

若要开始创建新的 Azure IoT Central 应用程序，请选择“新建应用程序”：

![Azure IoT Central 的“应用程序管理器”页](media/quick-deploy-iot-central/iotcentralhome.png)

若要创建新的 Azure IoT Central 应用程序：

1. 选择“免费试用应用程序”付款计划。
1. 选择一个友好的应用程序名称，例如 **Contoso IoT**。 Azure IoT Central 将会生成唯一的 URL 前缀。 可将此 URL 前缀更改为更容易记住的内容。
1. 选择“Contoso 示例”应用程序模板。
1. 选择“创建”。

![Azure IoT Central 的“创建应用程序”页](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="next-steps"></a>后续步骤

在本快速入门中，我们创建了一个包含“冷藏食品贩卖机”设备模板和模拟设备的预填充 Azure IoT Central 应用程序。 请参阅[在应用程序中定义新设备模板](tutorial-define-device-type.md)，详细了解构建人员如何定义自己的设备模板。
