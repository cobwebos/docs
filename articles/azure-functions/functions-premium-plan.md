---
title: Azure Functions 高级计划
description: Azure Functions 高级计划的详细信息和配置选项（VNet、无冷启动、无限制执行持续时间）。
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
ms.openlocfilehash: a037c903a72ba79b79c7e6b011fe025aefd7b51d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578030"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions 高级计划

Azure Functions 高级计划（有时称为“弹性高级计划”）是函数应用的一个承载选项。 高级计划提供 VNet 连接、无冷启动以及高级硬件等功能。  可以将多个函数应用部署到同一高级计划，该计划允许配置计算实例大小、基本计划大小和最大计划大小。  有关高级计划以及其他计划和承载类型的比较，请参阅[函数缩放和承载选项](functions-scale.md)。

## <a name="create-a-premium-plan"></a>创建高级计划

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

你还可以在 Azure CLI 中使用 [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) 创建高级计划。 下面的示例创建一个“弹性高级版 1”层计划：

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

在此示例中，请将 `<RESOURCE_GROUP>` 替换为资源组，并将 `<PLAN_NAME>` 替换为你的计划的名称，该名称在资源组中是独一无二的。 指定一个[受支持的 `<REGION>`](https://azure.microsoft.com/global-infrastructure/services/?products=functions)。 若要创建支持 Linux 的高级计划，请包括 `--is-linux` 选项。

创建计划后，可以使用 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 创建你的函数应用。 在门户中，计划和应用是同时创建的。 有关完整 Azure CLI 脚本的示例，请参阅[在高级计划中创建函数应用](scripts/functions-cli-create-premium-plan.md)。

## <a name="features"></a>功能

以下功能可供部署到高级计划的函数应用使用。

### <a name="always-ready-instances"></a>始终就绪实例

如果今天在消耗计划中没有发生任何事件，也没有任何执行操作，则你的应用可以横向缩减到零个实例。 当出现新事件时，需要使用在新实例上运行的应用将新实例专用化。  将新实例专用化可能需要一些时间，具体取决于应用。  第一次调用时的这种额外延迟通常称为应用冷启动。

在高级计划中，你可以让应用在指定数量的实例上始终处于就绪状态。  始终就绪实例的最大数目为 20。  当事件开始触发应用时，它们首先路由到始终就绪的实例。  当函数变为活动状态时，其他实例将作为缓冲区进行预热。  此缓冲区可防止缩放过程中所需的新实例的冷启动。  这些缓冲的实例称为[预热实例](#pre-warmed-instances)。  组合使用始终就绪实例和预热缓冲区，你的应用可以有效地消除冷启动。

> [!NOTE]
> 每个高级计划将至少有一个活动 (计费) 实例。

你可以通过以下方式在 Azure 门户中配置始终就绪实例的数目：选择你的**函数应用**，转到“平台功能”选项卡，然后选择“横向扩展”选项。 在函数应用编辑窗口中，始终就绪实例是该应用特有的。

![弹性缩放设置](./media/functions-premium-plan/scale-out.png)

你还可以使用 Azure CLI 为应用配置始终就绪实例。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites 
```

#### <a name="pre-warmed-instances"></a>预热实例

预热实例是在缩放和激活事件期间作为缓冲区预热的实例数。  预热实例将持续缓冲，直至达到最大横向扩展限制。  默认的预热实例数为 1，在大多数情况下应保留为 1。  如果应用长时间预热（如自定义容器映像），则可能需要增大此缓冲区。  只有在充分利用了所有活动实例后，预热实例才会变为活动状态。

请考虑此示例，了解始终就绪实例和预热实例如何协同工作。  高级函数应用已配置五个始终可用实例，并且默认为一个预准备好实例。  当应用处于空闲状态且未触发任何事件时，将在五个实例上预配并运行应用。  此时，你将不会向准备好实例收费，因为未使用 always ready 实例，也没有分配准备好实例。

第一次触发器进入后，五个 always ready 实例会变为活动状态，并分配准备好实例。  应用现在运行时有六个预配的实例：五个现在处于活动状态的始终就绪实例，以及第六个预热的处于非活动状态的缓冲区。  如果执行速率持续增大，则最终将利用五个活动实例。  当平台决定扩展到五个以上实例时，它将扩展到预热实例。  如果发生这种情况，则现在会有六个活动实例，系统会立即预配第七个实例并填充预热缓冲区。  此缩放和预热序列将继续，直至达到应用的最大实例计数。  不会预热或激活超出最大值的实例。

可以使用 Azure CLI 为应用修改预热实例数。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites 
```

#### <a name="maximum-instances-for-an-app"></a>应用的最大实例数

除了[计划最大实例计数](#plan-and-sku-settings)之外，还可以配置每个应用的最大值。  可以使用[应用规模限制](./functions-scale.md#limit-scale-out)配置应用最大值。

### <a name="private-network-connectivity"></a>专用网络连接

部署到高级计划的 Azure Functions 会利用[针对 Web 应用的新 VNet 集成](../app-service/web-sites-integrate-with-vnet.md)。  配置后，你的应用可以与 VNet 中的资源通信，或通过服务终结点获得保护。  应用上也提供 IP 限制，以限制传入流量。

在高级计划中向函数应用分配子网时，你需要有一个具有足够 IP 地址的子网，用于每个潜在的实例。 我们需要一个至少包含 100 个可用地址的 IP 块。

有关详细信息，请参阅 [将 function app 与 VNet 集成](functions-create-vnet.md)。

### <a name="rapid-elastic-scale"></a>快速弹性缩放

使用与消耗计划相同的快速缩放逻辑，为你的应用自动添加其他计算实例。 同一应用服务计划中的应用会基于单个应用的需求彼此独立地进行缩放。 但是，如果可能，同一应用服务计划中的 Functions 应用会共享 VM 资源，以便降低成本。 与某个 VM 关联的应用数取决于每个应用的资源占用情况和 VM 大小。

若要详细了解缩放的工作原理，请参阅[函数缩放和承载](./functions-scale.md#how-the-consumption-and-premium-plans-work)。

### <a name="longer-run-duration"></a>更长的运行持续时间

消耗计划中的 Azure Functions 限制为单次执行 10 分钟。  在高级计划中，运行持续时间默认为 30 分钟，以防止执行失控。 不过，你可以[修改 host.json 配置](./functions-host-json.md#functiontimeout)，使高级计划应用的持续时间无限长（保证 60 分钟）。

## <a name="plan-and-sku-settings"></a>计划和 SKU 设置

创建计划时，有两个计划大小设置：实例的最小数目（或计划大小）和最大突发限制。

如果应用需要的实例数超出了始终就绪实例数，则它可以继续横向扩展，直到实例数达到最大突发限制。  仅在每秒按计划大小运行并分配给你时，才会向你收费。  我们将尽最大努力将你的应用横向扩展到其定义的最大限制。

在 Azure 门户中，可以通过在计划中或在部署到该计划的函数应用中选择“横向扩展”选项（在“平台功能”下），配置计划大小和最大值。

你还可以通过 Azure CLI 提高最大突发限制：

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

每个计划的最小值至少为一个实例。  系统会根据计划中的应用请求的始终就绪实例自动为你配置实际的最小实例数。  例如，如果应用 A 请求 5 个始终就绪实例，应用 B 在同一计划中请求 2 个始终就绪实例，则最小计划大小将计算为 5。  应用 A 将在所有 5 个实例上运行，应用 B 将仅在 2 个实例上运行。

> [!IMPORTANT]
> 对于在最小实例计数中分配的每个实例，无论函数是否正在执行，系统都会向你收费。

在大多数情况下，此自动计算的最小值应该足以满足需要。  但是，系统会尽力进行超出最小值的缩放。  如果其他实例不可用，则在特定的时间，横向扩展可能会延迟，虽然这种情况不太可能发生。  通过将最小值设置为高于自动算出的最小值，可以在横向扩展之前预留实例。

可以使用 Azure CLI 为计划增大算出的最小值。

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set sku.capacity=<desired_min_instances> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>可用的实例 SKU

创建或缩放计划时，可以在三个实例大小之间进行选择。  将按每秒分配给你的核心和内存总量来计费。  你的应用可以根据需要自动横向扩展到多个实例。  

|SKU|核心数|内存|存储|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

### <a name="memory-utilization-considerations"></a>内存利用率注意事项
在具有更多内存的计算机上运行并不一定意味着函数应用将使用所有可用内存。

例如，JavaScript 函数应用受 Node.js 中的默认内存限制约束。 若要增大此固定内存限制，请添加值为 `--max-old-space-size=<max memory in MB>` 的应用设置 `languageWorkers:node:arguments`。

## <a name="region-max-scale-out"></a>区域最大 Scale Out

下面是每个区域和 OS 配置中单个计划的当前支持的最大缩放值。 若要请求增加，请打开支持票证。

请在此处查看 Functions 的完整的区域可用性：[Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|区域| Windows | Linux |
|--| -- | -- |
|澳大利亚中部| 100 | 不可用 |
|澳大利亚中部 2| 100 | 不可用 |
|澳大利亚东部| 100 | 20 |
|Australia Southeast | 100 | 20 |
|巴西南部| 100 | 20 |
|加拿大中部| 100 | 20 |
|美国中部| 100 | 20 |
|中国东部 2| 100 | 20 |
|中国北部 2| 100 | 20 |
|东亚| 100 | 20 |
|美国东部 | 100 | 20 |
|美国东部 2| 100 | 20 |
|法国中部| 100 | 20 |
|德国中西部| 100 | 不可用 |
|Japan East| 100 | 20 |
|日本西部| 100 | 20 |
|韩国中部| 100 | 20 |
|韩国南部| 不可用 | 20 |
|美国中北部| 100 | 20 |
|北欧| 100 | 20 |
|挪威东部| 100 | 20 |
|美国中南部| 100 | 20 |
|印度南部 | 100 | 不可用 |
|Southeast Asia| 100 | 20 |
|瑞士北部| 100 | 不可用 |
|瑞士西部| 100 | 不可用 |
|英国南部| 100 | 20 |
|英国西部| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Virginia| 100 | 20 |
|USNat 东部| 100 | 不可用 |
|USNat 西| 100 | 不可用 |
|西欧| 100 | 20 |
|印度西部| 100 | 20 |
|美国中西部| 100 | 20 |
|美国西部| 100 | 20 |
|美国西部 2| 100 | 20 |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure Functions 的缩放和承载选项](functions-scale.md)
