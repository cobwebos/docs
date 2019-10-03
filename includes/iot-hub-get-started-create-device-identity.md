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
ms.openlocfilehash: 1e919f22d5dd7975f055f262ec9ba69230aebd17
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2019
ms.locfileid: "68780854"
---
在本部分中, 将使用 Azure CLI 为本文创建设备标识。 设备 ID 区分大小写。

1. 打开 [Azure Cloud Shell](https://shell.azure.com/)。

1. 在 Azure Cloud Shell 中运行以下命令, 安装用于 Azure CLI 的 Microsoft Azure IoT 扩展:

    ```azurecli-interactive
    az extension add --name azure-cli-iot-ext
    ```

2. 创建名`myDeviceId`为的新设备标识, 并使用以下命令检索设备连接字符串:

    ```azurecli-interactive
    az iot hub device-identity create --device-id myDeviceId --hub-name {Your IoT Hub name}
    az iot hub device-identity show-connection-string --device-id myDeviceId --hub-name {Your IoT Hub name} -o table
    ```

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

记下结果中的设备连接字符串。 设备应用使用此设备连接字符串以设备身份连接到 IoT 中心。

<!-- images and links -->
