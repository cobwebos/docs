---
title: Azure Functions 的缩放和托管
description: 了解如何在 Azure Functions 消耗计划和高级计划之间进行选择。
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 26924498f32b8aac2e3e7fb5cfd7c1965ee5884f
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/07/2020
ms.locfileid: "86025822"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions 的缩放和托管

在 Azure 中创建函数应用时，必须为应用选择托管计划。 有三个可用于 Azure Functions 的基本托管计划：[消耗计划](#consumption-plan)、[高级计划](#premium-plan)和[专用（应用服务）计划](#app-service-plan)。 Linux 和 Windows 虚拟机上的所有托管计划均已正式发布（GA）。

选择的托管计划决定了以下行为：

* 函数应用的缩放方式。
* 每个函数应用实例可用的资源。
* 对 Azure 虚拟网络连接等高级功能的支持。

当代码运行时，消耗和高级计划会自动添加计算能力。 应用在需要处理负载时会横向扩展，在代码停止运行时会缩小。 此外，对于消耗计划，无需提前支付空闲 VM 或预留容量的费用。  

高级计划提供了额外的功能，如高级计算实例、使实例始终处于热热和 VNet 连接的能力。

选择应用服务计划可以利用你管理的专用基础结构。 函数应用不会基于事件进行缩放，这意味着，它永远不会缩小到零。 （要求启用 [Always On](#always-on)。）

有关各种宿主计划（包括基于 Kubernetes 的托管）之间的详细比较，请参阅[宿主计划比较部分](#hosting-plans-comparison)。

## <a name="consumption-plan"></a>消耗计划

使用消耗计划时，会根据传入事件数自动添加和删除 Azure Functions 主机实例。 这个无服务器计划会自动缩放，仅在函数运行时，才会产生计算资源费用。 在消耗计划中，函数执行在可配置的时间段后超时。

账单将基于执行数量、执行时间和所用内存。 账单是基于函数应用内的所有函数聚合而生成的。 有关详细信息，请参阅 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)。

消耗计划是默认的托管计划，它提供了以下优势：

* 仅当函数正在运行时才产生费用
* 可自动扩展，即使是在负载较高期间

可将同一区域中的函数应用分配到同一个消耗计划。 在同一个消耗计划中运行多个应用不会产生负面影响。 将多个应用分配到同一个消耗计划不会影响每个应用的复原能力、可伸缩性或可靠性。

若要详细了解在消耗计划中运行时如何估算成本，请参阅[了解消耗计划成本](functions-consumption-costs.md)。

## <a name="premium-plan"></a><a name="premium-plan"></a>高级计划

使用高级计划时，会根据传入事件的数目，添加和删除 Azure Functions 主机的实例，就像消耗计划一样。  高级计划支持以下功能：

* 永久温实例，以避免任何冷启动
* VNet 连接
* 无限制的执行持续时间（保证为60分钟）
* 高级实例大小（一个核心、两个核心和四个核心实例）
* 更可预测的定价
* 针对具有多个 function app 的计划的高密度应用分配

若要了解如何在高级计划中创建函数应用，请参阅[Azure Functions 高级计划](functions-premium-plan.md)。

高级计划的计费不是每次执行和内存计费，而是基于所需和预准备好实例使用的核心秒数和内存。 每个计划至少必须有一个实例处于热状态。 这意味着，每个活动计划的每月成本最低，而不考虑执行次数。 请记住，高级计划中的所有 function app 共享准备好和活动实例。

在以下情况下，请考虑 Azure Functions 高级计划：

* 函数应用持续或几乎持续运行。
* 您的小型执行数量很大，并且在消耗计划中的执行费用较高，但费用较低。
* 你需要比消耗计划提供的更多的 CPU 或内存选项。
* 你的代码所需的运行时间超过消耗计划[允许的最长执行时间](#timeout)。
* 你需要只能在高级计划（如虚拟网络连接）上使用的功能。 

## <a name="dedicated-app-service-plan"></a><a name="app-service-plan"></a>专用（应用服务）计划

函数应用也可以像其他应用服务应用（基本、标准、高级和隔离 SKU）一样在相同的专用 VM 上运行。

对于以下情况，可以考虑使用应用服务计划：

* 具有已运行其他应用服务实例的、未充分利用的现成 VM。
* 需要提供用于运行函数的自定义映像。

应用服务计划中函数应用的费用与其他应用服务资源（例如 Web 应用）的费用相同。 如需详细了解如何使用应用服务计划，请参阅 [Azure 应用服务计划深入概述](../app-service/overview-hosting-plans.md)。

借助应用服务计划，可通过添加更多 VM 实例手动进行横向扩展。 也可以启用自动缩放。 有关详细信息，请参阅[手动或自动缩放实例计数](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)。 还可以通过选择不同的应用服务计划来进行增加。 有关详细信息，请参阅[增加 Azure 中的应用](../app-service/manage-scale-up.md)。 

在应用服务计划上运行 JavaScript 函数时，应选择具有较少 vCPU 的计划。 有关详细信息，请参阅[选择单核应用服务计划](functions-reference-node.md#choose-single-vcpu-app-service-plans)。 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

在[应用服务环境](../app-service/environment/intro.md)（ASE）中运行可以完全隔离函数并利用高缩放性。

### <a name="always-on"></a><a name="always-on"></a> Always On

如果在应用服务计划上运行，应启用 AlwaysOn 设置，使函数应用能正常运行****。 在应用服务计划中，如果函数运行时处于不活动状态，几分钟后就会进入空闲状态，因此只有 HTTP 触发器才能“唤醒”函数。 只能对应用服务计划使用始终可用。 在消耗计划中，平台会自动激活函数应用。

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


即使启用了 AlwaysOn，各函数的执行超时也由 [host.json](functions-host-json.md#functiontimeout) 项目文件中的 `functionTimeout` 设置控制。

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>确定现有应用程序的托管计划

要确定你的函数应用所使用的托管计划，请在 [Azure 门户](https://portal.azure.com)中参阅函数应用的“概览”选项卡中的“应用服务计划”**** ****。 若要查看定价层，请选择“应用服务计划”的名称，然后从左侧窗格中选择“属性”**** ****。

![在门户中查看缩放计划](./media/functions-scale/function-app-overview-portal.png)

还可以使用 Azure CLI 来确定计划，如下所示：

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

此命令的输出为 `dynamic` 时，函数应用采用消耗计划。 此命令的输出为 `ElasticPremium` 时，函数应用采用高级计划。 所有其他值均表示应用服务计划的不同层。

## <a name="storage-account-requirements"></a>存储帐户要求

在任何计划中，函数应用需要一个支持 Azure Blob、队列、文件和表存储的常规 Azure 存储帐户。 这是因为 Azure Functions 依赖于 Azure 存储来执行管理触发器和记录函数执行等操作，但某些存储帐户不支持队列和表。 这些帐户包括仅限 blob 的存储帐户（包括高级存储）和使用区域冗余存储空间复制的常规用途存储帐户，已在创建函数应用时将从现有的“存储帐户”选项中过滤掉****。

触发器和绑定也可以使用函数应用使用的相同存储帐户来存储应用程序数据。 但是，对于存储密集型操作，应使用单独的存储帐户。  

多个函数应用可以共享同一个存储帐户，而不会出现任何问题。 （一个很好的例子是，当你使用 Azure 存储模拟器在本地环境中开发多个应用时，它的作用类似于一个存储帐户。） 

<!-- JH: Does using a Premium Storage account improve perf? -->

若要了解有关存储帐户类型的详细信息，请参阅 [Azure 存储服务简介](../storage/common/storage-introduction.md#core-storage-services)。

## <a name="how-the-consumption-and-premium-plans-work"></a>消耗计划和高级计划的工作原理

在消耗和高级计划中，Azure Functions 基础结构通过基于其函数触发的事件数来添加其他函数主机实例，从而缩放 CPU 和内存资源。 消耗计划中托管的每个函数实例限制为 1.5 GB 的内存和一个 CPU。  主机实例是整个函数应用，这意味着函数应用中的所有函数共享某个实例中的资源并同时缩放。 共享同一消耗计划的函数应用单独缩放。  在高级计划中，计划大小将确定该实例上该计划中的所有应用程序的可用内存和 CPU。  

函数代码文件存储在函数主要存储帐户中的 Azure 文件共享上。 删除函数应用的主存储帐户时，函数代码文件将被删除并且无法恢复。

### <a name="runtime-scaling"></a>运行时缩放

Azure Functions 使用名为“缩放控制器”的组件来监视事件率以及确定是要横向扩展还是横向缩减。** 缩放控制器针对每种触发器类型使用试探法。 例如，使用 Azure 队列存储触发器时，它会根据队列长度和最旧队列消息的期限进行缩放。

Azure Functions 的缩放单位为函数应用。 横向扩展函数应用时，将分配额外的资源来运行 Azure Functions 主机的多个实例。 相反，计算需求下降时，扩展控制器将删除函数主机实例。 当函数应用中没有运行函数时，实例数最终会*缩减*为零。

![用于监视事件和创建实例的扩展控制器](./media/functions-scale/central-listener.png)

### <a name="cold-start"></a>冷启动

函数应用空闲几分钟后，该平台可能会将你的应用程序的运行次数缩小到零。 下一个请求具有从零到1的缩放延迟。 此滞后时间称为_冷启动_。 函数应用必须加载的依赖项的数目可能会影响冷启动时间。 冷启动是同步操作的问题，例如必须返回响应的 HTTP 触发器。 如果冷启动会影响你的功能，请考虑在高级计划中或在启用了 Always on 的专用计划中运行。   

### <a name="understanding-scaling-behaviors"></a>了解缩放行为

缩放可根据多种因素而异，可根据选定的触发器和语言以不同的方式缩放。 需要注意缩放行为的以下几个细节：

* 单个函数应用最多只能横向扩展到 200 个实例。 不过，单个实例每次可以处理多个消息或请求，因此，对并发执行数没有规定的限制。
* 对于 HTTP 触发器，将最多每隔 1 秒分配一次新实例。
* 对于非 HTTP 触发器，将最多每隔 30 秒分配一次新实例。 在[高级计划](#premium-plan)中运行时，缩放速度会更快。
* 对于服务总线触发器，请使用资源的_管理_权限，以实现最有效的缩放。 使用_侦听_权限时，由于队列长度不能用于通知缩放决策，缩放不够准确。 若要详细了解如何在服务总线访问策略中设置权限，请参阅[共享访问授权策略](../service-bus-messaging/service-bus-sas.md#shared-access-authorization-policies)。
* 有关事件中心触发器，请参阅参考文章中的[缩放指南](functions-bindings-event-hubs-trigger.md#scaling)。 

### <a name="best-practices-and-patterns-for-scalable-apps"></a>可缩放应用的最佳做法和模式

函数应用的许多方面会影响其缩放，包括主机配置、运行时占用空间和资源效率。  有关详细信息，请查看[性能注意事项一文的“可扩展”部分](functions-best-practices.md#scalability-best-practices)。 还要注意随着函数应用的扩展，连接是如何实施的。 有关详细信息，请参阅[如何在 Azure Functions 中管理连接](manage-connections.md)。

有关 Python 和 Node.js 中的缩放的详细信息，请参阅[Azure Functions Python 开发人员指南-缩放和并发性](functions-reference-python.md#scaling-and-concurrency)和[Azure Functions Node.js 开发人员指南-缩放和并发](functions-reference-node.md#scaling-and-concurrency)。

### <a name="billing-model"></a>计费模式

不同计划的计费在 [Azure Functions 定价页](https://azure.microsoft.com/pricing/details/functions/)中有详细介绍。 使用量在 Function App 级别聚合，只会统计函数代码的执行时间。 以下是计费单位：

* 以千兆字节/秒 (GB-s) 计量的资源消耗量****。 根据内存大小和函数应用中所有函数的执行时间组合计算得出。 
* **执行**。 每次为响应事件触发而执行函数时记为一次。

在[帐单常见问题解答](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)中可以找到有关如何了解消费帐单的有用查询和信息。

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="hosting-plans-comparison"></a>托管计划比较

以下比较表显示了有助于决策 Azure Functions 应用托管计划选择的所有重要方面：

### <a name="plan-summary"></a>计划摘要
| | |
| --- | --- |  
|**[消耗计划](#consumption-plan)**| 当函数运行时，自动缩放并仅为计算资源付费。 在消耗计划中，根据传入事件的数量动态添加和删除函数主机的实例。<br/> ✔默认托管计划。<br/>仅当函数正在运行时，才✔付费。<br/>即使在高负载期间，也✔自动扩展。|  
|**[高级计划](#premium-plan)**|尽管自动根据需求进行缩放，但请使用准备好工作人员在空闲后无延迟运行应用程序，在功能更强大的实例上运行，并连接到 Vnet。 除了应用服务计划的所有功能之外，还请考虑在以下情况下 Azure Functions 高级计划： <br/>✔函数应用连续运行，或几乎连续运行。<br/>✔您的小型执行数量很大，并且在消耗计划中有高执行帐单但低达 GB 的费用。<br/>✔需要比消耗计划所提供的更多的 CPU 或内存选项。<br/>✔你的代码所需的运行时间超过消耗计划允许的最长执行时间。<br/>✔需要的功能仅适用于高级计划，如虚拟网络连接。|  
|**[专用计划](#app-service-plan)**<sup>1</sup>|在应用服务计划中以定期应用服务计划费率运行你的函数。 适用于长时间运行的操作，以及需要更多预测性和成本的情况。 对于以下情况，可以考虑使用应用服务计划：<br/>✔现有的、未充分利用的 Vm 已在运行其他应用服务实例。<br/>✔你想要提供用于运行函数的自定义映像。|  
|**[ASE](#app-service-plan)**<sup>1</sup>|应用服务环境（ASE）是一项应用服务功能，可提供完全隔离和专用的环境，以便高度安全地运行应用服务应用。 Ase 适用于需要以下操作的应用程序工作负荷： <br/>✔非常大的规模。<br/>✔隔离和安全的网络访问。<br/>✔高内存使用率。|  
| **[Kubernetes](functions-kubernetes-keda.md)** | Kubernetes 提供了一个完全隔离的专用环境，在 Kubernetes 平台之上运行。  Kubernetes 适用于需要以下操作的应用程序工作负荷： <br/>✔自定义硬件要求。<br/>✔隔离和安全的网络访问。<br/>✔功能在混合或多云环境中运行。<br/>✔与现有的 Kubernetes 应用程序和服务一起运行。|  

<sup>1</sup> 有关各种应用服务计划选项的特定限制，请参阅[应用服务计划限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="operating-systemruntime"></a>操作系统/运行时

| | Linux<sup>1</sup><br/>仅限代码 | Windows<sup>2</sup><br/>仅限代码 | Linux<sup>1、3</sup><br/>Docker 容器 |
| --- | --- | --- | --- |
| **[消耗计划](#consumption-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python | .NET Core<br/>Node.js<br/>Java<br/>PowerShell Core | 不支持  |
| **[高级计划](#premium-plan)** | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python  | 
| **[专用计划](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python|.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |
| **[ASE](#app-service-plan)**<sup>4</sup> | .NET Core<br/>Node.js<br/>Java<br/>Python |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core  |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python | 
| **[Kubernetes](functions-kubernetes-keda.md)** | 不适用 | 不适用 |.NET Core<br/>Node.js<br/>Java<br/>PowerShell Core<br/>Python |

<sup>1</sup>Linux 是 Python 运行时堆栈唯一支持的操作系统。  
<sup>2</sup>Windows 是 PowerShell 运行时堆栈唯一支持的操作系统。   
<sup>3</sup>Linux 是 Docker 容器唯一支持的操作系统。
<sup>4</sup>有关各个应用服务计划选项的特定限制，请参阅[应用服务计划限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="scale"></a>缩放

| | 横向扩展 | 最大实例数 |
| --- | --- | --- |
| **[消耗计划](#consumption-plan)** | 事件驱动。 即使是在负载较高期间也可自动扩展。 Azure Functions 基础结构通过基于触发函数的事件数添加函数主机的其他实例，从而缩放 CPU 和内存资源。 | 200 |
| **[高级计划](#premium-plan)** | 事件驱动。 即使是在负载较高期间也可自动扩展。 Azure Functions 基础结构通过基于触发函数的事件数添加函数主机的其他实例，从而缩放 CPU 和内存资源。 |100|
| **[专用计划](#app-service-plan)**<sup>1</sup> | 手动/自动缩放 |10-20|
| **[ASE](#app-service-plan)**<sup>1</sup> | 手动/自动缩放 |100 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | 使用[KEDA](https://keda.sh)的 Kubernetes 群集的事件驱动的自动缩放。 | 因 &nbsp; 群集而异 &nbsp; 。&nbsp;&nbsp;|

<sup>1</sup> 有关各种应用服务计划选项的特定限制，请参阅[应用服务计划限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="cold-start-behavior"></a>冷启动行为

|    |    | 
| -- | -- |
| **[消耗 &nbsp; 计划](#consumption-plan)** | 如果空闲一段时间，应用可能会扩展到零，这意味着某些请求可能会在启动时产生额外的延迟。  消耗计划确实具有一些优化，可帮助减少冷启动时间，包括从已具有运行函数主机和语言进程的准备好占位符函数拉取。 |
| **[高级计划](#premium-plan)** | 永久温实例，以避免任何冷启动。 |
| **[专用计划](#app-service-plan)**<sup>1</sup> | 在专用计划中运行时，函数主机可以连续运行，这意味着冷启动并不是真正的问题。 |
| **[ASE](#app-service-plan)**<sup>1</sup> | 在专用计划中运行时，函数主机可以连续运行，这意味着冷启动并不是真正的问题。 |
| **[Kubernetes](functions-kubernetes-keda.md)**  | 取决于 KEDA 配置。 可将应用配置为始终运行且绝不会开始冷启动，或将应用配置为缩放到零，这将导致冷启动新事件。 

<sup>1</sup> 有关各种应用服务计划选项的特定限制，请参阅[应用服务计划限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

### <a name="service-limits"></a>服务限制

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]

### <a name="networking-features"></a>网络功能

[!INCLUDE [functions-networking-features](../../includes/functions-networking-features.md)]

### <a name="billing"></a>计费

| | | 
| --- | --- |
| **[消耗计划](#consumption-plan)** | 仅为函数运行时间付费。 账单将基于执行数量、执行时间和所用内存。 |
| **[高级计划](#premium-plan)** | 高级计划基于所需和预准备好实例使用的核心秒数和内存。 每个计划至少有一个实例必须始终处于热状态。 此计划提供更可预测的定价。 |
| **[专用计划](#app-service-plan)**<sup>1</sup> | 应用服务计划中函数应用的费用与其他应用服务资源（例如 Web 应用）的费用相同。|
| **[ASE](#app-service-plan)**<sup>1</sup> | ASE 的按月费率为基础结构付费，并且不会随 ASE 的大小变化。 此外，每个应用服务计划的 vCPU。 ASE 中托管的所有应用都在“隔离”定价 SKU 中。 |
| **[Kubernetes](functions-kubernetes-keda.md)**| 只需支付 Kubernetes 群集的成本;函数无额外计费。 函数应用作为应用程序工作负荷在群集顶层运行，就像常规应用一样。 |

<sup>1</sup> 有关各种应用服务计划选项的特定限制，请参阅[应用服务计划限制](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)。

## <a name="next-steps"></a>后续步骤

+ [快速入门：使用 Visual Studio Code 创建 Azure Functions 项目](functions-create-first-function-vs-code.md)
+ [Azure Functions 中的部署技术](functions-deployment-technologies.md) 
+ [Azure Functions 开发人员指南](functions-reference.md)
