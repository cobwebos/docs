---
title: 脱机操作设备 - Azure IoT Edge | Microsoft Docs
description: 了解 IoT Edge 设备和模块如何能够长时间在无 Internet 连接的情况下操作，以及 IoT Edge 如何使常规 IoT 设备也能脱机操作。
author: kgremban
ms.author: kgremban
ms.date: 11/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 951c81b2d65fe17f6e79dbdd699051ba43b86c49
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867377"
---
# <a name="understand-extended-offline-capabilities-for-iot-edge-devices-modules-and-child-devices"></a>了解 IoT Edge 设备、模块和子设备的扩展脱机功能

Azure IoT Edge 支持对 IoT Edge 设备进行扩展的脱机操作，并对非 IoT Edge 子设备启用脱机操作。 只要 IoT Edge 设备有机会连接到 IoT 中心，该设备和任何子设备都可继续正常运行，或者无法连接到 internet。

## <a name="how-it-works"></a>如何运作

当 IoT Edge 设备进入脱机模式，IoT Edge 中心将扮演三个角色。 首先，它将存储任何向上游发送的消息并保存它们，直到设备重新连接。 其次，它代表 IoT 中心对模块和子设备进行身份验证，以便它们可以继续运行。 第三，它会在子设备之间启用通常通过 IoT 中心的通信。

下面的示例展示了 IoT Edge 方案如何在脱机模式下运行：

1. **配置设备**

   IoT Edge 设备自动启用脱机功能。 若要将此功能扩展到其他 IoT 设备，需要在 IoT 中心声明设备之间的父子关系。 然后，将子设备配置为信任其分配的父设备，并通过父级将设备到云的通信作为网关进行路由。

2. **与 IoT 中心同步**

   在安装 IoT Edge 运行时后，IoT Edge 设备至少要有一次处于联机状态，以便与 IoT 中心同步。 在此同步中，IoT Edge 设备将获取有关任何分配给它的子设备的详细信息。 IoT Edge 设备还可以安全更新本地缓存以启用脱机操作，并检索本地存储遥测消息的设置。

3. **脱机**

   从 IoT 中心断开连接时，IoT Edge 设备及其部署模块和任何 IoT 子设备都可以无限期运行。 模块和子设备可以在脱机状态下通过在 IoT Edge 中心进行身份验证来启动和重新启动。 上游绑定到 IoT 中心的遥测存储在本地。 模块之间或 loT 子设备之间的通信通过直接方法或消息来维护。

4. **重新连接和重新同步 IoT 中心**

   一旦还原与 IoT 中心的连接，IoT Edge 设备会再次同步。 本地存储的消息立即传递到 IoT 中心，但依赖于连接速度、IoT 中心延迟以及相关因素。 它们的传递顺序与存储它们的顺序相同。

   模块和设备的所需属性和报告属性之间的差异已得到协调。 IoT Edge 设备更新对其分配的 IoT 子设备集所做的任何更改。

## <a name="restrictions-and-limits"></a>约束和限制

