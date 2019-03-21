---
title: 脱机操作设备 - Azure IoT Edge | Microsoft Docs
description: 了解 IoT Edge 设备和模块如何能够长时间在无 Internet 连接的情况下操作，以及 IoT Edge 如何使常规 IoT 设备也能脱机操作。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: e82c842ec8fce703c48c98eaf09ea5c8d91be9be
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2019
ms.locfileid: "57307983"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices-preview"></a>了解有关 IoT Edge 设备、模块和子设备的扩展脱机功能（预览版）

Azure IoT Edge 支持 IoT Edge 设备上的扩展脱机操作，同时在非 Edge 子设备上启用脱机操作。 只要 IoT Edge 设备有机会连接到 IoT 中心，它和任何子设备就可以在间歇性或无 Internet 连接的情况下继续运作。 

>[!NOTE]
>对 IoT Edge 的脱机支持目前为[公共预览版](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="how-it-works"></a>工作原理

当 IoT Edge 设备进入脱机模式，IoT Edge 中心将扮演三个角色。 首先，它将存储任何向上游发送的消息并保存它们，直到设备重新连接。 其次，它代表 IoT 中心对模块和子设备进行身份验证，以便它们可以继续运行。 第三，它会在子设备之间启用通常通过 IoT 中心的通信。 

下面的示例展示了 IoT Edge 方案如何在脱机模式下运行：

1. **配置 IoT Edge 设备**。

   IoT Edge 设备自动启用脱机功能。 若要将此功能扩展到其他 IoT 设备，需要在 IoT 中心声明设备之间的父子关系。 

2. **与 IoT 中心同步**。

   在安装 IoT Edge 运行时后，IoT Edge 设备至少要有一次处于联机状态，以便与 IoT 中心同步。 在此同步中，IoT Edge 设备将获取有关任何分配给它的子设备的详细信息。 IoT Edge 设备还可以安全更新本地缓存以启用脱机操作，并检索本地存储遥测消息的设置。 

3. **脱机**。

   从 IoT 中心断开连接时，IoT Edge 设备及其部署模块和任何 IoT 子设备都可以无限期运行。 模块和子设备可以在脱机状态下通过在 IoT Edge 中心进行身份验证来启动和重新启动。 上游绑定到 IoT 中心的遥测存储在本地。 模块之间或 loT 子设备之间的通信通过直接方法或消息来维护。 

4. **与 IoT 中心重新连接和重新同步**。

   一旦还原与 IoT 中心的连接，IoT Edge 设备会再次同步。 本地存储的消息按照它们存储的相同顺序传递。 模块和设备的所需属性和报告属性之间的差异已得到协调。 IoT Edge 设备更新对其分配的 IoT 子设备集所做的任何更改。

## <a name="restrictions-and-limits"></a>约束和限制

本文所述的扩展脱机功能可在 [IoT Edge 1.0.4 版或更高版本](https://github.com/Azure/azure-iotedge/releases)中获得。 早期版本有一个脱机功能子集。 不具备扩展脱机功能的现有 IoT Edge 设备不能通过更改运行时版本进行升级，但是必须用新的 IoT Edge 设备标识重新配置才能获得这些功能。 

除了美国东部以外，所有提供 IoT 中心的区域都提供扩展的脱机支持。

只有非 Edge loT 设备可以作为子设备添加。 

IoT Edge 设备及其分配的子设备可以在初始一次性同步之后无限期脱机运行。但是，消息存储取决于生存时间 (TTL) 设置和存储消息的可用磁盘空间。 

## <a name="set-up-an-iot-edge-device"></a>设置 IoT Edge 设备

对于将其扩展脱机功能扩展到 loT 子设备的 IoT Edge 设备，需要在 Azure 门户中声明父子关系。

### <a name="assign-child-devices"></a>分配子设备

子设备可以是注册到同一个 IoT 中心的任何非 Edge 设备。 可以在创建新设备时管理父子关系，或者从 IoT Edge 父设备或 loT 子设备的设备详细信息页进行管理。 

   ![从 IoT Edge 设备的详细信息页管理子设备](./media/offline-capabilities/manage-child-devices.png)

父设备可以有多个子设备，但子设备只能有一个父设备。

### <a name="specifying-dns-servers"></a>指定 DNS 服务器 

为了提高可靠性，建议指定在环境中使用的 DNS 服务器地址。 例如，在 Linux 上，更新 /etc/docker/daemon.json（可能需要创建该文件）以包括：

```json
{
    "dns": ["1.1.1.1"]
}
```

如果使用的是本地 DNS 服务器，请将 1.1.1.1 替换为本地 DNS 服务器的 IP 地址。 重启 Docker 服务以使更改生效。


## <a name="optional-offline-settings"></a>可选脱机设置

如果希望收集设备在长时间脱机期中生成的所有消息，请配置 IoT Edge 中心，这样它就可以存储所有消息。 可以对 IoT Edge 中心进行两次更改，以启用长期消息存储。 首先，增加生存时间设置。 然后，为消息存储添加更多磁盘空间。 

### <a name="time-to-live"></a>生存时间

生存时间设置是指在过期之前消息可以等待传递的时间量（以秒为单位）。 默认为 7200 秒（两个小时）。 

此设置是 IoT Edge 中心的所需属性，它存储在模块孪生中。 可以在 Azure 门户的“配置高级 Edge 运行时设置”部分进行配置，也可以直接在部署清单中配置。 

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

默认情况下，消息存储在 IoT Edge 中心的容器文件系统中。 如果存储空间不足以满足你的脱机需求，可以在 IoT Edge 设备上使用本地存储。 为 IoT Edge 中心创建一个环境变量，以便指向容器中的存储文件夹。 然后，使用创建选项将存储文件夹绑定到主机上的文件夹。 

可以在 Azure 门户的“配置高级 Edge 运行时设置”部分配置环境变量和 IoT Edge 中心模块的创建选项。 或者，可以直接在部署清单中进行配置。 

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": {
            "HostConfig": {
                "Binds": ["<HostStoragePath>:<ModuleStoragePath>"],
                "PortBindings": {
                    "8883/tcp": [{"HostPort":"8883"}],
                    "443/tcp": [{"HostPort":"443"}],
                    "5671/tcp": [{"HostPort":"5671"}]
                }
            }
        }
    },
    "env": {
        "storageFolder": {
            "value": "<ModuleStoragePath>"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

将 `<HostStoragePath>` 和 `<ModuleStoragePath>` 替换为你的主机和模块存储路径；主机和模块存储路径都必须是绝对路径。 在创建选项中，将主机和模块存储路径绑定在一起。 然后，创建指向模块存储路径的环境变量。  

例如，`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 表示主机系统上的目录 /etc/iotedge/storage 映射到容器上的目录 /iotedge/storage/。 或是对于 Windows 系统的另一个示例，`"Binds":["C:\\temp:C:\\contemp"]` 表示主机系统上的目录 C:\\temp 映射到容器上的目录 C:\\contemp。 

你还可以从 [docker 文档](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)中找到有关创建选项的更多详细信息。

## <a name="next-steps"></a>后续步骤

在[透明网关](how-to-create-transparent-gateway.md)方案中启用扩展脱机操作。
