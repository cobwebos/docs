---
title: "使用 Azure 网络观察程序管理数据包捕获 - Azure 门户 | Microsoft 文档"
description: "本页介绍如何使用 Azure 门户管理网络观察程序的数据包捕获功能"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 59edd945-34ad-4008-809e-ea904781d918
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 915901ee24d2427ea020db48370790e8ddca6d54
ms.lasthandoff: 04/03/2017

---

# <a name="manage-packet-captures-with-azure-network-watcher-using-the-portal"></a>在门户中使用 Azure 网络观察程序管理数据包捕获

> [!div class="op_single_selector"]
> - [Azure 门户](network-watcher-packet-capture-manage-portal.md)
> - [PowerShell](network-watcher-packet-capture-manage-powershell.md)
> - [CLI](network-watcher-packet-capture-manage-cli.md)
> - [REST API](network-watcher-packet-capture-manage-rest.md)

使用网络观察程序数据包捕获功能，可以创建捕获会话以跟踪进出虚拟机的流量。 为捕获会话提供了筛选器以确保仅捕获所需的流量。 数据包捕获有助于以主动和被动方式诊断网络异常。 其他用途包括收集网络统计信息，获得网络入侵信息，调试客户端与服务器之间的通信，等等。 由于能够远程触发数据包捕获，此功能可减轻手动运行数据包捕获的负担，并可在所需计算机上运行，从而可节省宝贵的时间。

本文将引导你完成当前可用于数据包捕获的不同管理任务。

- [**启动数据包捕获**](#start-a-packet-capture)
- [**停止数据包捕获**](#stop-a-packet-capture)
- [**删除数据包捕获**](#delete-a-packet-capture)
- [**下载数据包捕获**](#download-a-packet-capture)

## <a name="before-you-begin"></a>开始之前

本文假设已准备好以下资源：

- 要创建数据包捕获的区域中的网络观察程序实例
- 已启用数据包捕获扩展的虚拟机。

> [!IMPORTANT]
> 数据包捕获需要虚拟机扩展 `AzureNetworkWatcherExtension`。 有关在 Windows VM 上安装扩展的信息，请访问[适用于 Windows 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/windows/extensions-nwa.md)；有关 Linux VM 的信息，请访问[适用于 Linux 的 Azure 网络观察程序代理虚拟机扩展](../virtual-machines/linux/extensions-nwa.md)。

### <a name="packet-capture-agent-extension-through-the-portal"></a>通过门户安装数据包捕获代理扩展

若要通过门户安装数据包捕获 VM 代理，请导航到你的虚拟机，单击“扩展” > “添加”，然后搜索“适用于 Windows 的网络观察程序代理”

![代理视图][agent]

## <a name="packet-capture-overview"></a>数据包捕获概述

导航到 [Azure 门户](https://portal.azure.com)，然后单击“网络” > “网络观察程序” > “数据包捕获”

概述页显示现有的所有数据包捕获列表，不管其状态如何。

> [!NOTE]
> 数据包捕获需要通过端口 443 来与存储帐户建立连接。

![数据包捕获概述屏幕][1]

## <a name="start-a-packet-capture"></a>启动数据包捕获

单击“添加”创建数据包捕获。

可在数据包捕获中定义的属性包括：

**主要设置**

- **订阅** - 此值是使用的订阅。每个订阅中需要网络观察程序的一个实例。
- **资源组** - 用作目标的虚拟机资源组。
- **目标虚拟机** - 运行数据包捕获的虚拟机
- **数据包捕获名称** - 此值是数据包捕获的名称。

**捕获配置**

- **存储帐户** - 确定是否将数据包捕获保存在存储帐户中。
- **文件** - 确定是否将数据包捕获保存在虚拟机本地。
- **存储帐户** - 用于保存数据包捕获的选定存储帐户。 默认位置为 https://{storage account name}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscription id}/resourcegroups/{resource group name}/providers/microsoft.compute/virtualmachines/{virtual machine name}/{YY}/{MM}/{DD}/packetcapture_{HH}_{MM}_{SS}_{XXX}.cap。 （仅当选择了“存储”时才启用此选项）
- **本地文件路径** - 虚拟机上保存数据包捕获的本地路径。 （仅当选择了“文件”时才启用此选项）。 必须提供有效的路径
- **每个数据包的最大字节数** - 从每个数据包捕获的字节数，如果留空，将捕获所有字节。
- **每个会话的最大字节数** - 捕获的字节总数，一旦达到此值，就会停止数据包捕获。
- **时间限制(秒)** - 设置停止数据包捕获的时间限制。 默认值为 1800 秒。

> [!NOTE]
> 目前不支持使用高级存储帐户存储数据包捕获。

**筛选（可选）**

- **协议** - 要筛选的数据包捕获协议。 可用值为 TCP、UDP 和 Any。
- **本地 IP 地址** - 此值将数据包捕获筛选为本地 IP 地址与此筛选器值匹配的数据包。
- **本地端口** - 此值将数据包捕获筛选为本地端口与此筛选器值匹配的数据包。
- **远程 IP 地址** - 此值将数据包捕获筛选为远程 IP 与此筛选器值匹配的数据包。
- **远程端口** - 此值将数据包捕获筛选为远程端口与此筛选器值匹配的数据包。

> [!NOTE]
> 端口和 IP 地址值可以是单个值、值的范围或集。 （例如，可为端口指定 80-1024）。可以定义任意数量的筛选器。

填写值后，单击“确定”创建数据包捕获。

![创建数据包捕获][2]

超过针对数据包捕获设置的时间限制后，数据包捕获将会停止，可供查看。 也可以手动停止数据包捕获会话。

## <a name="delete-a-packet-capture"></a>删除数据包捕获

在数据包捕获视图中，单击**上下文菜单** (…) 或右键单击，然后单击“删除”即可停止数据包捕获

![删除数据包捕获][3]

> [!NOTE]
> 删除数据包捕获不会删除存储帐户中的文件。

系统会要求确认是否要删除数据包捕获，此时请单击“是”

![确认][4]

## <a name="stop-a-packet-capture"></a>停止数据包捕获

在数据包捕获视图中，单击**上下文菜单** (…) 或右键单击，然后单击“停止”即可停止数据包捕获

## <a name="download-a-packet-capture"></a>下载数据包捕获

完成数据包捕获会话后，捕获文件将上载到 Blob 存储或 VM 上的本地文件。 数据包捕获的存储位置是在创建会话时定义的。 用于访问这些保存到存储帐户的捕获文件的便利工具是 Microsoft Azure 存储资源管理器，可以在此处下载：http://storageexplorer.com/

如果指定了存储帐户，则数据包捕获文件将保存到以下位置的存储帐户：
```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>后续步骤

查看[创建警报触发的数据包捕获](network-watcher-alert-triggered-packet-capture.md)，了解如何利用虚拟机警报自动执行数据包捕获

访问[查看“IP 流验证”](network-watcher-check-ip-flow-verify-portal.md)，了解是否允许某些流量传入和传出 VM

<!-- Image references -->
[1]: ./media/network-watcher-packet-capture-manage-portal/figure1.png
[2]: ./media/network-watcher-packet-capture-manage-portal/figure2.png
[3]: ./media/network-watcher-packet-capture-manage-portal/figure3.png
[4]: ./media/network-watcher-packet-capture-manage-portal/figure4.png
[agent]: ./media/network-watcher-packet-capture-manage-portal/agent.png














