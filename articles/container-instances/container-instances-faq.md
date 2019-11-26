---
title: 常见问题
description: 与 Azure 容器实例服务相关的常见问题的解答
author: dkkapur
ms.topic: article
ms.date: 4/25/2019
ms.openlocfilehash: b5888efe210ab0f3794895d350c5647b6f685880
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484132"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>有关 Azure 容器实例的常见问题

本文介绍有关 Azure 容器实例的常见问题。

## <a name="deployment"></a>部署

### <a name="how-large-can-my-container-image-be"></a>容器映像有多大？

Azure 容器实例上可部署容器映像的最大大小为 15 GB。 根据部署时的确切可用性，你可以部署更大的映像，但不保证这样做。

容器映像的大小会影响部署所需的时间，因此，一般情况下，你想要尽可能地减小容器映像。

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>如何加快容器的部署？

由于部署时间的主要 qps 之一是映像大小，因此请查看减小大小的方法。 删除不需要的层，或者减少映像中层的大小（通过选择较轻的基础操作系统映像）。 例如，如果运行的是 Linux 容器，请考虑使用 Alpine 作为基本映像，而不是使用完整的 Ubuntu 服务器。 同样，对于 Windows 容器，如有可能，请使用 Nano Server 基本映像。 

还应查看 Azure 容器映像中预缓存映像的列表，该列表可通过[列出缓存的映像](/rest/api/container-instances/listcachedimages)API 获取。 您可以为其中一个预缓存的图像切换图像层。 

有关缩短容器启动时间的详细[指南](container-instances-troubleshooting.md#container-takes-a-long-time-to-start)，请参阅。

### <a name="what-windows-base-os-images-are-supported"></a>支持哪些 Windows 基准操作系统映像？

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 基础映像

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver)： `10.0.14393.x`、`sac2016`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore)： `ltsc2016`、`10.0.14393.x`

> [!NOTE]
> 不支持基于半年频道版本1709或1803的 Windows 映像。

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 和客户端基本映像（预览）

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver)： `1809`、`10.0.17763.x`
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore)： `ltsc2019`、`1809``10.0.17763.x`
* [Windows](https://hub.docker.com/_/microsoft-windows)： `1809`、`10.0.17763.x` 

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>我应在容器中使用什么 .NET 或 .NET Core 映像层？ 

使用满足要求的最小映像。 对于 Linux，你可以使用*alpine* .net core 映像，自 .net core 2.1 发布后已受到支持。 对于 Windows，如果使用的是完整 .NET Framework，则需要使用 Windows Server Core 映像（仅限运行时映像，例如*4.7.2-windowsservercore-ltsc2016*）。 仅运行时映像较小，但不支持需要 .NET SDK 的工作负荷。

## <a name="availability-and-quotas"></a>可用性和配额

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>我应该为容器或容器组分配多少个核心和内存？

这实际上取决于工作负荷。 启动小规模并测试性能以查看容器的工作方式。 [监视 CPU 和内存资源的使用情况](container-instances-monitor.md)，然后根据在容器中部署的进程类型添加核心或内存。 

还要确保检查正在部署的区域的[资源可用性](container-instances-region-availability.md#availability---general)，以获取每个容器组的可用 CPU 内核和内存的上限。 

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>ACI 在哪些基础结构上运行？

Azure 容器实例旨在作为无服务器容器点播服务，因此，我们希望你集中精力开发容器，而无需担心基础结构！ 对于那些想要对性能进行比较的人而言，ACI 在各种 Sku 的 Azure Vm 集上运行，主要来自 F 和 D 系列。 我们预计将来会改变，因为我们将继续开发和优化服务。 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>我想在 ACI 上部署几千个核心-是否可以增加配额？
 
是（有时）。 请参阅[配额和限制](container-instances-quotas.md)一文，了解当前配额，以及可以按请求增加哪些限制。

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>能否部署超过4个内核和 16 GB RAM？

还不可以。 当前，这些是容器组的最大的。 请与 Azure 支持部门联系，以满足特定要求。 

### <a name="when-will-aci-be-in-a-specific-region"></a>何时将 ACI 置于特定区域？

当前区域可用性在[此处](container-instances-region-availability.md#availability---general)发布。 如果需要特定区域，请联系 Azure 支持部门。

## <a name="features-and-scenarios"></a>功能和方案

### <a name="how-do-i-scale-a-container-group"></a>如何实现缩放容器组？

目前，缩放不适用于容器或容器组。 如果需要运行更多的实例，请使用我们的 API 自动执行，并创建更多请求以将容器组创建到服务。 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>自定义 VNet 中运行的实例可使用哪些功能？

你可以在所选的 Azure 虚拟网络中部署容器组，并将专用 Ip 委托给容器组，以在 VNet 中跨 Azure 资源路由流量。 在虚拟网络中部署容器组目前处于预览阶段，此功能的某些方面可能会在正式发布（GA）之前更改。 请参阅[预览版限制](container-instances-vnet.md#preview-limitations)，了解更新的信息。

## <a name="pricing"></a>定价

### <a name="when-does-the-meter-start-running"></a>计量器何时开始运行？

容器组持续时间从开始拉取第一个容器的映像（对于新部署）或容器组已重新启动（如果已部署）开始，直到容器组停止为止。 请参阅[容器实例定价](https://azure.microsoft.com/pricing/details/container-instances/)的详细信息。

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>我的容器停止时是否停止计费？

在整个容器组停止后，计量将停止运行。 只要容器组中的容器正在运行，我们就会保留资源，以防需要重新启动容器。 

## <a name="next-steps"></a>后续步骤

* [详细了解](container-instances-overview.md)Azure 容器实例。
* [排查](container-instances-troubleshooting.md)Azure 容器实例中的常见问题。