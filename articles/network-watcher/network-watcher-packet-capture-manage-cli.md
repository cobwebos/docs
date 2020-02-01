---
title: 通过 Azure 网络观察程序管理数据包捕获-Azure CLI |Microsoft Docs
description: 本页说明如何使用 Azure CLI 管理网络观察程序的数据包捕获功能
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: f83fb2377f2db1deaed453131a61e26677b3d87d
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76896396"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>使用 Azure 网络观察程序管理数据包捕获使用 Azure CLI

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

网络观察程序数据包捕获允许您创建捕获会话以跟踪进出虚拟机的流量。 为捕获会话提供了筛选器，以确保只捕获所需的流量。 数据包捕获可帮助同时被动和主动诊断网络异常。 其他用途包括收集网络统计信息、获取有关网络入侵的信息、调试客户端与服务器之间的通信，等等。 由于能够远程触发数据包捕获，此功能可减轻手动运行数据包捕获的负担，并使在所需的计算机上能够节省宝贵的时间。

若要执行本文中的步骤，需要[安装适用于 Mac、Linux 和 Windows 的 Azure 命令行接口（Azure CLI）](/cli/azure/install-azure-cli)。

本文将指导你完成当前可用于数据包捕获的不同管理任务。

- [**启动数据包捕获**](#start-a-packet-capture)
- [**停止数据包捕获**](#stop-a-packet-capture)
- [**删除数据包捕获**](#delete-a-packet-capture)
- [**下载数据包捕获**](#download-a-packet-capture)

## <a name="before-you-begin"></a>开始之前

本文假定你拥有以下资源：

- 要创建数据包捕获的区域中的网络观察程序实例
- 启用了数据包捕获扩展的虚拟机。

> [!IMPORTANT]
> 数据包捕获需要在虚拟机上运行代理。 代理安装为扩展。 有关 VM 扩展的说明，请访问[虚拟机扩展和功能](../virtual-machines/windows/extensions-features.md)。

## <a name="install-vm-extension"></a>安装 VM 扩展

### <a name="step-1"></a>步骤1

运行 `az vm extension set` 命令，在来宾虚拟机上安装数据包捕获代理。

对于 Windows 虚拟机：

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

对于 Linux 虚拟机：

```azurecli
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>步骤2

若要确保已安装代理，请运行 `vm extension show` 命令，并向其传递资源组和虚拟机名称。 检查生成的列表以确保已安装代理。

对于 Windows 虚拟机：
```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

对于 Linux 虚拟机：
```azurecli
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

以下示例是运行的响应的示例 `az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>启动数据包捕获

完成上述步骤后，将在虚拟机上安装数据包捕获代理。


### <a name="step-1"></a>步骤1

检索存储帐户。 此存储帐户用于存储数据包捕获文件。

```azurecli
az storage account list
```

### <a name="step-2"></a>步骤2

此时，您已准备好创建数据包捕获。  首先，让我们检查你可能想要配置的参数。 筛选器是一个此类参数，可用于限制数据包捕获存储的数据。 下面的示例使用多个筛选器设置数据包捕获。  前三个筛选器仅收集从本地 IP 10.0.0.3 到目标端口20、80和443的传出 TCP 流量。  最后一个筛选器仅收集 UDP 流量。

```azurecli
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

下面的示例是运行 `az network watcher packet-capture create` 命令的预期输出。

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>获取数据包捕获

运行 `az network watcher packet-capture show-status` 命令，检索当前正在运行的或已完成的数据包捕获的状态。

```azurecli
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

下面的示例是 `az network watcher packet-capture show-status` 命令的输出。 以下示例是停止捕获时，StopReason 为 TimeExceeded。 

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>停止数据包捕获

通过运行 `az network watcher packet-capture stop` 命令，如果捕获会话正在进行，则它将停止。

```azurecli
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> 在当前正在运行的捕获会话或已停止的现有会话上运行时，该命令将返回 "无响应"。

## <a name="delete-a-packet-capture"></a>删除数据包捕获

```azurecli
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> 删除数据包捕获不会删除存储帐户中的文件。

## <a name="download-a-packet-capture"></a>下载数据包捕获

完成数据包捕获会话后，可以将捕获文件上传到 blob 存储或 VM 上的本地文件。 数据包捕获的存储位置是在创建会话时定义的。 用于访问保存到存储帐户的捕获文件的便利工具是 Microsoft Azure 存储资源管理器的，可在此处下载： https://storageexplorer.com/

如果指定了存储帐户，则会将数据包捕获文件保存到位于以下位置的存储帐户：

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>后续步骤

查看[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)，了解如何使用虚拟机警报自动执行数据包捕获

访问[检查 IP 流验证](diagnose-vm-network-traffic-filtering-problem.md)，查找是否允许某些流量传入或传出 VM

<!-- Image references -->
