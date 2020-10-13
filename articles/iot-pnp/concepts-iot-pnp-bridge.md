---
title: IoT 即插即用 bridge |Microsoft Docs
description: 了解 IoT 即插即用 bridge 以及如何使用它将连接到 Windows 或 Linux 网关的现有设备连接为 IoT 即插即用设备。
author: usivagna
ms.author: ugans
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 224f86a40fa812003463301f97bcae07de907f3c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91579752"
---
# <a name="iot-plug-and-play-bridge"></a>IoT 即插即用桥接

IoT 即插即用桥是一个开源应用程序，用于将连接到 Windows 或 Linux 网关的现有设备连接为 IoT 即插即用设备。 在 Windows 或 Linux 计算机上安装和配置应用程序后，可以使用它将连接的设备连接到 IoT 中心。 你可以使用桥将 IoT 即插即用接口映射到附加设备发送的遥测、使用设备属性以及调用命令。

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-high-level.png" alt-text="左侧有几个现有的传感器 (有线和无线) 连接到包含 IoT 即插即用 bridge 的 Windows 或 Linux 计算机。然后，IoT 即插即用桥连接到右侧的 IoT 中心":::

IoT 即插即用 bridge 可以作为独立的可执行文件部署在运行 Windows 10 或 Linux 的任何 IoT 设备、工业 PC、服务器或网关上。 它还可以编译到应用程序代码中。 简单的配置 JSON 文件告诉 IoT 即插即用桥接哪些连接的设备/外设应在 Azure 中公开。

## <a name="supported-protocols-and-sensors"></a>支持的协议和传感器

默认情况下，IoT 即插即用桥支持以下类型的外围设备，并提供适配器文档的链接：

|外设|Windows|Linux|
|---------|---------|---------|
|[蓝牙 LE](https://aka.ms/iot-pnp-bridge-bluetooth)       |是|否|
|[摄像头](https://aka.ms/iot-pnp-bridge-camera)               |是|否|
|[Modbus](https://aka.ms/iot-pnp-bridge-modbus)                |是|是|
|[MQTT](https://aka.ms/iot-pnp-bridge-mqtt)                    |是|是|
|[串行](https://aka.ms/iot-pnp-bridge-serial)                |是|是|
|[Windows USB 外围设备](https://aka.ms/iot-pnp-bridge-usb)  |是|不适用|

>[!Important]
>开发人员可以通过 **[此处的 iot 即插即用 bridge 开发人员文档](https://aka.ms/iot-pnp-bridge-dev-doc)** 中的说明，扩展 iot 即插即用桥以支持其他设备协议。

## <a name="prerequisites"></a>必备条件

### <a name="os-platform"></a>OS 平台

支持以下 OS 平台和版本：

|平台  |支持的版本  |
|---------|---------|
|Windows 10 |     支持所有 Windows Sku。 例如： IoT Enterprise、Server、Desktop、IoT Core。 *对于相机运行状况监视功能，建议使用20H1 或更高版本。所有其他功能在所有 Windows 10 版本上都可用。*  |
|Linux     |在 Ubuntu 18.04 上经过测试和支持，尚未测试其他分发版的功能。         |
||

### <a name="hardware"></a>硬件

- 支持上述 OS Sku 和版本的任何硬件平台。
- 本机支持串行、USB、蓝牙和相机外设和传感器。 可对 IoT 即插即用桥进行扩展，以支持任何自定义外围设备或传感器 ([请参阅上面) 的外围设备部分](#iot-plug-and-play-bridge) 。

### <a name="development-environment"></a>开发环境

若要构建、扩展和开发 IoT 即插即用 bridge，你将需要：  

- 支持编译 c + + 的开发环境（例如 [Visual studio (社区版、专业版或企业版) ](https://visualstudio.microsoft.com/downloads/)），请确保在安装 visual studio 时包含 "使用 c + + 进行桌面开发" 工作负载。
- [CMake](https://cmake.org/download/) -安装 CMake 时，选择选项 `Add CMake to the system PATH` 。
- 如果要在 Windows 上进行构建，还需要下载 Windows 17763 SDK： [https://developer.microsoft.com/windows/downloads/windows-10-sdk](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
- [Azure IoT 中心设备 C SDK](https://github.com/Azure/azure-iot-sdk-c)。 此存储库中包含的生成脚本会自动克隆所需的 Azure IoT C SDK。

### <a name="azure-iot-products-and-tools"></a>Azure IoT 产品和工具

- **Azure Iot 中心** -需要 azure 订阅中的 [azure iot 中心](https://docs.microsoft.com/azure/iot-hub/) 将设备连接到。 如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。 如果没有 IoT 中心，请 [按照以下说明创建一个](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-using-cli)。

> [!Note]
> IoT 即插即用当前在美国中部、北欧和日本东部区域创建的 IoT 中心中提供。 基本层的 IoT 中心不包含 IoT 即插即用支持。 若要与 IoT 即插即用设备进行交互，可以使用 Azure IoT 浏览器工具。 为操作系统[下载并安装最新版本的 Azure IoT 资源管理器](./howto-use-iot-explorer.md)。

## <a name="iot-plug-and-play-bridge-architecture"></a>IoT 即插即用 bridge 体系结构

:::image type="content" source="media/concepts-iot-pnp-bridge/iot-pnp-bridge-components.png" alt-text="左侧有几个现有的传感器 (有线和无线) 连接到包含 IoT 即插即用 bridge 的 Windows 或 Linux 计算机。然后，IoT 即插即用桥连接到右侧的 IoT 中心":::

## <a name="download-iot-plug-and-play-bridge"></a>下载 IoT 即插即用 bridge

你可以在 [IoT 即插即用 bridge 版本](https://aka.ms/iot-pnp-bridge-releases) 中下载具有支持的适配器的网桥预构建版本，并展开最新版本的资产列表。 下载适用于你的操作系统的最新版本的应用程序。

你还可以 [在 GitHub 上下载并查看 IoT 即插即用 bridge](https://aka.ms/bridge)的源代码。

## <a name="next-steps"></a>后续步骤

现在，你已大致了解 IoT 即插即用 bridge 的体系结构，接下来的步骤是详细了解以下内容：

- [如何使用 IoT 即插即用 bridge](./howto-use-iot-pnp-bridge.md)
- [请参阅适用于 IoT 即插即用 bridge 的 GitHub 开发人员参考](https://aka.ms/iot-pnp-bridge-dev-doc)
- [GitHub 上的 IoT 即插即用桥](https://aka.ms/iotplugandplaybridge)
