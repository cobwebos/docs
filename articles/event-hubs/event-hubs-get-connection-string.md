---
title: 获取 Azure 事件中心连接字符串 | Microsoft Docs
description: 获取 Azure 事件中心连接字符串
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.date: 10/15/2018
ms.author: shvija
ms.openlocfilehash: bfc82f2dc280c3528f38c9cb466473a76328e552
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285379"
---
# <a name="get-an-event-hubs-connection-string"></a>获取事件中心连接字符串

若要使用事件中心，需要创建一个事件中心命名空间。 命名空间是可以容装多个事件中心/Kafka 主题的范围容器。 此命名空间提供唯一的 [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)。 创建命名空间后，可以获取与事件中心通信所需的连接字符串。

Azure 事件中心的连接字符串中嵌入了以下组成部分：

* FQDN = 创建的事件中心命名空间的 FQDN（包括事件中心命名空间的名称，后接 servicebus.windows.net）
* SharedAccessKeyName = 为应用程序的 SAS 密钥选择的名称
* SharedAccessKey = 生成的密钥值。

连接字符串模板如下所示
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

`Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=` 是连接字符串的示例

本文逐步讲解获取连接字符串的各种方法。

## <a name="get-connection-string-from-the-portal"></a>从门户中获取连接字符串

创建事件中心命名空间后，门户的概述部分会提供连接字符串，如下所示：

![事件中心连接字符串](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)

在概述部分单击连接字符串链接时，会打开下图所示的 SAS 策略选项卡：

![事件中心 SAS 策略](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)

可以添加新的 SAS 策略并获取连接字符串，或使用系统为你创建的默认策略。 打开策略后，即可获取连接字符串，如下图所示：

![获取事件中心连接字符串](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>使用 Azure PowerShell 获取连接字符串
可以使用 Get-AzureRmEventHubNamespaceKey 获取指定策略/规则名称的连接字符串，如下所示：

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

有关更多详细信息，请参阅 [Azure 事件中心 PowerShell 模块](https://docs.microsoft.com/powershell/module/azurerm.eventhub/get-azurermeventhubkey)。

## <a name="getting-the-connection-string-with-azure-cli"></a>使用 Azure CLI 获取连接字符串
可使用以下命令获取命名空间的连接字符串：

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

有关详细信息，请参阅[适用于事件中心的 Azure CLI](https://docs.microsoft.com/cli/azure/eventhubs)。

## <a name="next-steps"></a>后续步骤

访问以下链接可以了解有关事件中心的详细信息：

* [事件中心概述](event-hubs-what-is-event-hubs.md)
* [创建事件中心](event-hubs-create.md)
