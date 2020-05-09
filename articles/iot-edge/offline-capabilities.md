---
title: 脱机操作设备 - Azure IoT Edge | Microsoft Docs
description: 了解 IoT Edge 设备和模块如何能够长时间在无 Internet 连接的情况下操作，以及 IoT Edge 如何使常规 IoT 设备也能脱机操作。
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 588926a90d9a40c00bca4914dc1d5ed08301ff75
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780787"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>了解有关 IoT Edge 设备、模块和子设备的扩展脱机功能

Azure IoT Edge 支持 IoT Edge 设备上的扩展脱机操作，同时在非 IoT Edge 子设备上启用脱机操作。 只要 IoT Edge 设备有机会连接到 IoT 中心，该设备和任何子设备就可以在间歇性或无 Internet 连接的情况下继续运作。

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

   一旦还原与 IoT 中心的连接，IoT Edge 设备会再次同步。 本地存储的消息会立即传递到 IoT 中心，但取决于连接速度、IoT 中心延迟和相关因素。 这些消息会按照存储它们的相同顺序传递。

   模块和设备的所需属性和报告属性之间的差异已得到协调。 IoT Edge 设备更新对其分配的 IoT 子设备集所做的任何更改。

## <a name="restrictions-and-limits"></a>约束和限制

本文所述的扩展脱机功能可在 [IoT Edge 1.0.7 版或更高版本](https://github.com/Azure/azure-iotedge/releases)中获得。 早期版本有一个脱机功能子集。 不具备扩展脱机功能的现有 IoT Edge 设备不能通过更改运行时版本进行升级，但是必须用新的 IoT Edge 设备标识重新配置才能获得这些功能。

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

```azurecli
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

为了提高可靠性，强烈建议指定在环境中使用的 DNS 服务器地址。 若要为 IoT Edge 设置 DNS服务器，请参阅故障排除文章中 [Edge 代理模块不断报告“空配置文件”且设备上没有模块启动](troubleshoot-common-errors.md#edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device)的解决方案。

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

默认情况下，消息和模块状态信息存储在 IoT Edge 中心的本地容器文件系统中。 若要改进可靠性，尤其是在脱机操作时改进可靠性，也可在主机 IoT Edge 设备上设置专用存储。 有关详细信息，请参阅[向模块授予对设备本地存储的访问权限](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>后续步骤

详细了解如何为父/子设备连接设置透明网关：

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [在 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)
* [将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)
