---
title: "对 Azure 虚拟网络网关和连接进行故障排除 - Azure CLI | Microsoft 文档"
description: "此页说明如何使用 Azure 网络观察程序对 Azure CLI 进行故障排除"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 2838bc61-b182-4da8-8533-27db8fdbd177
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 30475d710dc00ff06683dcb963e9fdfdd762735b
ms.lasthandoff: 02/23/2017


---

# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-azure-cli"></a>使用 Azure 网络观察程序 Azure CLI 对虚拟网络网关和连接进行故障排除

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI](network-watcher-troubleshoot-manage-cli.md)
> - [REST API](network-watcher-troubleshoot-manage-rest.md)

网络观察程序提供了许多功能，因为它关系到了解 Azure 中的网络资源。 其中一项功能就是资源故障排除。 可以通过 PowerShell、CLI 或 REST API 调用资源故障排除。 调用后，网络观察程序将检查虚拟网络网关或连接的运行状况，并返回查找结果。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="before-you-begin"></a>开始之前

此方案假定你已按照[创建网络观察程序](network-watcher-create.md)中的步骤创建网络观察程序。

## <a name="overview"></a>概述

“资源故障排除”提供对使用虚拟网络网关和连接时发生的问题进行故障排除的功能。 向“资源故障排除”发出请求时，系统将查询并检查日志。 检查完成后，将返回结果。 资源故障排除请求是长时间运行的请求，可能需要好几分钟才返回结果。 故障排除日志存储在指定的存储帐户上的容器中。

## <a name="retrieve-a-virtual-network-gateway-connection"></a>检索虚拟网络网关连接

在此示例中，将针对连接运行资源故障排除。 还可以向其传递虚拟网络网关。 以下 cmdlet 将列出资源组中的 vpn 连接。

```azurecli
azure network vpn-connection list -g resourceGroupName
```

还可以运行命令以查看订阅中的连接。

```azurecli
azure network vpn-connection list -s subscription
```

知道存储帐户的名称后，可以运行此命令来获取其资源 ID：

```azurecli
azure network vpn-connection show -g resourceGroupName -n connectionName
```

## <a name="create-a-storage-account"></a>创建存储帐户

资源故障排除返回有关资源运行状况的数据，还将日志保存到要查看的存储帐户中。 在此步骤中，我们将创建一个存储帐户（如果存在现有的存储帐户，你可以使用它）。

```azurecli
azure storage account create -n storageAccountName -l location -g resourceGroupName
```

## <a name="run-network-watcher-resource-troubleshooting"></a>运行网络观察程序资源故障排除

使用 `network watcher troubleshoot` cmdlet 对资源进行故障排除。 我们将向该 cmdlet 传递资源组、网络观察程序的名称、连接的 ID、存储帐户 的 ID 以及要在其中存储故障排除结果的 blob 的路径。

```azurecli
azure network watcher -g resourceGroupName -n networkWatcherName -t connectionId -i storageId -p storagePath
```

运行 cmdlet 后，网络观察程序将查看资源以确认运行状况。 它将结果返回到 shell，并将结果的日志存储在指定的存储帐户中。

## <a name="understanding-the-results"></a>了解结果

操作文本提供有关如何解决此问题的常规指导。 如果可以对此问题采取措施，将提供一个包含更多指导的链接。 如果没有更多指导，响应将提供一个用于建立支持案例的 URL。  有关响应的属性及其包含的内容的详细信息，请访问[网络观察程序故障排除概述](network-watcher-troubleshoot-overview.md)

有关从 Azure 存储帐户下载文件的说明，请参阅[通过 .NET 开始使用 Azure Blob 存储](../storage/storage-dotnet-how-to-use-blobs.md)。 可以使用的另一个工具是存储资源管理器。 有关存储资源管理器的详细信息可以在此链接中找到：[存储资源管理器](http://storageexplorer.com/)

## <a name="next-steps"></a>后续步骤

如果停止 VPN 连接的设置已更改，请参阅[管理网络安全组](../virtual-network/virtual-network-manage-nsg-arm-portal.md)找到可能有问题的网络安全组和安全规则。

