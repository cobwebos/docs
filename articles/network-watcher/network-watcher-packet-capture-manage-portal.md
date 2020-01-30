---
title: 管理数据包捕获-Azure 门户
titleSuffix: Azure Network Watcher
description: 了解如何使用 Azure 门户管理网络观察程序的数据包捕获功能。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: damendo
ms.openlocfilehash: 6fc4a25e39fb8f27151b2e3bec1959d74a619233
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840821"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>在门户中使用 Azure 网络观察程序管理数据包捕获

使用网络观察程序数据包捕获，可以创建捕获会话以跟踪进出虚拟机的流量。 为捕获会话提供了筛选器以确保仅捕获所需的流量。 数据包捕获有助于以主动和被动方式诊断网络异常。 其他用途包括收集网络统计信息，获得网络入侵信息，调试客户端与服务器之间的通信，等等。 远程触发数据包捕获能够减轻在所需虚拟机上手动运行数据包捕获的负担，并节省宝贵的时间。

本文介绍如何启动、停止、下载和删除数据包捕获。 

## <a name="before-you-begin"></a>开始之前

数据包捕获需要以下连接：
* 通过端口 443 与存储帐户建立的出站连接。
* 与 169.254.169.254 建立的入站和出站连接
* 与 168.63.129.16 建立的入站和出站连接

如果某个网络安全组已关联到网络接口或该网络接口所在的子网，请确保存在允许上述端口的规则。 同样，将用户定义的流量路由添加到网络可能会阻止与以上提到的 Ip 和端口建立连接。 请确保可以访问它们。 

## <a name="start-a-packet-capture"></a>启动数据包捕获

1. 在浏览器中导航到 [Azure 门户](https://portal.azure.com)，选择“所有服务”，然后在“网络”部分选择“网络观察程序”。
2. 选择“网络诊断工具”下的“数据包捕获”。 此时会列出所有现有的数据包捕获，不管其状态如何。
3. 选择“添加”以创建数据包捕获。 可以选择以下属性的值：
   - **订阅**：要为其创建数据包捕获的虚拟机所在的订阅。
   - **资源组**：虚拟机的资源组。
   - **目标虚拟机**：要为其创建数据包捕获的虚拟机。
   - **数据包捕获名称**：数据包捕获的名称。
   - **存储帐户或文件**：选择“存储帐户”和/或“文件”。 如果选择“文件”，则捕获将写入虚拟机中的某个路径。
   - **本地文件路径**：虚拟机上要将数据包捕获保存到的本地路径（仅当选择了“文件”时才有效）。 该路径必须是有效路径。 如果使用 Linux 虚拟机，则路径必须以 */var/captures* 开头。
   - **存储帐户**：如果选择了“存储帐户”，请选择现有的存储帐户。 仅当选择了“存储”时，此选项才可用。
   
     > [!NOTE]
     > 目前不支持使用高级存储帐户存储数据包捕获。

   - **每个数据包的最大字节数**：从每个数据包捕获的字节数。 如果留空，将捕获所有字节。
   - **每个会话的最大字节数**：捕获的字节总数。 一旦达到此值，就会停止数据包捕获。
   - **时间限制(秒)** ：停止数据包捕获之前所要达到的时间限制。 默认值为 18,000 秒。
   - 筛选（可选） 选择“+ 添加筛选器”
     - **协议**：要筛选的数据包捕获协议。 可用值为 TCP、UDP 和 Any。
     - **本地 IP 地址**：将数据包捕获筛选为本地 IP 地址与此值匹配的数据包。
     - **本地端口**：将数据包捕获筛选为本地端口与此值匹配的数据包。
     - **远程 IP 地址**：将数据包捕获筛选为远程 IP 地址与此值匹配的数据包。
     - **远程端口**：将数据包捕获筛选为远程端口与此值匹配的数据包。
    
     > [!NOTE]
     > 端口和 IP 地址值可以是端口的单个值、值范围或某个范围，例如 80-1024。 可以定义任意数量的筛选器。

4. 选择“确定”。

超过针对数据包捕获设置的时间限制后，数据包捕获会停止，可供查看。 也可以手动停止数据包捕获会话。

> [!NOTE]
> 门户自动执行以下操作：
>  * 如果选定虚拟机所在的同一区域中没有网络观察程序，则在该区域中创建一个网络观察程序。
>  * 如果尚未安装，则将*AzureNetworkWatcherExtension* [Linux](../virtual-machines/linux/extensions-nwa.md)或[Windows](../virtual-machines/windows/extensions-nwa.md)虚拟机扩展添加到虚拟机。

## <a name="delete-a-packet-capture"></a>删除数据包捕获

1. 在数据包捕获视图中，选择数据包捕获右侧的“...”，或右键单击现有数据包捕获并选择“删除”。
2. 系统会要求确认是否要删除数据包捕获。 请选择“是”。

> [!NOTE]
> 删除数据包捕获不会删除存储帐户或虚拟机中的捕获文件。

## <a name="stop-a-packet-capture"></a>停止数据包捕获

在数据包捕获视图中，选择数据包捕获右侧的“...”，或右键单击现有数据包捕获并选择“停止”。

## <a name="download-a-packet-capture"></a>下载数据包捕获

完成数据包捕获会话后，捕获文件将上传到 Blob 存储或虚拟机中的本地文件。 数据包捕获的存储位置是在创建数据包捕获期间定义的。 使用可[下载](https://storageexplorer.com/)的 Microsoft Azure 存储资源管理器工具可以方便访问存储帐户中保存的捕获文件。

如果指定了存储帐户，则数据包捕获文件将保存到以下位置的存储帐户：

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

如果创建捕获时选择了“文件”，可以从虚拟机上配置的路径查看或下载该文件。

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用虚拟机警报自动执行数据包捕获，请参阅[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)。
- 若要确定是否允许传入和传出虚拟机的特定流量，请参阅[诊断虚拟机网络流量筛选器问题](diagnose-vm-network-traffic-filtering-problem.md)。
