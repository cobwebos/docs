---
title: 脱机操作设备 - Azure IoT Edge | Microsoft Docs
description: 了解 IoT Edge 设备和模块如何能够长时间在无 Internet 连接的情况下操作，以及 IoT Edge 如何使常规 IoT 设备也能脱机操作。
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4f3e5c1566271573b43e24a1749b42daa7530555
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67051949"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>了解有关 IoT Edge 设备、模块和子设备的扩展脱机功能

Azure IoT Edge 支持 IoT Edge 设备上的扩展脱机操作并太启用 IoT Edge 子设备上的脱机操作。 只要 IoT Edge 设备有机会连接到 IoT 中心，它和任何子设备就可以在间歇性或无 Internet 连接的情况下继续运作。 


## <a name="how-it-works"></a>工作原理

当 IoT Edge 设备进入脱机模式，IoT Edge 中心将扮演三个角色。 首先，它将存储任何向上游发送的消息并保存它们，直到设备重新连接。 其次，它代表 IoT 中心对模块和子设备进行身份验证，以便它们可以继续运行。 第三，它会在子设备之间启用通常通过 IoT 中心的通信。 

下面的示例展示了 IoT Edge 方案如何在脱机模式下运行：

1. **配置设备**

   IoT Edge 设备自动启用脱机功能。 若要将此功能扩展到其他 IoT 设备，需要在 IoT 中心声明设备之间的父子关系。 然后，配置子设备信任其已分配的父设备并通过父用作网关的设备到云通信进行路由。 

2. **使用 IoT 中心的同步**

   在安装 IoT Edge 运行时后，IoT Edge 设备至少要有一次处于联机状态，以便与 IoT 中心同步。 在此同步中，IoT Edge 设备将获取有关任何分配给它的子设备的详细信息。 IoT Edge 设备还可以安全更新本地缓存以启用脱机操作，并检索本地存储遥测消息的设置。 

3. **进入脱机状态**

   从 IoT 中心断开连接时，IoT Edge 设备及其部署模块和任何 IoT 子设备都可以无限期运行。 模块和子设备可以在脱机状态下通过在 IoT Edge 中心进行身份验证来启动和重新启动。 上游绑定到 IoT 中心的遥测存储在本地。 模块之间或 loT 子设备之间的通信通过直接方法或消息来维护。 

4. **重新连接并重新同步使用 IoT 中心**

   一旦还原与 IoT 中心的连接，IoT Edge 设备会再次同步。 本地存储的消息按照它们存储的相同顺序传递。 模块和设备的所需属性和报告属性之间的差异已得到协调。 IoT Edge 设备更新对其分配的 IoT 子设备集所做的任何更改。

## <a name="restrictions-and-limits"></a>约束和限制

