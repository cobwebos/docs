---
title: Azure IoT Edge 的脱机功能 | Microsoft Docs
description: 了解 IoT Edge 设备和模块如何能够长时间脱机操作，以及 IoT Edge 如何使常规 IoT 设备也能脱机操作。
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f4afad753da4a314ade3fb7433c6be3e489e05b0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47033679"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>了解有关 IoT Edge 设备、模块和子设备的扩展脱机功能（预览版）

Azure IoT Edge 支持 IoT Edge 设备上的扩展脱机操作，同时在非 Edge 子设备上启用脱机操作。 只要 IoT Edge 设备有机会连接到 IoT 中心，它和任何子设备就可以在间歇性或无 Internet 连接的情况下继续运作。 

>[!NOTE]
>对 IoT Edge 的脱机支持目前为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-it-works"></a>工作原理

当 IoT Edge 设备进入脱机模式，Edge 中心将扮演三个角色。 首先，它将存储任何向上游发送的消息并保存它们，直到设备重新连接。 其次，它代表 IoT 中心对模块和子设备进行身份验证，以便它们可以继续运行。 第三，它会在子设备之间启用通常通过 IoT 中心的通信。 

下面的示例展示了 IoT Edge 方案如何在脱机模式下运行：

1. **配置 IoT Edge 设备**。

   IoT Edge 设备自动启用脱机功能。 若要将此功能扩展到其他 IoT 设备，需要在 IoT 中心声明设备之间的父子关系。 

2. **与 IoT 中心同步**。

   在安装 IoT Edge 运行时后，IoT Edge 设备至少要有一次处于联机状态，以便与 IoT 中心同步。 在此同步中，IoT Edge 设备将获取有关任何分配给它的子设备的详细信息。 IoT Edge 设备还可以安全更新本地缓存以启用脱机操作，并检索本地存储遥测消息的设置。 

3. **脱机**。

   从 IoT 中心断开连接时，IoT Edge 设备及其部署模块和任何 IoT 子设备都可以无限期运行。 模块和子设备可以在脱机状态下通过在 Edge 中心进行身份验证来启动和重新启动。 上游绑定到 IoT 中心的遥测存储在本地。 模块之间或 loT 子设备之间的通信通过直接方法或消息来维护。 

4. **与 IoT 中心重新连接和重新同步**。

   一旦还原与 IoT 中心的连接，IoT Edge 设备会再次同步。 本地存储的消息按照它们存储的相同顺序传递。 模块和设备的所需属性和报告属性之间的差异已得到协调。 IoT Edge 设备更新对其分配的 IoT 子设备集所做的任何更改。

## <a name="restrictions-and-limits"></a>约束和限制

本文所述的扩展脱机功能可在 [IoT Edge 1.0.2 或更高版本](https://github.com/Azure/azure-iotedge/releases)中获得。 早期版本有一个脱机功能子集。 不具备扩展脱机功能的现有 IoT Edge 设备不能通过更改运行时版本进行升级，但是必须用新的 IoT Edge 设备标识重新配置才能获得这些功能。 

除了美国东部和西欧以外，所有提供 IoT Hub 的区域都提供扩展脱机支持。 

只有非 Edge loT 设备可以作为子设备添加。 

IoT Edge 设备及其分配的子设备可以在初始一次性同步之后无限期脱机运行。但是，消息存储取决于生存时间 (TTL) 设置和存储消息的可用磁盘空间。 

## <a name="set-up-an-edge-device"></a>设置 Edge 设备

对于想要在扩展脱机期间执行的任何 IoT Edge 设备，配置 IoT Edge 运行时以通过 MQTT 进行通信。 

对于将其扩展脱机功能扩展到 loT 子设备的 IoT Edge 设备，需要在 Azure 门户中声明父子关系。

### <a name="set-the-upstream-protocol-to-mqtt"></a>将上游协议设置为 MQTT

配置 Edge 中心和 Edge 代理，以便按照上游协议与 MQTT 进行通信。 该协议是在部署清单中使用环境变量声明的。 

在 Azure 门户中，可以通过在为部署设置模块时选择“配置高级 Edge 运行时设置”按钮来访问 Edge 中心和 Edge 代理模块定义。 对于这两个模块，创建名为“UpstreamProtocol”的环境变量并将其值设为“MQTT”。 

在部署模板 JSON 中，环境变量声明如以下示例所示： 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "UpstreamProtocol": {
            "value": "MQTT"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

### <a name="assign-child-devices"></a>分配子设备

子设备可以是注册到同一个 IoT 中心的任何非 Edge 设备。 可以在创建新设备时管理父子关系，或者从 IoT Edge 父设备或 loT 子设备的设备详细信息页进行管理。 

   ![从 IoT Edge 设备的详细信息页管理子设备](./media/offline-capabilities/manage-child-devices.png)

父设备可以有多个子设备，但子设备只能有一个父设备。

## <a name="optional-offline-settings"></a>可选脱机设置

如果希望设备经历长时间的脱机期，在此之后你想要收集生成的所有消息，请配置 Edge 中心，这样它就可以存储所有消息。 可以对 Edge 中心进行两次更改，以启用长期消息存储。 首先增加生存时间设置，然后为消息存储添加额外的磁盘空间。 

### <a name="time-to-live"></a>生存时间

生存时间设置是指在过期之前消息可以等待传递的时间量（以秒为单位）。 默认为 7200 秒（两个小时）。 

此设置是 Edge 中心的所需属性，它存储在模块孪生中。 可以在 Azure 门户的“配置高级 Edge 运行时设置”部分进行配置，也可以直接在部署清单中配置。 

```json
"$edgeHub": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {},
        "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
        }
    }
}
```

### <a name="additional-offline-storage"></a>其他脱机存储

默认情况下，消息存储在 Edge 中心的容器文件系统中。 如果存储空间不足以满足你的脱机需求，可以在 IoT Edge 设备上使用本地存储。 需要为 Edge 中心创建一个环境变量，以便指向容器中的存储文件夹。 然后，使用创建选项将存储文件夹绑定到主机上的文件夹。 

可以在 Azure 门户的“配置高级 Edge 运行时设置”部分配置环境变量和 Edge 中心模块的创建选项。 或者，可以直接在部署清单中进行配置。 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": "{\"HostConfig\":{\"Binds\":[\"C:\\\\HostStoragePath:C:\\\\ModuleStoragePath\"],\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}]}}}"
    },
    "env": {
        "storageFolder": {
            "value": "C:\\\\ModuleStoragePath"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

## <a name="next-steps"></a>后续步骤

在透明网关方案中启用针对 [Linux](how-to-create-transparent-gateway-linux.md) 或 [Windows](how-to-create-transparent-gateway-windows.md) 设备的扩展脱机操作。 