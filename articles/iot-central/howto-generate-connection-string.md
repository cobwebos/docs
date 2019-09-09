---
title: 生成 Azure IoT Central 的设备连接字符串 |Microsoft Docs
description: 作为设备开发人员，如何为需要连接到 IoT Central 应用程序的设备生成连接字符串？
author: dominicbetts
ms.author: dobett
ms.date: 08/23/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 97c0332656b75c3c8d0cddecb41c7a15ac2f218c
ms.sourcegitcommit: 80dff35a6ded18fa15bba633bf5b768aa2284fa8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019779"
---
# <a name="generate-a-device-connection-string-to-connect-to-an-azure-iot-central-application"></a>生成连接到 Azure IoT Central 应用程序的设备连接字符串

本文介绍了如何作为设备开发人员为需要连接到 IoT Central 应用程序的设备生成连接字符串。 本文中所述的过程说明如何使用共享访问签名（SAS）快速连接一台设备。 当你尝试 IoT Central 或测试设备时，此方法非常有用。 有关在生产环境中使用的替代方法，请参阅[Azure 中的设备连接 IoT Central](concepts-connectivity.md)。

## <a name="prerequisites"></a>先决条件

若要完成本文中的步骤，需要以下各项：

- Azure IoT Central 应用程序。 有关详细信息，请参阅[创建应用程序快速入门](quick-deploy-iot-central.md)。
- 安装[了 node.js 8.0.0 版或](https://nodejs.org/)更高版本的开发计算机。 若要检查版本，可以在命令行中运行 `node --version`。 Node.js 适用于各种操作系统。

## <a name="get-connection-information"></a>获取连接信息

以下步骤介绍如何获取生成设备的 SAS 连接字符串所需的信息：

1. 在**Device Explorer**中，找到要连接到应用程序的实际设备：

    ![选择真实设备](media/howto-generate-connection-string/real-devices.png)

1. 在 "**设备**" 页上，选择 "**连接**"：

    ![选择连接](media/howto-generate-connection-string/connect.png)

1. 记下连接详细信息、**作用域 id**、**设备 ID**和**设备主密钥**，以在以下步骤中使用：

    ![连接详细信息](media/howto-generate-connection-string/device-connect.png)

    您可以从此页复制值以便保存。

## <a name="generate-the-connection-string"></a>生成连接字符串

[!INCLUDE [iot-central-howto-connection-string](../../includes/iot-central-howto-connection-string.md)]

## <a name="next-steps"></a>后续步骤

现在，你已为真实设备生成连接字符串，可以连接到 Azure IoT Central 应用程序，下面是建议的后续步骤：

* [准备和连接 DevKit 设备 (C)](howto-connect-devkit.md)
* [准备和连接 Raspberry Pi (Python)](howto-connect-raspberry-pi-python.md)
* [准备和连接 Raspberry Pi (C#)](howto-connect-raspberry-pi-csharp.md)
* [准备和连接 Windows 10 IoT Core 设备 (C#)](howto-connect-windowsiotcore.md)
* [将泛型 Node.js 客户端连接到 Azure IoT Central 应用程序](howto-connect-nodejs.md)