本文所述的扩展脱机功能可在 [IoT Edge 1.0.4 版或更高版本](https://github.com/Azure/azure-iotedge/releases)中获得。 早期版本有一个脱机功能子集。 不具备扩展脱机功能的现有 IoT Edge 设备不能通过更改运行时版本进行升级，但是必须用新的 IoT Edge 设备标识重新配置才能获得这些功能。 

除了美国东部以外，所有提供 IoT 中心的区域都提供扩展的脱机支持  。

仅非 IoT Edge 设备可以添加为子设备。 

IoT Edge 设备及其分配的子设备可以在初始一次性同步之后无限期脱机运行。但是，消息存储取决于生存时间 (TTL) 设置和存储消息的可用磁盘空间。 

## <a name="set-up-parent-and-child-devices"></a>设置父级和子级的设备

对于将其扩展脱机功能扩展到子 IoT 设备的 IoT Edge 设备，需要完成两个步骤。 首先，声明父-子关系在 Azure 门户中。 其次，创建父设备和任何子设备之间的信任关系，然后配置为通过网关作为父级的设备到云通信。 

### <a name="assign-child-devices"></a>分配子设备

子设备可以是任何非 IoT Edge 设备注册到同一个 IoT 中心。 父设备可以有多个子设备，但是子设备仅有一个父元素。 有三个选项可用于将子设备设置为 edge 设备： 通过 Azure 门户中，使用 Azure CLI，或使用 IoT 中心服务 SDK。 

以下部分提供如何声明为现有的 IoT 设备在 IoT 中心的父/子关系的示例。 如果您要创建新的设备标识您的孩子的设备，请参阅[到 Azure IoT 中心下游设备进行身份验证](how-to-authenticate-downstream-device.md)有关详细信息。

#### <a name="option-1-iot-hub-portal"></a>选项 1：IoT 中心门户

创建新设备时，您可以声明的父-子关系。 或对于现有的设备，可以声明从设备详细信息页的父级 IoT Edge 设备的关系或子 IoT 设备。 

   ![从 IoT Edge 设备的详细信息页管理子设备](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>选项 2：使用 `az` 命令行工具

使用[Azure 命令行接口](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)与[IoT 扩展](https://github.com/azure/azure-iot-cli-extension)(v0.7.0 或更高版本)，你可以管理具有父子关系[设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)子命令。 下面的示例使用查询来分配中心要子设备的 IoT Edge 设备中的所有 IoT Edge 设备。 

```shell
# Set IoT Edge parent device
egde_device="edge-device1"

# Get All IoT Devices
device_list=$(az iot hub query \
        --hub-name replace-with-hub-name \
        --subscription replace-with-sub-name \
        --resource-group replace-with-rg-name \
        -q "SELECT * FROM devices WHERE capabilities.iotEdge = false" \
        --query 'join(`, `, [].deviceId)' -o tsv)

# Add all IoT devices to IoT Edge (as child)
az iot hub device-identity add-children \
  --device-id $egde_device \
  --child-list $device_list \
  --hub-name replace-with-hub-name \
  --resource-group replace-with-rg-name \
  --subscription replace-with-sub-name 
```

可以修改[查询](../iot-hub/iot-hub-devguide-query-language.md)，选择另一部分设备。 如果指定大的设备集，此命令可能需要数秒钟才能完成。

#### <a name="option-3-use-iot-hub-service-sdk"></a>选项 3：使用 IoT 中心服务 SDK 

最后，可以使用 C#、Java 或 Node.js IoT 中心服务 SDK 以编程方式管理父子关系。 这是使用 C# SDK [分配子设备的示例](https://aka.ms/set-child-iot-device-c-sharp)。

### <a name="set-up-the-parent-device-as-a-gateway"></a>设置父设备用作网关

您可以将用作透明网关，在子设备在 IoT 中心中具有其自己的标识，但通过其父级通过云进行通信的父/子关系。 安全通信，子设备需要能够验证父设备来自受信任的源。 否则，第三方可以设置恶意设备来模拟父项并截获通信。 

以下文章中详细介绍创建此信任关系的一种方法： 
* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [下游 （子） 设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>指定 DNS 服务器 

为了提高可靠性，强烈建议指定在环境中使用的 DNS 服务器地址。 请参阅两个选项[将 DNS 服务器设置为故障排除文章](troubleshoot.md#resolution-7)。

## <a name="optional-offline-settings"></a>可选脱机设置

如果你的设备处于脱机状态，IoT Edge 父设备将存储所有的设备到云消息，直到重新建立连接。 IoT Edge 中心模块管理的存储和转发的脱机消息。 对于更长的时间可能会脱机的设备，通过配置两个 IoT Edge 中心设置优化性能。 

首先，增加到实时设置，以便 IoT Edge 中心将保留你的设备以重新连接的足够长的时间的消息的时间。 然后，为消息存储添加更多磁盘空间。 

### <a name="time-to-live"></a>生存时间

生存时间设置是指在过期之前消息可以等待传递的时间量（以秒为单位）。 默认为 7200 秒（两个小时）。 此最大值仅受整数变量的最大值（约为 20 亿）限制。 

此设置是 IoT Edge 中心的所需属性，它存储在模块孪生中。 在 Azure 门户中或直接在部署清单中，可以配置它。 

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

可以在 Azure 门户的“配置高级 Edge 运行时设置”  部分配置环境变量和 IoT Edge 中心模块的创建选项。 或者，可以直接在部署清单中进行配置。 

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

例如，`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 表示主机系统上的目录 /etc/iotedge/storage  映射到容器上的目录 /iotedge/storage/  。 或是对于 Windows 系统的另一个示例，`"Binds":["C:\\temp:C:\\contemp"]` 表示主机系统上的目录 C:\\temp  映射到容器上的目录 C:\\contemp  。 

你还可以从 [docker 文档](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)中找到有关创建选项的更多详细信息。

## <a name="next-steps"></a>后续步骤

了解有关如何设置父/子设备连接的透明网关的详细信息： 

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [到 Azure IoT 中心下游设备进行身份验证](how-to-authenticate-downstream-device.md)
* [下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)
