---
title: 使用 C 将 Raspberry Pi 预配到远程监视 - Azure | Microsoft Docs
description: 介绍如何使用以 C 编写的应用程序将 Raspberry Pi 设备连接到远程监视解决方案加速器。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 3331db51f4d141cf142d1bd0578043ca6681f3cd
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58180494"
---
# <a name="connect-your-raspberry-pi-device-to-the-remote-monitoring-solution-accelerator-c"></a>将 Raspberry Pi 设备连接到远程监视解决方案加速器 (C)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教程介绍如何将真实设备连接到远程监视解决方案加速器。 与受约束设备上运行的大多数嵌入式应用程序一样，Raspberry Pi 设备应用程序的客户端代码是用 C 语言编写的。在本教程中，将在运行 Raspbian OS 的 Raspberry Pi 上生成应用程序。

如果更喜欢模拟某个设备，请参见[创建和测试新的模拟设备](iot-accelerators-remote-monitoring-create-simulated-device.md)。

### <a name="required-hardware"></a>所需硬件

一个台式机，用于通过远程方式连接到 Raspberry Pi 上的命令行。

[适用于 Raspberry Pi 3 的 Microsoft IoT 初学者套件](https://azure.microsoft.com/develop/iot/starter-kits/)或等效组件。 本教程使用套件中的以下项目：

- Raspberry Pi 3
- MicroSD 卡（带 NOOBS）
- USB 迷你电缆
- 以太网电缆

### <a name="required-desktop-software"></a>所需的桌面软件

需要在台式机上安装 SSH 客户端，才能远程访问 Raspberry Pi 上的命令行。

- Windows 不包括 SSH 客户端。 建议使用 [PuTTY](https://www.putty.org/)。
- 大多数 Linux 分发版和 Mac OS 包括命令行 SSH 实用工具。 有关详细信息，请参阅 [SSH Using Linux or Mac OS](https://www.raspberrypi.org/documentation/remote-access/ssh/unix.md)（使用 Linux 或 Mac OS 的 SSH）。

### <a name="required-raspberry-pi-software"></a>所需的 Raspberry Pi 软件

本文假设已安装最新版本的 [Raspberry Pi 上的 Raspbian OS](https://www.raspberrypi.org/learning/software-guide/quickstart/)。

以下步骤演示如何准备 Raspberry Pi 以生成可连接到解决方案加速器的 C 应用程序：

1. 使用 ssh 连接到 Raspberry Pi。 有关详细信息，请参阅 [Raspberry Pi 网站](https://www.raspberrypi.org/)上的 [SSH（安全外壳）](https://www.raspberrypi.org/documentation/remote-access/ssh/README.md)。

1. 使用以下命令更新 Raspberry Pi：

    ```sh
    sudo apt-get update
    ```

1. 若要完成本操作指南中的步骤，请按照[设置 Linux 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux)中的步骤将所需的开发工具和库添加到 Raspberry Pi。

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
