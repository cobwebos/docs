---
title: 注册新 Azure IoT Edge 设备 (CLI) | Microsoft Docs
description: 使用适用于 Azure CLI 2.0 的 IoT 扩展注册新 IoT Edge 设备
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 05/30/2018
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a6935dfe5b32364e402017cd3005ab17969ce1ce
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034344"
---
# <a name="register-a-new-azure-iot-edge-device-with-azure-cli-20"></a>使用 Azure CLI 2.0 注册新 Azure IoT Edge 设备

在 Azure IoT Edge 中使用 IoT 设备之前，需要在 IoT 中心中注册这些设备。 注册设备后，会收到一个连接字符串，可使用该字符串针对 Edge 工作负载设置设备。 

[Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) 是一个开源跨平台命令行工具，用于管理 IoT Edge 等 Azure 资源。 使用 Azure CLI 2.0 可以管理 Azure IoT 中心资源、设备预配服务实例和现成的链接中心。 新的 IoT 扩展丰富了 Azure CLI 2.0 的功能，例如设备管理和完整的 IoT Edge 功能。

本文介绍如何使用 Azure CLI 2.0 注册新 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 
* 环境中的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 2.0 版本必须至少是 2.0.24 或更高。 请使用 `az –-version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 
* [适用于 Azure CLI 2.0 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="create-a-device"></a>创建设备

使用以下命令在 IoT 中心中创建新设备标识： 

   ```cli
   az iot hub device-identity create --device-id [device id] --hub-name [hub name] --edge-enabled
   ```

此命令包括三个参数：
* **device-id**：提供对 IoT 中心唯一的描述性名称。
* **hub-name**：提供 IoT 中心的名称。
* **edge-enabled**：此参数声明该设备用于 IoT Edge。

   ![创建 IoT Edge 设备](./media/how-to-register-device-cli/Create-edge-device.png)

## <a name="view-all-devices"></a>查看所有设备

使用以下命令查看 IoT 中心内的所有设备：

   ```cli
   az iot hub device-identity list --hub-name [hub name]
   ```

注册为 IoT Edge 设备的任何设备的 **capabilities.iotEdge** 属性都会设置为 **true**。 

## <a name="retrieve-the-connection-string"></a>检索连接字符串

如果已准备好设置设备，则需要连接字符串，该字符串使用物理设备在 IoT 中心内的标识链接该设备。 使用以下命令返回单个设备的连接字符串：

   ```cli
   az iot hub device-identity show-connection-string --device-id [device id] --hub-name [hub name] 
   ```

device id 参数区分大小写。 

## <a name="next-steps"></a>后续步骤

了解如何[使用 Azure CLI 2.0 将模块部署到设备](how-to-deploy-modules-cli.md)