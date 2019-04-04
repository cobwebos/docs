---
title: Azure Functions 的缩放和托管 | Microsoft Docs
description: 了解如何在 Azure Functions 消耗计划和高级计划之间进行选择。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure 函数、 函数、 消耗计划、 高级版计划，事件处理，webhook，动态计算，无服务器体系结构
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f09fded38e384126a8dfdbe567ce4a3ebd5b1af4
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893582"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions 的缩放和托管

Azure Functions 在两个不同的计划中运行：消耗量计划和高级计划 （公共预览版）。 运行你的代码时，消耗计划会自动添加的计算能力。 您的应用程序是以处理负载，根据需要向外扩展和缩减代码停止运行时。 无需为空闲的 VM 付费或提前保留容量。  高级计划还会自动将缩放和运行你的代码时将添加更多计算能力。  高级计划提供了其他功能，例如高级计算实例、 使实例保存无限期，热和 VNet 连接。  如果你有现有的应用服务计划，还可以运行函数应用中。

> [!NOTE]  
> 这两[高级版计划](https://azure.microsoft.com/blog/uncompromised-serverless-scale-for-enterprise-workloads-with-the-azure-functions-premium-plan/preview/)并[适用于 Linux 的消耗计划](https://azure.microsoft.com/updates/azure-functions-consumption-plan-for-linux-preview/)目前处于预览状态。

如果不熟悉 Azure Functions，请参阅 [Azure Functions 概述](functions-overview.md)。

创建函数应用时，要为应用中的函数选择托管计划。 Azure Functions 主机的实例在任一计划中均可执行函数。 计划类型可控制：

* 主机实例的扩展方式。
* 可供每个主机使用的资源。
* 实例功能，如 VNet 连接。

> [!NOTE]
> 您可以通过更改计划属性的函数应用资源消耗和高级计划之间进行切换。

## <a name="consumption-plan"></a>消耗量计划

当您使用消耗计划时，Azure Functions 主机的实例动态添加和删除根据传入事件数。 这个无服务器计划会自动缩放，仅在函数运行时，才会产生计算资源费用。 在消费计划中，函数执行在可配置的时间段后超时。

账单将基于执行数量、执行时间和所用内存。 账单是基于函数应用内的所有函数聚合而生成的。 有关详细信息，请参阅 [Azure Functions 定价页]。

消耗计划是默认的宿主计划，它提供了以下优势：

* 仅当函数运行时才产生费用。
* 即使是在负载较高期间也可自动扩展。

## <a name="premium-plan-public-preview"></a>高级计划 （公共预览版）

当您使用的高级计划时，Azure Functions 主机的实例快速添加和删除基于消耗计划一样的传入事件的数目。  但是，高级计划还提供：

* 始终暖实例，以避免任何冷启动。
* VNet 连接。
* 不受限制的执行持续时间。
* 高级实例大小 （一项核心、 两个核心和 4 个核心实例）。
* 可预测定价选项。
* 高密度应用于包含多个函数应用计划的分配。

如何配置这些选项的信息可在[Azure Functions 高级计划](functions-premium-plan.md)。

而不是每个执行和内存消耗的计费，计费基于核心秒和每 GB 秒数计需和保留实例使用的数量。  需要为热根本时间，因此有固定每月每个处于活动状态 （而不考虑执行数） 的计划成本至少一个实例。

Azure Functions 高级计划在以下情况下，请考虑：
* 函数应用持续或几乎持续运行。 在这种情况下，应用服务计划可能更经济高效。
* 所需 CPU 或内存选项超出消耗计划提供的选项。
* 你的代码需要运行时间超过[允许的最长执行时间](#timeout)基于消耗计划。
* 需要仅对应用服务计划可用的功能，例如应用服务环境支持、VNET/VPN 连接性和更大的 VM。

> [!NOTE]
> 高级计划预览版目前支持通过 Windows 基础结构在.NET、 节点或 Java 中运行的函数。

当在高级版计划上运行 JavaScript 函数，应选择具有较少 Vcpu 的实例。 有关详细信息，请参阅[选择单核心高级计划](functions-reference-node.md#considerations-for-javascript-functions)。  

## <a name="app-service-plan"></a>应用服务计划

函数应用还可以作为其他应用服务应用 （基本、 标准、 高级版和独立 Sku） 相同的专用 Vm 上运行。 应用服务计划支持 Linux。

对于以下情况，可以考虑使用应用服务计划：

* 具有已运行其他应用服务实例的、未充分利用的现成 VM。
* 想要在 Linux 上运行函数应用，或者想要提供要在其上运行函数的自定义映像。

您支付相同应用服务计划中的 function app 就像对于其他应用服务资源，例如 web 应用。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/overview-hosting-plans.md)。 

借助应用服务计划，可通过添加更多 VM 实例手动进行扩展，也可启用自动缩放。 有关详细信息，请参阅[手动或自动缩放实例计数](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)。 还可以通过选择不同的应用服务计划来进行增加。 有关详细信息，请参阅[增加 Azure 中的应用](../app-service/web-sites-scale.md)。 

在应用服务计划上运行 JavaScript 函数时，应选择具有较少 vCPU 的计划。 有关详细信息，请参阅[选择单核应用服务计划](functions-reference-node.md#choose-single-vcpu-app-service-plans)。 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

如果在应用服务计划上运行，应启用 AlwaysOn 设置，使函数应用能正常运行。 在应用服务计划中，如果函数运行时处于不活动状态，几分钟后就会进入空闲状态，因此只有 HTTP 触发器才能“唤醒”函数。 只能对应用服务计划使用始终可用。 在消耗计划中，平台会自动激活函数应用。

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="what-is-my-hosting-plan"></a>我采用了哪种托管计划

要确定你的函数应用所使用的托管计划，请在 [Azure 门户](https://portal.azure.com)中参阅函数应用的“概览”选项卡中的“应用服务计划/定价层”。 对于应用服务计划，还指明了定价层。 

![在门户中查看缩放计划](./media/functions-scale/function-app-overview-portal.png)

还可以使用 Azure CLI 来确定计划，如下所示：

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

此命令的输出为 `dynamic` 时，函数应用采用消耗量计划。 当此命令的输出是`ElasticPremium`，function app 是高级版计划中。  所有其他值均表示应用服务计划的层。

即使启用了 AlwaysOn，各函数的执行超时也由 [host.json](functions-host-json.md#functiontimeout) 项目文件中的 `functionTimeout` 设置控制。

## <a name="storage-account-requirements"></a>存储帐户要求

在任何计划中，函数应用都需要支持 Azure Blob、 队列、 文件和表存储的常规 Azure 存储帐户。 这是因为函数依赖于 Azure 存储提供管理触发器和记录函数执行等操作，但某些存储帐户不支持队列和表。 这些帐户包括仅限 blob 的存储帐户（包括高级存储）和使用区域冗余存储空间复制的常规用途存储帐户，已在创建函数应用时将从现有的“存储帐户”选项中过滤掉。

<!-- JH: Does using a Premium Storage account improve perf? -->

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介](../storage/common/storage-introduction.md#azure-storage-services)。

## <a name="how-the-consumption-and-premium-plans-work"></a>消耗计划和高级计划的工作原理

在消耗和高级版计划中，缩放控制器自动通过添加其他基于触发函数的事件数的 Functions 主机实例来缩放 CPU 和内存资源。 Functions 主机在消耗计划中的每个实例都限制为 1.5 GB 内存和 1 的 CPU。  主机的实例是整个函数应用，即在同一时间内的实例和规模的函数应用共享资源中的所有函数。 函数应用都共享相同的消耗计划是独立缩放。  在高级版计划中，你的计划大小将确定可用内存和 CPU 的所有应用在该实例上的该计划中。  

函数代码文件存储在 Azure 文件共享上函数的主存储帐户。 删除函数应用的主存储帐户时，函数代码文件将被删除并且无法恢复。

> [!NOTE]
> 在消耗量计划中使用 blob 触发器时，处理新的 blob 可能会出现长达 10 分钟的延迟。 函数应用处于空闲时会发生这种延迟。 函数应用运行后，就会立即处理 Blob。 若要避免此冷启动延迟，请使用高级计划，或使用[事件网格触发器](functions-bindings-event-grid.md)。 有关详细信息，请参阅 [blob 触发器绑定参考文章](functions-bindings-storage-blob.md#trigger)。

### <a name="runtime-scaling"></a>运行时缩放

Azure Functions 使用名为“缩放控制器”的组件来监视事件率以及确定是要扩大或缩小。 缩放控制器针对每种触发器类型使用试探法。 例如，使用 Azure 队列存储触发器时，它会根据队列长度和最旧队列消息的期限进行缩放。

缩放单位是 Function App。 横向扩展函数应用时，将分配额外的资源来运行 Azure Functions 主机的多个实例。 相反，计算需求下降时，扩展控制器将删除函数主机实例。 实例数最终会缩减为零，此时 Function App 中没有任何函数运行。

![用于监视事件和创建实例的扩展控制器](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>了解缩放行为

缩放可根据多种因素而异，可根据选定的触发器和语言以不同的方式缩放。 但是，当今的系统中存在一些缩放特征：

* 单个函数应用最多只能纵向扩展到 200 个实例。 不过，单个实例每次可以处理多个消息或请求，因此，对并发执行数没有规定的限制。
* HTTP 触发器的新实例分配最多一次每隔 1 秒。
* 对于非 HTTP 触发器，新的实例分配最多一次每隔 30 秒。

不同触发器还可能有不同的缩放限制，如下所述：

* [事件中心](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>可缩放应用的最佳做法和模式

函数应用的许多方面会影响其缩放，包括主机配置、运行时占用空间和资源效率。  有关详细信息，请查看[性能注意事项一文的“可扩展”部分](functions-best-practices.md#scalability-best-practices)。 还要注意随着函数应用的扩展，连接是如何实施的。 有关详细信息，请参阅[如何在 Azure Functions 中管理连接](manage-connections.md)。

### <a name="billing-model"></a>计费模式

消耗量计划的计费在 [Azure Functions 定价页]中有详细介绍。 使用量在 Function App 级别聚合，只会统计函数代码的执行时间。 以下是计费单位：

* 以千兆字节/秒 (GB-s) 计量的资源消耗量。 根据内存大小和函数应用中所有函数的执行时间组合计算得出。 
* **执行**。 每次为响应事件触发而执行函数时记为一次。

可以找到有用的查询和如何了解消耗帐单信息[计费常见问题](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)。

[Azure Functions 定价页]: https://azure.microsoft.com/pricing/details/functions
