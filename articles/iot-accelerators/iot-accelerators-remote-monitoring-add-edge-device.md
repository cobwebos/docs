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
ms.openlocfilehash: d34ac159a216c5c77214b4c8b799a233c3671235
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61447345"
---
# <a name="add-an-iot-edge-device-to-your-remote-monitoring-solution-accelerator"></a>将 IoT Edge 设备添加到远程监视解决方案加速器

若要将 [IoT Edge](../iot-edge/about-iot-edge.md) 设备添加到解决方案加速器，请完成以下两个步骤：

1. 添加 Edge 设备上**Device Explorer**远程监视解决方案加速器 web UI 中的页。
1. 在 Edge 设备上安装 IoT Edge 运行时。

## <a name="add-the-iot-edge-device"></a>添加 IoT Edge 设备

若要将 IoT Edge 设备添加到远程监视解决方案加速器，请在 Web UI 中导航到“设备资源管理器”页，然后单击“+ 新建设备”。  

在“新建设备”面板中，选择“IoT Edge 设备”   。 对于其他设置，可以保留默认值。 然后单击“应用”： 

![添加 IoT Edge 设备](media/iot-accelerators-remote-monitoring-add-edge-device/addedgedevice.png)

### <a name="alternative-ways-to-add-an-iot-edge-device"></a>添加 IoT Edge 设备的其他方法

也可以将 IoT Edge 设备直接注册到解决方案加速器中的 IoT 中心实例。 在按照以下任一操作指南操作之前，需要知道解决方案加速器中 IoT 中心的名称：

- [通过 Azure 门户注册新 Azure IoT Edge 设备](../iot-edge/how-to-register-device-portal.md)
- [使用 Azure CLI 注册新 Azure IoT Edge 设备](../iot-edge/how-to-register-device-cli.md)
- [通过 Visual Studio Code 注册新 Azure IoT Edge 设备](../iot-edge/how-to-register-device-vscode.md)

直接与远程监视解决方案加速器中的 IoT 中心注册设备时，它将列在**Device Explorer** web UI 中的页。

## <a name="install-the-iot-edge-runtime"></a>安装 IoT Edge 运行时

必须先在真实设备上安装 IoT Edge 运行时，然后才能向 Edge 设备部署模块。 以下操作指南说明了如何在常见设备平台上安装运行时：

- [在 Linux (x64) 上安装 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge-linux.md)
- [在 Linux (ARM32v7/armhf) 上安装 Azure IoT Edge 运行时](../iot-edge/how-to-install-iot-edge-linux-arm.md)
- [在 Windows 上安装 Azure IoT Edge 运行时，以将其与 Windows 容器一起使用](../iot-edge/how-to-install-iot-edge-windows-with-windows.md)
- [在 Windows 上安装 Azure IoT Edge 运行时，使其与 Linux 容器一起使用](../iot-edge/how-to-install-iot-edge-windows-with-linux.md)
- [在 Windows IoT Core 上安装 IoT Edge 运行时](../iot-edge/how-to-install-iot-core.md)

## <a name="next-steps"></a>后续步骤

现在你已准备好 IoT Edge 设备，下一步是向其部署模块。 请参阅[将 IoT Edge 包导入到远程监视解决方案加速器](iot-accelerators-remote-monitoring-import-edge-package.md)
