---
title: 使用 PowerShell 创建事件中心 - Azure 事件中心 | Microsoft Docs
description: 本快速入门介绍如何使用 Azure PowerShell 创建事件中心，然后使用 .NET Standard SDK 发送和接收事件。
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 786d6fe04ced719217f57a2d603dff810b7a4c20
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2019
ms.locfileid: "54430749"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>快速入门：使用 Azure PowerShell 创建事件中心

Azure 事件中心是一个大数据流式处理平台和事件引入服务，每秒能够接收和处理数百万个事件。 事件中心可以处理和存储分布式软件和设备生成的事件、数据或遥测。 可以使用任何实时分析提供程序或批处理/存储适配器转换和存储发送到数据中心的数据。 有关事件中心的详细概述，请参阅[事件中心概述](event-hubs-about.md)和[事件中心功能](event-hubs-features.md)。

在本快速入门中，请使用 Azure PowerShell 创建事件中心。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保做好以下准备：

- Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户][]。
- [Visual Studio 2017 Update 3（版本 15.3 (26730.01)）](https://www.visualstudio.com/vs)或更高版本。
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 2.0 或更高版本。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果在本地使用 PowerShell，必须运行最新版本的 PowerShell 才能完成本快速入门。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-5.7.0)。

## <a name="create-a-resource-group"></a>创建资源组

资源组是 Azure 资源的逻辑集合，需有一个资源组才能创建事件中心。 

以下示例在美国东部区域创建一个资源组： 将 `myResourceGroup` 替换为要使用的资源组的名称：

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间

创建资源组后，在该资源组中创建事件中心命名空间。 事件中心命名空间提供唯一的完全限定域名，可在其中创建事件中心。 将 `namespace_name` 替换为命名空间的唯一名称：

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>创建事件中心

创建事件中心命名空间后，在该命名空间中创建事件中心：  
`MessageRetentionInDays` 的允许期限为 1 到 7 天。

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

祝贺你！ 现已使用 Azure PowerShell 创建了一个事件中心命名空间，并在该命名空间中创建了一个事件中心。 

## <a name="next-steps"></a>后续步骤

在本文中，我们已创建事件中心命名空间，并使用示例应用程序从事件中心发送和接收事件。 有关如何将事件发送到事件中心（或）从事件中心接收事件的分步说明，请参阅以下教程： 

- **将事件发送到事件中心**：[.NET Core](event-hubs-dotnet-standard-getstarted-send.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)、[Java](event-hubs-java-get-started-send.md)、[Python](event-hubs-python-get-started-send.md)、[Node.js](event-hubs-node-get-started-send.md)、[Go](event-hubs-go-get-started-send.md)、[C](event-hubs-c-getstarted-send.md)
- **接收来自事件中心的事件**：[.NET Core](event-hubs-dotnet-standard-getstarted-receive-eph.md)、[.NET Framework](event-hubs-dotnet-framework-getstarted-receive-eph.md)、[Java](event-hubs-java-get-started-receive-eph.md)、[Python](event-hubs-python-get-started-receive.md)、[Node.js](event-hubs-node-get-started-receive.md)、[Go](event-hubs-go-get-started-receive-eph.md)、[Apache Storm](event-hubs-storm-getstarted-receive.md)

[创建一个免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
