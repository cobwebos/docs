---
title: Azure Functions 的高级计划 （预览版） |Microsoft Docs
description: 计划的详细信息和 Azure Functions 高级的配置选项 (VNet，没有冷启动、 不受限制的执行持续时间)。
services: functions
author: jeffhollan
manager: jeconnoc
ms.assetid: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 4/11/2019
ms.author: jehollan
ms.openlocfilehash: dab7561db8f223bff87f41ef756605359c3478e4
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66492696"
---
# <a name="azure-functions-premium-plan-preview"></a>Azure Functions 的高级计划 （预览版）

Azure Functions 高级版计划是函数应用的托管选项。 高级计划提供了 VNet 连接性、 不冷启动和高级硬件等功能。  可以将多个函数应用部署到相同的高级计划，并计划让你能够配置计算实例大小、 基本计划大小和最大计划大小。  有关高级版计划和其他计划和托管类型的比较，请参阅[函数的缩放和托管选项](functions-scale.md)。

> [!NOTE]
> 高级计划预览版目前支持通过 Windows 基础结构在.NET、 节点或 Java 中运行的函数。

## <a name="create-a-premium-plan"></a>创建高级版计划

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

此外可以从 Azure CLI 创建高级版计划

```azurecli-interactive
az functionapp plan create -g <resource-group> -n <plan-name> -l <region> --number-of-workers 1 --sku EP1
```

## <a name="features"></a>功能

以下功能可供部署到高级版计划的函数应用。

### <a name="pre-warmed-instances"></a>预上做好准备的实例

如果没有事件和执行立即出现在消耗计划中，您的应用程序可能会减少到零个实例。 当新事件时，需要使用你在其上运行的应用进行专用化新实例。  专用的新实例可能需要一些时间，具体取决于应用程序。  在首次调用此额外延迟通常称为冷启动应用程序。

在高级版计划中，您可以在指定数量的实例，直到达到最小的计划大小上预先上做好准备您的应用程序。  预上做好准备的实例还允许您预先的高负载之前的某个应用扩展。 当应用横向扩展，它会预 warmed 实例第一次扩展。 其他实例可继续缓冲区和热立即以准备进行下一步的缩放操作。 通过让预上做好准备的实例的缓冲区，可以有效地避免冷启动延迟。  预上做好准备的实例是一项功能的高级计划，并需要保留至少一个实例在运行并可在所有时间的计划处于活动状态。

可以在 Azure 中配置预上做好准备的实例数门户通过选择您**Function App**、 到接下来**平台功能**选项卡，并选择**Scale Out**选项。 在函数应用的编辑窗口中，预上做好准备的实例是特定于该应用，但最小值和最大实例应用于整个计划。

![弹性缩放设置](./media/functions-premium-plan/scale-out.png)

此外可以使用 Azure CLI 配置为应用预上做好准备的实例

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>专用网络连接

Azure 函数部署到高级版计划利用[新的 web 应用的 VNet 集成](../app-service/web-sites-integrate-with-vnet.md)。  配置时，您的应用程序可以与在 VNet 中的资源通信，或通过服务终结点保护。  IP 限制，还提供应用程序来限制传入流量。

在高级版计划中，将子网分配到 function app 中，您将需要具有足够的 IP 地址的子网为每个潜在的实例。 在预览期间，最大实例数可能会有所不同，但我们需要具有至少 100 个可用地址的 IP 块。

有关详细信息，请参阅[将函数应用与 VNet 集成](functions-create-vnet.md)。

### <a name="rapid-elastic-scale"></a>快速弹性缩放

与消耗计划中使用相同的快速缩放逻辑对应用自动添加额外的计算实例。  若要了解有关如何缩放的工作原理的详细信息，请参阅[函数的缩放和托管](./functions-scale.md#how-the-consumption-and-premium-plans-work)。

### <a name="unbounded-run-duration"></a>无限运行持续时间

在消耗计划中的 azure Functions 是限制为 10 分钟一次执行。  在高级版计划中，将运行持续时间默认为 30 分钟，以防止失控的执行。 但是，你可以[修改 host.json 配置](./functions-host-json.md#functiontimeout)来实现此高级计划应用不受限制。

在预览版中，您的持续时间过去的 12 分钟不能保证，并且会有超过 30 分钟后运行，如果您的应用程序不扩展超出其最小工作线程计数的最大限度地。

## <a name="plan-and-sku-settings"></a>计划和 SKU 设置

在创建计划时，配置两个设置： 实例 （或计划大小） 的最小数目和最大突发限制。  高级计划的最小实例为 1，并在预览期间的最大突发为 20。  最小实例是保留的始终运行。

> [!IMPORTANT]
> 收费为每个实例分配的最小实例计数而不考虑如果或不执行函数。

如果您的应用程序需要超出计划大小的实例，它可以继续向外扩展，直到达到最大突发限制时的实例数。  向你收费的超出计划大小的实例而它们是正在运行和租赁给你。  而保证您的应用程序的最小的计划实例，我们将在缩放应用到其定义的最大限制，最大努力。

你可以配置的计划大小和最大值通过在 Azure 门户中的选择**Scale Out**中的计划或 function app 部署到该计划的选项 (下**平台功能**)。

您还可以增加的最大突发限制从 Azure CLI:

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>可用实例 Sku

当创建或缩放你的计划时，你可以选择三种实例大小。  您将收取的内核数和每秒使用的内存总数。  您的应用程序可以自动横向扩展到多个实例根据需要。  

|SKU|核心数|内存|存储|
|--|--|--|--|
|EP1|第|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

## <a name="regions"></a>区域

下面是公共预览版目前支持的区域。

|区域|
|--|
|澳大利亚东部|
|澳大利亚东南部|
|加拿大中部|
|美国中部|
|东亚|
|美国东部 2|
|法国中部|
|日本西部|
|韩国中部|
|美国中北部|
|北欧|
|美国中南部|
|印度南部|
|东南亚|
|英国西部|
|西欧|
|印度西部|
|美国西部|

## <a name="known-issues"></a>已知问题

你可以跟踪的已知问题的状态[GitHub 上的公共预览版](https://github.com/Azure/Azure-Functions/wiki/Premium-plan-known-issues)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure Functions 的缩放和托管选项](functions-scale.md)
