---
title: Azure Functions 高级计划
description: Azure Functions 高级计划的详细信息和配置选项（VNet，无冷启动，无限制执行持续时间）。
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: 9c1a9a9e3b9e1c12c3960a8586c25436c8d937e0
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/26/2019
ms.locfileid: "74532900"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions 高级计划

Azure Functions 高级计划是函数应用的托管选项。 高级计划提供 VNet 连接、无冷启动和高级硬件等功能。  可以将多个函数应用部署到相同的高级计划，该计划允许配置计算实例大小、基本计划大小和最大计划大小。  有关高级计划以及其他计划和托管类型的比较，请参阅[函数缩放和宿主选项](functions-scale.md)。

## <a name="create-a-premium-plan"></a>创建高级计划

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

你还可以在 Azure CLI 中使用[az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create)创建高级计划。 以下示例创建一个_弹性高级版 1_层计划：

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

在此示例中，将 `<RESOURCE_GROUP>` 替换为资源组，并将 `<PLAN_NAME>` 替换为在资源组中唯一的计划的名称。 指定[支持的 `<REGION>`](#regions)。 若要创建支持 Linux 的高级计划，请包含 `--is-linux` 选项。

创建计划后，可以使用[az functionapp create](/cli/azure/functionapp#az-functionapp-create)创建 function app。 在门户中，计划和应用都同时创建。 有关完整 Azure CLI 脚本的示例，请参阅[在高级计划中创建函数应用](scripts/functions-cli-create-premium-plan.md)。

## <a name="features"></a>功能

以下功能可用于部署到高级计划的函数应用。

### <a name="pre-warmed-instances"></a>准备好实例

如果在消耗计划中今天没有发生事件和执行，则应用可能会缩小到零实例。 当出现新事件时，需要使用在其上运行的应用程序来专用化新的实例。  专用化新实例可能需要一些时间，具体取决于应用程序。  第一次调用的额外延迟通常称为应用冷启动。

在高级计划中，你可以将应用预先准备好指定数量的实例，最小计划大小。  使用准备好实例，还可以在高负载之前预缩放应用。 在应用程序扩展时，它首先扩展到准备好实例。 其他实例将继续为下一个缩放操作做好缓冲并使其立即预热。 通过使用准备好实例的缓冲区，可以有效地避免冷启动延迟。  准备好实例是高级计划的一项功能，需要在计划处于活动状态时至少保持一个实例处于运行状态且可用。

您可以通过选择**Function App**，转到 "**平台功能**" 选项卡，然后选择 " **Scale Out** " 选项，在 Azure 门户中配置准备好实例的数目。 在函数应用编辑窗口中，准备好实例特定于该应用，但最小和最大实例适用于你的整个计划。

![弹性缩放设置](./media/functions-premium-plan/scale-out.png)

你还可以使用 Azure CLI 配置应用的预准备好实例

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>专用网络连接

部署到高级计划的 Azure Functions 将利用[适用于 web 应用的新 VNet 集成](../app-service/web-sites-integrate-with-vnet.md)。  配置后，你的应用可以与 VNet 中的资源通信，或通过服务终结点进行保护。  应用上也提供了 IP 限制，以限制传入流量。

在高级计划中向函数应用分配子网时，需要一个子网，其中每个潜在实例都有足够的 IP 地址。 需要至少具有100个可用地址的 IP 块。

有关详细信息，请参阅[将 function app 与 VNet 集成](functions-create-vnet.md)。

### <a name="rapid-elastic-scale"></a>快速弹性缩放

使用与消耗计划相同的快速缩放逻辑，为你的应用自动添加其他计算实例。  若要详细了解缩放的工作方式，请参阅[函数缩放和宿主](./functions-scale.md#how-the-consumption-and-premium-plans-work)。

### <a name="longer-run-duration"></a>运行持续时间较长

对于单个执行，消耗计划中的 Azure Functions 限制为10分钟。  在高级计划中，运行持续时间默认为30分钟，以防止执行失控。 不过，你可以[修改 host json 配置](./functions-host-json.md#functiontimeout)，以使高级计划应用60分钟。

## <a name="plan-and-sku-settings"></a>计划和 SKU 设置

创建计划时，需要配置两个设置：实例的最小数目（或计划大小）和最大猝发限制。  最小实例保留并始终运行。

> [!IMPORTANT]
> 对于在最小实例计数中分配的每个实例，无论函数是否正在执行，都将向你收费。

如果你的应用程序需要的实例超出了你的计划大小，则它可以继续横向扩展，直到实例数达到最大猝发限制。  仅当计划规模正在运行且为你租借时，才会向你收费。  我们将尽最大努力将应用扩展到其定义的最大限制，而最小计划实例可确保适用于你的应用。

您可以通过选择计划中的**Scale Out**选项或部署到该计划的函数应用（在 "**平台功能**" 下），在 Azure 门户中配置计划大小和最大值。

还可以增加 Azure CLI 的最大猝发限制：

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>可用实例 Sku

创建或缩放计划时，可以在三种实例大小之间进行选择。  将按照内核总数和每秒使用的内存数计费。  应用可根据需要自动向外扩展到多个实例。  

|SKU|核心数|内存|存储|
|--|--|--|--|
|EP1|1|3.5 GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14 GB|250GB|

## <a name="regions"></a>区域

下面是每个操作系统当前支持的区域。

|区域| Windows | Linux |
|--| -- | -- |
|澳大利亚中部| ✔<sup>1</sup> | |
|澳大利亚中部 2| ✔<sup>1</sup> | |
|澳大利亚东部| ✔ | |
|澳大利亚东南部 | ✔ | ✔ |
|巴西南部| ✔<sup>2</sup> |  |
|加拿大中部| ✔ |  |
|美国中部| ✔ |  |
|东亚| ✔ |  |
|美国东部 | ✔ | ✔ |
|美国东部 2| ✔ |  |
|法国中部| ✔ |  |
|日本东部| ✔ | ✔ |
|日本西部| ✔ | |
|韩国中部| ✔ |  |
|美国中北部| ✔ |  |
|北欧| ✔ | ✔ |
|美国中南部| ✔ |  |
|印度南部 | ✔ | |
|东南亚| ✔ | ✔ |
|英国南部| ✔ | |
|英国西部| ✔ |  |
|西欧| ✔ | ✔ |
|印度西部| ✔ |  |
|美国西部| ✔ | ✔ |
|美国西部 2| ✔ |  |

<sup>1</sup>最大扩大范围限制为20个实例。  
<sup>2</sup>最大扩大规模限制为60实例。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure Functions 缩放和托管选项](functions-scale.md)
