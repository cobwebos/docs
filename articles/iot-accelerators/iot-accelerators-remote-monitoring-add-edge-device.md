---
title: 远程监视解决方案添加 Edge 设备 - Azure | Microsoft Docs
description: 本文介绍如何将 IoT Edge 设备添加到远程监视解决方案加速器
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/09/2018
ms.topic: conceptual
ms.openlocfilehash: de060be7ace84ea309b71087a50fd572091bed43
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076447"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>将 IoT Edge 设备添加到远程监视解决方案加速器

若要将 [IoT Edge](../iot-edge/about-iot-edge.md) 设备添加到解决方案加速器，请完成以下两个步骤：

1. 在远程监视解决方案加速器 web UI 的 " **Device Explorer** " 页上添加 Edge 设备。
1. 在 Edge 设备上安装 IoT Edge 运行时。

## <a name="add-the-iot-edge-device"></a>添加 IoT Edge 设备

若要将 IoT Edge 设备添加到远程监视解决方案加速器，请在 Web UI 中导航到“设备资源管理器”页，然后单击“+ 新建设备”。********

在“新建设备”面板中，选择“IoT Edge 设备”********。 对于其他设置，可以保留默认值。 然后单击“应用”：

![添加 IoT Edge 设备](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>添加 IoT Edge 设备的其他方法

也可以将 IoT Edge 设备直接注册到解决方案加速器中的 IoT 中心实例。 在按照以下任一操作指南操作之前，需要知道解决方案加速器中 IoT 中心的名称：

- [通过 Azure 门户注册新 Azure IoT Edge 设备](../iot-edge/how-to-register-device.md#register-in-the-azure-portal)
- [使用 Azure CLI 注册新 Azure IoT Edge 设备](../iot-edge/how-to-register-device.md#register-with-the-azure-cli)
- [通过 Visual Studio Code 注册新 Azure IoT Edge 设备](../iot-edge/how-to-register-device.md#register-with-visual-studio-code)

当你直接在远程监视解决方案加速器中向 IoT 中心注册设备时，它将列在 web UI 中的 " **Device Explorer** " 页上。

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

必须先在真实设备上安装 IoT Edge 运行时，然后才能向 Edge 设备部署模块。 以下操作指南说明了如何在常见设备平台上安装运行时：

- [在 Linux 上安装 Azure IoT Edge 运行时 (x64)](../iot-edge/how-to-install-iot-edge-linux.md)
- [在 Linux 上安装 Azure IoT Edge 运行时 (ARM32v7/armhf)](../iot-edge/how-to-install-iot-edge-linux.md)
- [在 Windows 上安装 Azure IoT Edge 运行时，以将其与 Windows 容器一起使用](../iot-edge/how-to-install-iot-edge-windows.md)
- [在 Windows 上安装 Azure IoT Edge 运行时，使其与 Linux 容器一起使用](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [在 Windows IoT Core 上安装 IoT Edge 运行时](../iot-edge/how-to-install-iot-edge-windows.md)

## <a name="next-steps"></a>后续步骤

现在你已准备好 IoT Edge 设备，下一步是向其部署模块。 请参阅[将 IoT Edge 包导入到远程监视解决方案加速器](iot-accelerators-remote-monitoring-import-edge-package.md)