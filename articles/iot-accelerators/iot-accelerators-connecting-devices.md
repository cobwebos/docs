---
title: 使用 C 将 Windows 设备预配到远程监视 - Azure | Microsoft Docs
description: 介绍如何使用 Windows 上运行的以 C 编写的应用程序将设备连接到远程监视解决方案加速器。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: dobett
ms.openlocfilehash: 2a8a0bf1e63f06bbe6b6a073af6b3da8904dcaeb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182891"
---
# <a name="connect-your-device-to-the-remote-monitoring-solution-accelerator-windows"></a>将设备连接到远程监视解决方案加速器 (Windows)

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

本教程介绍如何将真实设备连接到远程监视解决方案加速器。

与受约束设备上运行的大多数嵌入式应用程序一样，设备应用程序的客户端代码是用 C 语言编写的。在本教程中，将在运行 Windows 的计算机上生成设备客户端应用程序。

如果更喜欢模拟某个设备，请参见[创建和测试新的模拟设备](iot-accelerators-remote-monitoring-create-simulated-device.md)。

## <a name="prerequisites"></a>必备组件

若要完成本操作指南中的步骤，请按照[设置 Windows 开发环境](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment)中的步骤将所需的开发工具和库添加到 Windows 计算机中。

## <a name="view-the-code"></a>查看代码

本指南中使用的[示例代码](https://github.com/Azure/azure-iot-sdk-c/tree/master/samples/solutions/remote_monitoring_client)可在 Azure IoT C SDK GitHub 存储库中找到。

### <a name="download-the-source-code-and-prepare-the-project"></a>下载源代码并准备项目

若要准备项目，请从 GitHub [克隆 Azure IoT C SDK 存储库](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#set-up-a-windows-development-environment)。

该示例位于 samples/solutions/remote_monitoring_client 文件夹中。

在文本编辑器中打开 samples/solutions/remote_monitoring_client 文件夹中的 remote_monitoring.c 文件。

[!INCLUDE [iot-accelerators-connecting-code](../../includes/iot-accelerators-connecting-code.md)]

## <a name="build-and-run-the-sample"></a>生成并运行示例

1. 编辑 remote_monitoring.c 文件，使用本操作指南开头在将设备添加到解决方案加速器时记下的设备连接字符串替换 `<connectionstring>`。

1. 按照[在 Windows 中构建 C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#build-the-c-sdk-in-windows) 中的步骤构建 SDK 和远程监视客户端应用程序。

1. 在用于构建解决方案的命令提示符下，运行：

    ```cmd
    samples\solutions\remote_monitoring_client\Release\remote_monitoring_client.exe
    ```

    控制台中的消息显示为：

    - 应用程序将示例遥测数据发送到解决方案加速器。
    - 响应从解决方案仪表板调用的方法。

[!INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]
