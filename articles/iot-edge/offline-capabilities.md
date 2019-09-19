---
title: 脱机操作设备 - Azure IoT Edge | Microsoft Docs
description: 了解 IoT Edge 设备和模块如何能够长时间在无 Internet 连接的情况下操作，以及 IoT Edge 如何使常规 IoT 设备也能脱机操作。
author: kgremban
ms.author: kgremban
ms.date: 08/04/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 80a38767121f5c54afe51a7d4d788716fe9547e2
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091363"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>了解有关 IoT Edge 设备、模块和子设备的扩展脱机功能

Azure IoT Edge 支持 IoT Edge 设备上的扩展脱机操作，同时在非 IoT Edge 子设备上启用脱机操作。 只要 IoT Edge 设备有机会连接到 IoT 中心，它和任何子设备就可以在间歇性或无 Internet 连接的情况下继续运作。 


## <a name="how-it-works"></a>工作原理

当 IoT Edge 设备进入脱机模式，IoT Edge 中心将扮演三个角色。 首先，它将存储任何向上游发送的消息并保存它们，直到设备重新连接。 其次，它代表 IoT 中心对模块和子设备进行身份验证，以便它们可以继续运行。 第三，它会在子设备之间启用通常通过 IoT 中心的通信。 

下面的示例展示了 IoT Edge 方案如何在脱机模式下运行：

1. **配置设备**

   IoT Edge 设备自动启用脱机功能。 若要将此功能扩展到其他 IoT 设备，需要在 IoT 中心声明设备之间的父子关系。 然后，将子设备配置为信任分配给它们的父设备，并通过父设备（用作网关）路由设备到云的通信。 

2. **与 IoT 中心同步**

   在安装 IoT Edge 运行时后，IoT Edge 设备至少要有一次处于联机状态，以便与 IoT 中心同步。 在此同步中，IoT Edge 设备将获取有关任何分配给它的子设备的详细信息。 IoT Edge 设备还可以安全更新本地缓存以启用脱机操作，并检索本地存储遥测消息的设置。 

3. **脱机**

   从 IoT 中心断开连接时，IoT Edge 设备及其部署模块和任何 IoT 子设备都可以无限期运行。 模块和子设备可以在脱机状态下通过在 IoT Edge 中心进行身份验证来启动和重新启动。 上游绑定到 IoT 中心的遥测存储在本地。 模块之间或 loT 子设备之间的通信通过直接方法或消息来维护。 

4. **与 IoT 中心重新连接和重新同步**

   一旦还原与 IoT 中心的连接，IoT Edge 设备会再次同步。 本地存储的消息按照它们存储的相同顺序传递。 模块和设备的所需属性和报告属性之间的差异已得到协调。 IoT Edge 设备更新对其分配的 IoT 子设备集所做的任何更改。

## <a name="restrictions-and-limits"></a>约束和限制

