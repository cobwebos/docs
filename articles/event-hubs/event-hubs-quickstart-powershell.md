---
title: Azure 快速入门 - 使用 PowerShell 处理事件流 | Microsoft Docs
description: 本快速入门介绍如何使用 PowerShell 与示例 .NET 应用程序来发送和接收 Azure 事件中心事件。
services: event-hubs
author: sethmanheim
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/26/2018
ms.author: sethm
ms.openlocfilehash: 9216372038db7a6f97cfc8034f715b34de08d83c
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132116"
---
# <a name="quickstart-process-event-streams-using-powershell-and-net-standard"></a>快速入门：使用 PowerShell 和 .NET Standard 处理事件流

Azure 事件中心是高度可缩放的数据流平台和引入服务，每秒能够接收和处理数百万个事件。 本快速入门介绍如何使用 Azure PowerShell 创建事件中心，然后使用 .NET Standard SDK 向事件中心发送事件和从中接收事件。

若要完成本快速入门，需要一个 Azure 订阅。 如果没有订阅，请在开始之前[创建一个免费帐户][]。

## <a name="prerequisites"></a>先决条件

若要完成本教程，请确保做好以下准备：

- [Visual Studio 2017 Update 3（版本 15.3 (26730.01)）](http://www.visualstudio.com/vs)或更高版本。
- [.NET Standard SDK](https://www.microsoft.com/net/download/windows) 2.0 或更高版本。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

如果在本地使用 PowerShell，必须运行最新版本的 PowerShell 才能完成本快速入门。 如需进行安装或升级，请参阅[安装和配置 Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0)。

## <a name="provision-resources"></a>预配资源

### <a name="create-a-resource-group"></a>创建资源组

资源组是 Azure 资源的逻辑集合，需有一个资源组才能创建事件中心。 

以下示例在美国东部区域创建一个资源组： 将 `myResourceGroup` 替换为要使用的资源组的名称：

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>创建事件中心命名空间

创建资源组后，在该资源组中创建事件中心命名空间。 事件中心命名空间提供唯一的完全限定域名，可在其中创建事件中心。 将 `namespace_name` 替换为命名空间的唯一名称：

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>创建事件中心

创建事件中心命名空间后，在该命名空间中创建事件中心：

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>为事件处理程序主机创建存储帐户

事件处理程序主机通过管理检查点和并行接收器来简化从事件中心接收事件的过程。 对于检查点，事件处理程序主机需要一个存储帐户。 若要创建存储帐户并获取其密钥，请运行以下命令：

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>获取连接字符串

需要使用连接字符串连接到事件中心和处理事件。 若要获取连接字符串，请运行：

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>流式传输到事件中心

现在可以开始流式传输到事件中心。 可以下载示例，或者从[事件中心存储库](https://github.com/Azure/azure-event-hubs)克隆 Git

### <a name="ingest-events"></a>引入事件

若要开始流式传输事件，请从 GitHub 下载 [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender)，或通过发出以下命令克隆[事件中心 GitHub 存储库](https://github.com/Azure/azure-event-hubs)：

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

导航到 \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender 文件夹，并将 SampleSender.sln 文件加载到 Visual Studio。

接下来，将 [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) Nuget 包添加到项目。

在 Program.cs 文件中，将以下占位符替换为事件中心名称和连接字符串：

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

现在，请生成并运行示例。 可以看到，事件正在引入到事件中心：

![][3]

### <a name="receive-and-process-events"></a>接收和处理事件

现在，下载事件处理程序主机接收器示例，用于接收刚刚发送的消息。 从 GitHub 下载 [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver)，或通过发出以下命令克隆[事件中心 GitHub 存储库](https://github.com/Azure/azure-event-hubs)：

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

导航到 \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver 文件夹，并将 SampleEphReceiver.sln 解决方案文件加载到 Visual Studio。

接下来，将 [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) 和 [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) Nuget 包添加到项目。

在 Program.cs 文件中，将以下常量替换为相应的值：

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

现在，请生成并运行示例。 可以看到，示例应用程序中正在接收事件：

![][4]

在 Azure 门户中，可以查看针对给定的事件中心命名空间处理事件的速率，如下所示：

![][5]

## <a name="clean-up-resources"></a>清理资源

完成本快速入门后，可以删除资源组及其包含的命名空间、存储帐户和事件中心。 将 `myResourceGroup` 替换为创建的资源组的名称。 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>后续步骤

在本文中，我们已创建用于从事件中心发送和接收事件的事件中心命名空间和其他资源。 有关详细信息，请继续阅读以下教程：

> [!div class="nextstepaction"]
> [将事件中心数据流中的数据异常可视化](event-hubs-tutorial-visualize-anomalies.md)

[创建一个免费帐户]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
