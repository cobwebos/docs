---
title: 生成 Azure IoT 中心设备连接字符串 |Microsoft Docs
description: 作为设备开发人员，如何生成需要连接到 IoT 中心应用程序的设备的连接字符串？
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: f302cbfa7152ae30be434f560c0c39056d40f9f4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60885602"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>生成一个设备连接字符串以连接到 Azure IoT Central 应用程序

本指南介绍了如何为设备开发人员，以生成设备需要连接到 IoT 中心应用程序的连接字符串。 在本文中所述的过程演示如何快速连接使用共享的访问签名 (SAS) 的单个设备。 通过 IoT Central 试用或测试设备时，此方法非常有用。 若要在生产环境中使用的替代方法，请参阅[在 Azure IoT 中心的设备连接性](concepts-connectivity.md)。

## <a name="prerequisites"></a>必备组件

若要完成本文中的步骤，需要以下各项：

- Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
- 与在开发计算机[Node.js](https://nodejs.org/) 8.0.0 版或更高版本。 若要检查版本，可以在命令行中运行 `node --version`。 Node.js 适用于各种操作系统。

## <a name="get-connection-information"></a>获取连接信息

以下步骤介绍如何获取您需要生成设备的 SAS 连接字符串的信息：

1. 在中**Device Explorer**，找到想要连接到你的应用程序的真实设备：

    ![选择一个真实的设备](media/howto-generate-connection-string/real-devices.png)

1. 上**设备**页上，选择**Connect**:

    ![选择连接](media/howto-generate-connection-string/connect.png)

1. 请记下的连接详细信息，**作用域 ID**，**设备 ID**，并**设备主键**，则应使用以下步骤：

    ![连接详细信息](media/howto-generate-connection-string/device-connect.png)

    可以从此页来保存复制值。

## <a name="generate-the-connection-string"></a>生成连接字符串

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>后续步骤

现在，已生成真实的设备连接到 Azure IoT Central 应用程序的连接字符串，下面是建议的后续步骤：

* [准备和连接 DevKit 设备 (C)](howto-connect-devkit.md)
* [准备和连接 Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [准备和连接 Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [准备和连接 Windows 10 IoT Core 设备 (C#)](howto-connect-windowsiotcore.md)
* [将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序](howto-connect-nodejs.md)