本文中介绍的扩展脱机功能[IoT Edge 版本1.0.7 或更高版本](https://github.com/Azure/azure-iotedge/releases)中可用。 早期版本有一个脱机功能子集。 不具备扩展脱机功能的现有 IoT Edge 设备不能通过更改运行时版本进行升级，但是必须用新的 IoT Edge 设备标识重新配置才能获得这些功能。

只有非 IoT Edge 设备才能作为子设备添加。

在初次运行一次性同步之后，IoT Edge 设备及其分配的子设备可能会无限期地脱机工作。但是，消息的存储取决于生存时间（TTL）设置和用于存储消息的可用磁盘空间。

## <a name="set-up-parent-and-child-devices"></a>设置父设备和子设备

要使 IoT Edge 设备将其扩展的脱机功能扩展到子 IoT 设备，需要完成两个步骤。 首先，在 Azure 门户中声明父子关系。 其次，在父设备和任何子设备之间创建信任关系，然后配置设备到云的通信，将父设备作为网关。 

### <a name="assign-child-devices"></a>分配子设备

子设备可以是注册到相同 IoT 中心的任何非 IoT Edge 设备。 父设备可以有多个子设备，但子设备只能有一个父设备。 有三个选项可用于将子设备设置到边缘设备：通过 Azure 门户、使用 Azure CLI 或使用 IoT 中心服务 SDK。 

以下各节提供了有关如何在 IoT 中心中声明现有 IoT 设备的父/子关系的示例。 如果你要为子设备创建新的设备标识，请参阅对[Azure IoT 中心的下游设备进行身份验证](how-to-authenticate-downstream-device.md)，了解详细信息。

#### <a name="option-1-iot-hub-portal"></a>选项1： IoT 中心门户

创建新设备时，可以声明父子关系。 对于现有设备，您可以从父 IoT Edge 设备或子 IoT 设备的 "设备详细信息" 页声明关系。 

   ![从 IoT Edge 设备的详细信息页管理子设备](./media/offline-capabilities/manage-child-devices.png)


#### <a name="option-2-use-the-az-command-line-tool"></a>选项2：使用 `az` 命令行工具

将[Azure 命令行接口](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)用于[IoT 扩展](https://github.com/azure/azure-iot-cli-extension)（v 0.7.0 编写或更高版本），可以使用[设备标识](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub/device-identity?view=azure-cli-latest)子命令管理父子关系。 下面的示例使用查询将集线器中的所有非 IoT Edge 设备分配为 IoT Edge 设备的子设备。 

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

您可以修改[查询](../iot-hub/iot-hub-devguide-query-language.md)以选择其他设备的子集。 如果指定大量设备，此命令可能需要几秒钟。

#### <a name="option-3-use-iot-hub-service-sdk"></a>选项3：使用 IoT 中心服务 SDK 

最后，你可以使用C#Java 或 Node.js IoT 中心服务 SDK 以编程方式管理父子关系。 下面是使用C# SDK[分配子设备的示例](https://aka.ms/set-child-iot-device-c-sharp)。

### <a name="set-up-the-parent-device-as-a-gateway"></a>将父设备设置为网关

可以将父/子关系视为透明网关，其中，子设备在 IoT 中心具有其自己的标识，但通过其父级通过云进行通信。 为了安全地通信，子设备需要能够验证父设备是否来自受信任的源。 否则，第三方可能会设置恶意设备来模拟父项和拦截通信。 

以下文章中详细介绍了创建此信任关系的一种方法：

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [将下游（子）设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)

## <a name="specify-dns-servers"></a>指定 DNS 服务器 

为了提高可靠性，强烈建议您指定在您的环境中使用的 DNS 服务器地址。 若要为 IoT Edge 设置 DNS 服务器，请参阅故障排除一文中的["边缘代理模块](troubleshoot.md#edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device)的解决方法"。

## <a name="optional-offline-settings"></a>可选脱机设置

如果设备处于脱机状态，则在重新建立连接之前，IoT Edge 父设备将存储所有设备到云的消息。 IoT Edge 中心模块管理脱机消息的存储和转发。 对于长时间脱机的设备，可通过配置两个 IoT Edge 集线器设置来优化性能。 

首先，增加 "生存时间" 设置，以便 IoT Edge 中心将消息保留足够长的时间，以便重新连接设备。 然后，为消息存储添加更多磁盘空间。 

### <a name="time-to-live"></a>生存时间

生存时间设置是指在过期之前消息可以等待传递的时间量（以秒为单位）。 默认为 7200 秒（两个小时）。 最大值仅受整数变量的最大值限制，此值大约为2000000000。 

此设置是 IoT Edge 中心的所需属性，它存储在模块孪生中。 你可以在 Azure 门户中或直接在部署清单中进行配置。 

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

默认情况下，消息和模块状态信息存储在 IoT Edge 中心的本地容器文件系统中。 为了提高可靠性，尤其是在脱机操作时，还可以在主机 IoT Edge 设备上存储存储。 有关详细信息，请参阅[为模块提供对设备的本地存储的访问权限](how-to-access-host-storage-from-module.md)

## <a name="next-steps"></a>后续步骤

详细了解如何为父/子设备连接设置透明网关： 

* [配置 IoT Edge 设备以充当透明网关](how-to-create-transparent-gateway.md)
* [通过 Azure IoT 中心对下游设备进行身份验证](how-to-authenticate-downstream-device.md)
* [将下游设备连接到 Azure IoT Edge 网关](how-to-connect-downstream-device.md)
