---
title: 使用 C 将 Linux 设备预配到远程监视 - Azure | Microsoft Docs
description: 介绍如何使用 Linux 上运行的以 C 编写的应用程序将设备连接到远程监视解决方案加速器。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: dobett
ms.openlocfilehash: 91d4eda566c8b534daa10c62637db28ccb01bbb6
ms.sourcegitcommit: c94cf3840db42f099b4dc858cd0c77c4e3e4c436
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/19/2018
ms.locfileid: "53628317"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-linux"></a>将设备连接到远程监视解决方案加速器 (Linux)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教程介绍如何将真实设备连接到远程监视解决方案加速器。

与受约束设备上运行的大多数嵌入式应用程序一样，设备应用程序的客户端代码是用 C 语言编写的。在本教程中，将在运行 Ubuntu (Linux) 的计算机上生成应用程序。

如果更喜欢模拟某个设备，请参阅[创建和测试新的模拟设备](iot-accelerators-remote-monitoring-create-simulated-device.md)。

## <a name="prerequisites"></a>先决条件

要完成该操作指南中的步骤，你需要一台运行 Ubuntu 版本 15.04 或更高版本的设备。 继续操作之前，请[设置 Linux 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)。

## <a name="view-the-code"></a>查看代码

本指南中使用的[示例代码](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client)可在 Azure IoT C SDK GitHub 存储库中找到。

### <a name="download-the-source-code-and-prepare-the-project"></a>下载源代码并准备项目

若要准备项目，请从 GitHub 克隆或下载 [Azure IoT C SDK 存储库](https://github.com/Azure/azure-iot-sdk-c)。

该示例位于 samples/solutions/remote_monitoring_client 文件夹中。

在文本编辑器中打开 samples/solutions/remote_monitoring_client 文件夹中的 remote_monitoring.c 文件。

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-application"></a>构建并运行应用程序

以下步骤描述如何使用 CMake 生成客户端应用程序。 远程监视客户端应用程序是在构建 SDK 的过程中构建的。

1. 编辑 remote_monitoring.c 文件，使用本操作指南开头在将设备添加到解决方案加速器时记下的设备连接字符串替换 `<connectionstring>`。

1. 导航到 [Azure IoT C SDK 存储库](https://github.com/Azure/azure-iot-sdk-c)的克隆副本的根目录，然后运行以下命令来生成客户端应用程序：

    ```sh
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

1. 运行客户端应用程序，并将遥测数据发送到 IoT 中心：

    ```sh
    ./samples/solutions/remote_monitoring_client/remote_monitoring_client
    ```

    控制台中的消息显示为：

    - 应用程序将示例遥测数据发送到解决方案加速器。
    - 响应从解决方案仪表板调用的方法。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
