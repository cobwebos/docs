---
title: include 文件
description: include 文件
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 09/07/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: e80033d696de1b83da43fc27e5be9eca3b3f8757
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2018
ms.locfileid: "44168772"
---
## <a name="create-a-device-identity"></a>创建设备标识

在本部分中，你将使用 Azure CLI 创建一个设备标识以在本教程中使用。 Azure CLI 预安装在 [Azure Cloud Shell](https://docs.microsoft.com/zure/cloud-shell/overview) 中，你也可以[在本地安装它](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)。 设备 ID 区分大小写。

1. 在命令行环境中运行以下命令，你将在其中使用 Azure CLI 来安装 IoT 扩展：

    ```cmd/sh
    az extension add --name azure-cli-iot-ext
    ```

1. 如果是在本地运行 Azure CLI，请使用以下命令登录到你的 Azure 帐户（如果使用的是 Cloud Shell，则会自动登录，不需要运行此命令）：

    ```cmd/sh
    az login
    ```

1. 最后，使用以下命令创建一个名为 `myDeviceId` 的新设备标识并检索设备连接字符串：

    ```cmd/sh
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

记下结果中的设备连接字符串。 设备应用使用此设备连接字符串以设备身份连接到 IoT 中心。

<!-- images and links -->