本文所述的扩展脱机功能可在 [IoT Edge 1.0.7 版或更高版本](https://github.com/Azure/azure-iotedge/releases)中获得。 早期版本有一个脱机功能子集。 不具备扩展脱机功能的现有 IoT Edge 设备不能通过更改运行时版本进行升级，但是必须用新的 IoT Edge 设备标识重新配置才能获得这些功能。 

除了美国东部以外，所有提供 IoT 中心的区域都提供扩展的脱机支持。

只能添加非 IoT Edge 设备作为子设备。 

IoT Edge 设备及其分配的子设备可以在初始一次性同步之后无限期脱机运行。但是，消息存储取决于生存时间 (TTL) 设置和存储消息的可用磁盘空间。 

## <a name="set-up-parent-and-child-devices"></a>设置父设备和子设备

对于将其扩展脱机功能扩展到 IoT 子设备的 IoT Edge 设备，需要完成两个步骤。 首先，在 Azure 门户中声明父子关系。 其次，在父设备与任何子设备之间建立信任关系，然后将设备到云的通信配置为通过父设备（用作网关）路由。 

### <a name="assign-child-devices"></a>分配子设备

子设备可以是注册到同一个 IoT 中心的任何非 IoT Edge 设备。 父设备可以有多个子设备，但一个子设备只能有一个父设备。 可以使用三个选项在 Edge 设备中设置子设备：使用 Azure 门户、Azure CLI 或 IoT 中心服务 SDK。 

以下部分举例说明如何在 IoT 中心为现有的 IoT 设备声明父/子关系。 若要为子设备创建新的设备标识，请参阅[在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)了解详细信息。

#### <a name="option-1-iot-hub-portal"></a>选项 1：IoT 中心门户

可以在创建新设备时声明父子关系。 或者，对于现有的设备，可以从 IoT Edge 父设备或 IoT 子设备的设备详细信息页声明关系。 

   ![从 IoT Edge 设备的详细信息页管理子设备](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>选项 2：使用 `az` 命令行工具

将 [Azure 命令行接口](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)与 [IoT 扩展](https://github.com/azure/azure-iot-cli-extension)（v0.7.0 或更高版本）配合使用时，可以通过 [device-identity](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest) 子命令管理父子关系。 以下示例使用一个查询将中心内的所有非 IoT Edge 设备分配为 IoT Edge 设备的子设备。 

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

### <a name="set-up-the-parent-device-as-a-gateway"></a>将父设备设为网关

可将父/子关系视为一种透明网关，其中的子设备在 IoT 中心具有自身的标识，但通过其父设备在云中通信。 若要安全通信，子设备需能够验证父设备来自受信任的源。 否则，第三方可能会设置恶意设备来模拟父设备并截获通信。 

以下文章详细介绍了建立这种信任关系的一种方法： 
* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [将下游（子）设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>指定 DNS 服务器 

为了提高可靠性，强烈建议指定在环境中使用的 DNS 服务器地址。 若要为 IoT Edge 设置 DNS 服务器，请参阅故障排除一文中的["边缘代理模块](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device)的解决方法"。

## <a name="optional-offline-settings"></a>可选脱机设置

如果设备已脱机，IoT Edge 父设备将一直存储所有设备到云的消息，直到重新建立连接为止。 IoT Edge 中心模块将管理脱机消息的存储和转发。 对于长时间脱机的设备，可以通过配置两项 IoT Edge 中心设置来优化性能。 

首先，增大活动设置的时间，以便在设备重新建立连接之前，IoT Edge 中心将消息保留足够长的时间。 然后，为消息存储添加更多磁盘空间。 

### <a name="time-to-live"></a>生存时间

生存时间设置是指在过期之前消息可以等待传递的时间量（以秒为单位）。 默认为 7200 秒（两个小时）。 此最大值仅受整数变量的最大值（约为 20 亿）限制。 

此设置是 IoT Edge 中心的所需属性，它存储在模块孪生中。 可以在 Azure 门户中或者直接在部署清单中配置此项设置。 

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

### <a name="host-storage-for-system-modules"></a>系统模块的主机存储

默认情况下，消息和模块状态信息存储在 IoT Edge 中心的本地容器文件系统中。 为了提高可靠性，尤其是在脱机操作时，还可以在主机 IoT Edge 设备上存储存储。

若要在主机系统上设置存储，请为指向容器中的存储文件夹的 IoT Edge 中心和 IoT Edge 代理创建环境变量。 然后，使用创建选项将存储文件夹绑定到主机上的文件夹。 

可以在 Azure 门户的“配置高级 Edge 运行时设置”部分配置环境变量和 IoT Edge 中心模块的创建选项。 

1. 对于 IoT Edge 中心和 IoT Edge 代理，添加一个名为**storageFolder**的环境变量，指向模块中的一个目录。
1. 对于 IoT Edge 集线器和 IoT Edge 代理，添加 "绑定"，将主计算机上的本地目录连接到模块中的一个目录。 例如： 

   ![为本地存储添加创建选项和环境变量](./media/offline-capabilities/offline-storage.png)

或者，你可以在部署清单中直接配置本地存储。 例如： 

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

将`<HostStoragePath>` 和`<ModuleStoragePath>`替换为主机和模块存储路径; 这两个值必须是绝对路径。 

例如，`"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` 表示主机系统上的目录 /etc/iotedge/storage 映射到容器上的目录 /iotedge/storage/。 或是对于 Windows 系统的另一个示例，`"Binds":["C:\\temp:C:\\contemp"]` 表示主机系统上的目录 C:\\temp 映射到容器上的目录 C:\\contemp。 

在 Linux 设备上，请确保 IoT Edge 集线器的用户配置文件 "UID 1000" 具有主机系统目录的 "读取"、"写入" 和 "执行" 权限。 这些权限是必需的，以便 IoT Edge 中心可以将消息存储在目录中，并在以后检索这些消息。 （IoT Edge 代理以 root 身份运行，因此无需其他权限。）管理 Linux 系统上的目录权限有多种方法，包括使用`chown`更改目录所有者，然后`chmod`更改权限。 例如：

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

可以在[docker 文档](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)中找到有关创建选项的更多详细信息。

## <a name="next-steps"></a>后续步骤

详细了解如何为父/子设备连接设置透明网关： 

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [通过 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)
* [将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)
