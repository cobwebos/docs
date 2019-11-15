---
title: 快速入门：使用 PowerShell 创建事件中心 - Azure 事件中心
description: 本快速入门介绍如何使用 Azure PowerShell 创建事件中心，然后使用 .NET Standard SDK 发送和接收事件。
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 2a88fe340037c31a71cb34181682095f31400c36
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720607"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建事件中心

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

在本快速入门中，请使用 Azure PowerShell 创建事件中心。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

若要完成本教程，请确保做好以下准备：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户][]。
- [Visual Studio 2019](https://www.visualstudio.com/vs)。
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 2.0 或更高版本。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果在本地使用 PowerShell，必须运行最新版本的 PowerShell 才能完成本快速入门。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是 Azure 资源的逻辑集合，需有一个资源组才能创建事件中心。 

以下示例在美国东部区域创建一个资源组： 将 `myResourceGroup` 替换为要使用的资源组的名称：

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间

创建资源组后，在该资源组中创建事件中心命名空间。 事件中心命名空间提供唯一的完全限定域名，可在其中创建事件中心。 将 `namespace_name` 替换为命名空间的唯一名称：

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>创建事件中心

创建事件中心命名空间后，在该命名空间中创建事件中心：  
`MessageRetentionInDays` 的允许期限为 1 到 7 天。

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

祝贺你！ 现已使用 Azure PowerShell 创建了一个事件中心命名空间，并在该命名空间中创建了一个事件中心。 

## <a name="next-steps"></a>后续步骤

在本文中，我们已创建事件中心命名空间，并使用示例应用程序从事件中心发送和接收事件。 有关如何将事件发送到事件中心（或）从事件中心接收事件的分步说明，请参阅**发送和接收事件**教程： 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C（仅发送）](event-hubs-c-getstarted-send.md)
- [Apache Storm（仅接收）](event-hubs-storm-getstarted-receive.md)


[创建一个免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-az-ps
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-Azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
