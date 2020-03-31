---
title: Azure 函数高级计划
description: Azure 函数高级计划的详细信息和配置选项（VNet、无冷启动、无限制执行持续时间）。
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: dd7f6d0760f2b848435e7c77657e261517d29dd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79276902"
---
# <a name="azure-functions-premium-plan"></a>Azure 函数高级计划

Azure 函数高级计划（有时称为弹性高级计划）是函数应用的托管选项。 高级计划提供 VNet 连接、无冷启动和高级硬件等功能。  可以将多个功能应用部署到同一高级计划，并且该计划允许您配置计算实例大小、基本计划大小和最大计划大小。  有关高级计划和其他计划和托管类型的比较，请参阅[功能规模和托管选项](functions-scale.md)。

## <a name="create-a-premium-plan"></a>创建高级计划

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

您还可以使用在 Azure CLI 中[创建的 az 函数应用计划](/cli/azure/functionapp/plan#az-functionapp-plan-create)创建高级计划。 以下示例创建_弹性高级高级 1_层计划：

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

在此示例中，替换为资源组`<RESOURCE_GROUP>`，并`<PLAN_NAME>`替换为资源组中唯一的计划的名称。 指定[受支持的`<REGION>`](https://azure.microsoft.com/global-infrastructure/services/?products=functions)。 要创建支持 Linux 的高级计划，请包括`--is-linux`该选项。

创建计划后，可以使用[创建 az 函数应用](/cli/azure/functionapp#az-functionapp-create)来创建函数应用。 在门户中，将同时创建计划和应用。 有关完整 Azure CLI 脚本的示例，请参阅[在高级计划中创建函数应用](scripts/functions-cli-create-premium-plan.md)。

## <a name="features"></a>功能

以下功能可用于部署到高级计划的应用。

### <a name="pre-warmed-instances"></a>预热实例

如果"消耗"计划中今天未发生事件和执行，则应用可能会扩展到零实例。 当新事件进入时，需要专门使用运行其应用的新实例。  根据应用的不同，专业化新实例可能需要一些时间。  第一次调用时的额外延迟通常称为应用冷启动。

在高级计划中，您可以让应用在指定数量的实例上预热，最多达到最低计划大小。  预热的实例还允许您在高负载之前预缩放应用。 当应用缩小时，它首先扩展到预热的实例。 其他实例继续缓冲并立即加热，为下一次规模操作做准备。 通过具有预预热实例的缓冲区，您可以有效地避免冷启动延迟。  预热实例是高级计划的一项功能，您需要保持至少一个实例的运行状态，并且在计划处于活动状态时随时可用。

您可以通过选择**功能应用**（进入 **"平台功能"** 选项卡以及选择 **"横向扩展"** 选项）来配置 Azure 门户中预热实例的数量。 在函数应用编辑窗口中，预热的实例特定于该应用，但最小和最大实例适用于整个计划。

![弹性比例设置](./media/functions-premium-plan/scale-out.png)

还可以使用 Azure CLI 为应用配置预热的实例。

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>专用网络连接

部署到高级计划的 Azure 函数利用了[Web 应用的新 VNet 集成](../app-service/web-sites-integrate-with-vnet.md)。  配置后，你的应用可以与 VNet 中的资源通信或通过服务终结点进行保护。  应用程序上也提供 IP 限制，以限制传入流量。

在高级计划中将子网分配给函数应用时，需要每个潜在实例具有足够的 IP 地址的子网。 我们需要一个至少具有 100 个可用地址的 IP 块。

有关详细信息，请参阅[将函数应用与 VNet 集成](functions-create-vnet.md)。

### <a name="rapid-elastic-scale"></a>快速弹性刻度

使用与消耗计划相同的快速缩放逻辑，为您的应用自动添加其他计算实例。  要了解有关缩放的工作原理，请参阅[函数缩放和托管](./functions-scale.md#how-the-consumption-and-premium-plans-work)。

### <a name="longer-run-duration"></a>更长的运行持续时间

使用计划中的 Azure 函数在单个执行时限制为 10 分钟。  在高级计划中，运行持续时间默认为 30 分钟，以防止失控执行。 但是，您可以[修改 host.json 配置](./functions-host-json.md#functiontimeout)，使其对高级计划应用无限制（保证 60 分钟）。

## <a name="plan-and-sku-settings"></a>计划和 SKU 设置

创建计划时，配置两个设置：最小实例数（或计划大小）和最大突发限制。  保留最小实例并始终运行。

> [!IMPORTANT]
> 无论函数是否正在执行，都会为在最小实例计数中分配的每个实例收费。

如果应用需要超出计划大小的实例，则可以继续横向扩展，直到实例数达到最大突发限制。  只有在超出计划大小的实例运行并租给您时，才会向您收费。  我们将尽最大努力将你的应用扩展到其定义的最大限制，同时保证你的应用的最小计划实例。

您可以通过在计划中选择 **"横向扩展"** 选项或部署到该计划的函数应用（在**平台功能**下）来配置 Azure 门户中的计划大小和最大值。

您还可以从 Azure CLI 增加最大突发限制：

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>可用实例 SKU

创建或缩放计划时，可以在三个实例大小之间进行选择。  您将按每秒消耗的内核和内存总数计费。  您的应用可以根据需要自动扩展到多个实例。  

|SKU|核心数|内存|存储|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

### <a name="memory-utilization-considerations"></a>内存利用率注意事项
在内存较多的计算机上运行并不总是意味着函数应用将使用所有可用内存。

例如，JavaScript 函数应用受 Node.js 中的默认内存限制的约束。 要增加此固定内存限制，添加值 为`languageWorkers:node:arguments`的应用`--max-old-space-size=<max memory in MB>`设置。

## <a name="region-max-scale-out"></a>区域最大横向扩展

以下是当前支持的每个区域和操作系统配置中单个计划的最大横向扩展值。 要要求增加，请打开一张支持票。

在此处查看功能的完整区域可用性[：Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|区域| Windows | Linux |
|--| -- | -- |
|澳大利亚中部| 20 | 不可用 |
|澳大利亚中部 2| 20 | 不可用 |
|澳大利亚东部| 100 | 20 |
|澳大利亚东南部 | 100 | 20 |
|巴西南部| 60 | 20 |
|加拿大中部| 100 | 20 |
|美国中部| 100 | 20 |
|东亚| 100 | 20 |
|美国东部 | 100 | 20 |
|美国东部 2| 100 | 20 |
|法国中部| 100 | 20 |
|德国中西部| 100 | 不可用 |
|日本东部| 100 | 20 |
|日本西部| 100 | 20 |
|韩国中部| 100 | 20 |
|美国中北部| 100 | 20 |
|北欧| 100 | 20 |
|挪威东部| 20 | 20 |
|美国中南部| 100 | 20 |
|印度南部 | 100 | 不可用 |
|东南亚| 100 | 20 |
|英国南部| 100 | 20 |
|英国西部| 100 | 20 |
|西欧| 100 | 20 |
|印度西部| 100 | 20 |
|美国中西部| 20 | 20 |
|美国西部| 100 | 20 |
|美国西部 2| 100 | 20 |

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解 Azure 函数缩放和托管选项](functions-scale.md)
