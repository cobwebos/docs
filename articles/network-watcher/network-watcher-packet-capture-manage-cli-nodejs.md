---
title: 使用 Azure 网络观察程序管理数据包捕获 - Azure CLI 1.0 | Microsoft Docs
description: 此页说明如何使用 Azure CLI 1.0 管理网络观察程序的数据包捕获功能
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: c74d1a94971495f7cd5f5bed42b33869fa9710d9
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
ms.locfileid: "32185379"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-cli-10"></a>通过 Azure CLI 1.0 使用 Azure 网络观察程序管理数据包捕获

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI 1.0](network-watcher-packet-capture-manage-cli-nodejs.md)
> - [CLI 2.0](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

使用网络观察程序数据包捕获，可以创建捕获会话以跟踪进出虚拟机的流量。 为捕获会话提供了筛选器以确保仅捕获所需的流量。 数据包捕获有助于以主动和被动方式诊断网络异常。 其他用途包括收集网络统计信息，获得网络入侵信息，调试客户端与服务器之间的通信，等等。 由于能够远程触发数据包捕获，此功能可减轻手动运行数据包捕获的负担，并可在所需计算机上运行，从而可节省宝贵的时间。

本文使用适用于 Windows、Mac 和 Linux 的跨平台 Azure CLI 1.0。

本文将引导完成当前可用于数据包捕获的不同管理任务。

- [**启动数据包捕获**](#start-a-packet-capture)
- [**停止数据包捕获**](#stop-a-packet-capture)
- [**删除数据包捕获**](#delete-a-packet-capture)
- [**下载数据包捕获**](#download-a-packet-capture)

## <a name="before-you-begin"></a>开始之前

本文假定你拥有以下资源：

- 要创建数据包捕获的区域中的网络观察程序实例
- 已启用数据包捕获扩展的虚拟机。

> [!IMPORTANT]
> 数据包捕获要求在虚拟机上运行代理。 已安装代理作为扩展。 有关 VM 扩展的说明，请访问[虚拟机扩展和功能](../virtual-machines/windows/extensions-features.md)。

## <a name="install-vm-extension"></a>安装 VM 扩展

### <a name="step-1"></a>步骤 1

在来宾虚拟机上运行 `azure vm extension set` cmdlet 以安装数据包捕获代理。

对于 Windows 虚拟机：

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentWindows -o 1.4
```

对于 Linux 虚拟机：

```azurecli
azure vm extension set -g resourceGroupName -m virtualMachineName -p Microsoft.Azure.NetworkWatcher -r AzureNetworkWatcherExtension -n NetworkWatcherAgentLinux -o 1.4
````

### <a name="step-2"></a>步骤 2

若要确保已安装代理，请运行 `vm extension get` cmdlet 并向其传递资源组和虚拟机的名称。 检查结果列表，以确保已安装代理。

```azurecli
azure vm extension get -g resourceGroupName -m virtualMachineName
```

以下示例是运行 `azure vm extension get` 后的响应的实例

```
info:    Executing command vm extension get
+ Looking up the VM "virtualMachineName"
data:    Publisher                       Name                        Version  State
data:    ------------------------------  -----------------------     -------  ---------
data:    Microsoft.Azure.NetworkWatcher  NetworkWatcherAgentWindows  1.4      Succeeded
info:    vm extension get command OK
```

## <a name="start-a-packet-capture"></a>启动数据包捕获

完成前面的步骤后，数据包捕获代理将安装在虚拟机上。

### <a name="step-1"></a>步骤 1

下一步是检索网络观察程序实例。 将此变量传递给步骤 4 中的 `network watcher show` cmdlet。

```azurecli
azure network watcher show -g resourceGroup -n networkWatcherName
```

### <a name="step-2"></a>步骤 2

检索存储帐户。 此存储帐户用于存储数据包捕获文件。

```azurecli
azure storage account list
```

### <a name="step-3"></a>步骤 3

可以使用筛选器来限制数据包捕获存储的数据。 以下示例为数据包捕获设置了多个筛选器。  前三个筛选器仅收集从本地 IP 10.0.0.3 发往目标端口 20、80 和 443 的传出 TCP 流量。  最后一个筛选器仅收集 UDP 流量。

```azurecli
azure network watcher packet-capture create -g resourceGroupName -w networkWatcherName -n packetCaptureName -t targetResourceId -o storageAccountResourceId -f "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

可以为数据包捕获定义多个筛选器。 如果要使用复杂的筛选器结构，最好将筛选器用作 json 文件来避免语法错误。 例如，使用标志“-r”（而不是“-f”），并传递包含以下筛选器的 json 文件的位置：

```json
[
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"20"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"80"
    },
    {
        "protocol":"TCP",
        "remoteIPAddress":"1.1.1.1-255.255.255",
        "localIPAddress":"10.0.0.3",
        "remotePort":"443"
    },
    {
        "protocol":"UDP"
    }
]
```


以下示例是运行 `network watcher packet-capture create` cmdlet 后的预期输出。

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture create command OK
```

## <a name="get-a-packet-capture"></a>获取数据包捕获

运行 `network watcher packet-capture show` cmdlet，检索当前正在运行的或已完成的数据包捕获的状态。

```azurecli
azure network watcher packet-capture show -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

以下示例是 `network watcher packet-capture show` cmdlet 的输出。 以下示例是捕获完成后的输出结果。 PacketCaptureStatus 值为“已停止”，StopReason 为 TimeExceeded。 此值显示数据包捕获已成功完成，并已运行了限定的时间。

```
data:    Name                            : packetCaptureName
data:    Etag                            : W/"d59bb2d2-dc95-43da-b740-e0ef8fcacecb"
data:    Target                          : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Compute/virtualMachines/testVM
data:    Bytes To Capture Per Packet     : 0
data:    Total Bytes Per Session         : 1073741824
data:    Time Limit In Seconds           : 18000
data:    Storage Location:
data:      Storage Id                    : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Storage/storageAccounts/testStorage
data:      Storage Path                  : https://testStorage.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/testRG/providers/microsoft.compute/virtualmachines/testVM/2017/02/17/packetcapture_01_21_18_145.cap
data:    Filters                         : []
data:    Provisioning State              : Succeeded
info:    network watcher packet-capture show command OK
```

## <a name="stop-a-packet-capture"></a>停止数据包捕获

运行 `network watcher packet-capture stop` cmdlet 后，如果捕获会话正在进行，它将停止。

```azurecli
azure network watcher packet-capture stop -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> 该 cmdlet 在当前正在运行的捕获会话或已停止的现有会话中运行时，将不返回任何响应。

## <a name="delete-a-packet-capture"></a>删除数据包捕获

```azurecli
azure network watcher packet-capture delete -g resourceGroupName -w networkWatcherName -n packetCaptureName
```

> [!NOTE]
> 删除数据包捕获不会删除存储帐户中的文件。

## <a name="download-a-packet-capture"></a>下载数据包捕获

完成数据包捕获会话后，可以将捕获文件上传到 blob 存储或 VM 上的本地文件。 数据包捕获的存储位置是在创建会话时定义的。 用于访问这些保存到存储帐户的捕获文件的便利工具是 Microsoft Azure 存储资源管理器，下载地址为：http://storageexplorer.com/

如果指定了存储帐户，则数据包捕获文件将保存到以下位置的存储帐户：

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>后续步骤

查看[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)，了解如何利用虚拟机警报自动执行数据包捕获

访问[查看“IP 流验证”](diagnose-vm-network-traffic-filtering-problem.md)，了解是否允许某些流量传入和传出 VM

<!-- Image references -->
