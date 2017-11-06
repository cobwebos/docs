---
title: "Azure 应用服务 Web 应用中的应用服务计划 | Microsoft Docs"
description: "了解针对 Azure 应用服务的应用服务计划的工作原理，以及如何利用它们进行管理。"
keywords: "应用服务, azure 应用服务, 缩放, 可缩放, 应用服务计划, 应用服务成本"
services: app-service
documentationcenter: 
author: btardif
manager: erikre
editor: 
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: byvinyal
ms.openlocfilehash: fb5b782f09bdd8c8a862eddfbd65b0f86ef8d08c
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2017
---
# <a name="app-service-plans-in-azure-app-service-web-apps"></a>Azure 应用服务 Web 应用中的应用服务计划

应用服务计划代表用于托管应用的物理资源的集合。

应用服务计划定义：

- 区域（美国西部、美国东部，等等）
- 规模计数（一个、两个、三个实例等）
- 实例大小（小、中、大）
- SKU（免费、共享、基本、标准、高级、高级 V2、隔离）

[Azure 应用服务](http://go.microsoft.com/fwlink/?LinkId=529714)中的 Web 应用、移动应用、API 应用、Function App（或 Functions）均在应用服务计划中运行。  同一订阅和区域中的应用可共享应用服务计划。 

分配到**应用服务计划**的所有应用程序将共享该计划定义的资源。 在单个应用服务计划中托管多个应用时，此共享操作可节省成本。

**应用服务计划**可以从“免费”和“共享”层扩展到“基本”、“标准”、“高级”和“隔离”层。 每个更高级别的层均授权访问更多资源和功能。

如果应用服务计划设置为“基本”层或更高级别，则可以控制 VM 的**大小**和规模计数。

例如，如果计划配置为使用“标准”层中的两个“小型”实例，则该计划中的所有应用都会在这两个实例上运行。 这些应用还可访问“标准”层功能。 运行应用的计划实例是完全托管的，且可用性高。

> [!IMPORTANT]
> 应用服务计划的定价层 (SKU) 确定成本，而不是其中托管的应用数。

本文探讨应用服务计划的主要特征（如定价层和缩放），以及在管理应用时这些特征的工作方式。

## <a name="new-pricing-tier-premiumv2"></a>新的定价层：高级 V2

与“标准”层相比，新的“高级 V2”层提供 [Dv2 系列 VM](../virtual-machines/windows/sizes-general.md#dv2-series)，此系列 VM 配备更快的处理器、SSD 存储以及双倍的内存核心比。 **高级 V2** 还支持通过增加实例数扩大规模，同时仍提供标准计划中的所有高级功能。 **高级 V2** 中包含现有“高级”层中提供的所有功能。

类似于其他专用层，以下三种 VM 大小可用于此层：

- 小型（1 个 CPU 核心，3.5 GiB 内存） 
- 中型（2 个 CPU 核心，7 GiB 内存） 
- 大型（4 个 CPU 核心，14 GiB 内存）  

有关**高级 V2** 定价信息，请参阅[应用服务定价](/pricing/details/app-service/)。

若要开始使用新的“高级 V2”定价层，请参阅[为应用服务配置“高级 V2”层](app-service-configure-premium-tier.md)。

## <a name="apps-and-app-service-plans"></a>应用和应用服务计划

应用服务中的应用在任何特定的时刻只能与一个应用服务计划相关联。

应用和计划都包含在**资源组**中。 资源组充当该组中每个资源的生命周期界限。 可以使用资源组来同时管理应用程序的所有组件。

由于一个资源组可以有多个应用服务计划，因此用户可以将不同的应用分配给不同的物理资源。

例如，可以将资源分到开发环境、测试环境和生产环境中。 具有单独的生产和开发/测试环境，可将资源隔离。 通过这种方式对新版应用进行负载测试时，不会与为真正的客户提供服务的生产应用竞争相同的资源。

单个资源组中存在多个计划时，也可定义一个跨多个地理区域的应用程序。

例如，在两个区域中运行的高度可用的应用至少包括两个计划，一个区域一个计划，一个计划关联一个应用。 在这种情况下，应用的所有副本包含在单个资源组中。 一个资源组内具有多个计划和多个应用，让管理、控制和查看应用程序的运行状况变得简单。

## <a name="create-an-app-service-plan-or-use-existing-one"></a>创建应用服务计划或使用现有应用服务计划

在应用服务中创建新的 Web 应用时，可以通过将应用放到现有应用服务计划中来共享托管资源。 若要确定新的应用是否包含所需的资源，需要了解现有应用服务计划的容量，以及新应用预期的负载。 过度分配资源可能会导致新应用和现有应用停机。

我们建议在以下情况下将应用放在新应用服务计划中：

- 应用占用大量资源。
- 应用与现有计划中托管的其他应用的比例系数不同。
- 应用需要其他地理区域中的资源。

这样一来，可以为应用分配新的资源集，并更好地控制应用。

## <a name="create-an-app-service-plan"></a>创建应用服务计划

> [!TIP]
> 如果已有应用服务环境，请参阅[在应用服务环境中创建应用服务计划](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)。

可以在创建应用时创建一个空的应用服务计划。

在 [Azure 门户](https://portal.azure.com)中，单击**“新建”** > **“Web + 移动”**，然后选择**“Web 应用”**或其他应用服务应用类型。

![在 Azure 门户中创建应用。][createWebApp]

然后即可为新应用选择或创建应用服务计划。

 ![创建应用服务计划。][createASP]

要创建应用服务计划，可单击“[+] 新建”，键入“应用服务计划”名称，并选择相应的“位置”。 单击“定价层”，并为服务选择适当的服务定价层。 选择“全部查看”以查看其他定价选项，例如“免费”和“共享”。 选择定价层后，单击“选择”按钮。

## <a name="move-an-app-to-a-different-app-service-plan"></a>将应用移到其他应用服务计划

可以在 [Azure 门户](https://portal.azure.com)中将应用移到其他应用服务计划。 只要计划属于_同一资源组和同一地理区域_，即可在计划之间移动应用。

要将应用移动到其他计划：

- 导航到要移动的应用。
- 在“菜单”中，查找“应用服务计划”部分。
- 选择“更改应用服务计划”启动该过程。

“更改应用服务计划”将打开“应用服务计划”选择器。 此时，可以选取要移入此应用的现有计划。 仅显示同一资源组和同一区域中的计划。

![应用服务计划选择器。][change]

每个计划都有自己的定价层。 例如，将站点从免费层移到标准层时，分配给站点的所有应用都可使用标准层的功能和资源。

## <a name="clone-an-app-to-a-different-app-service-plan"></a>将应用克隆到其他应用服务计划

要将应用移到其他区域，也可以使用应用克隆。 不管位于什么区域，克隆都会在新的或现有的应用服务计划中复制应用。

可以在菜单的“开发工具”部分中找到“克隆应用”。

> [!IMPORTANT]
> 克隆有一些限制，详见 [Azure 应用服务应用克隆](app-service-web-app-cloning.md)。

## <a name="scale-an-app-service-plan"></a>缩放应用服务计划

可通过三种方式缩放一个计划：

- **更改计划的定价层**。 基本层中的计划可转换为标准层中的计划，分配给它的所有应用可转换为使用标准层的功能。
- **更改计划的实例大小**。 例如，可以将“基本”层中使用小型实例的计划改为使用大型实例。 与该计划关联的所有应用即可使用实例大小提高以后带来的更多内存和 CPU 资源。
- **更改计划的实例计数**。 例如，可以将最大规模为 3 个实例的“标准”计划扩展到 10 个实例。 “高级”计划可以扩展到 20 个实例（取决于可用性）。 与该计划关联的所有应用即可使用实例数量提高以后带来的更多内存和 CPU 资源。

单击应用或应用服务计划设置下的“向上缩放”项即可更改定价层和实例大小。 更改会应用到应用服务计划并影响其所托管的所有应用。

 ![设置向上缩放应用的值。][pricingtier]

## <a name="app-service-plan-cleanup"></a>应用服务计划清理

> [!IMPORTANT]
> 未与任何应用关联的**应用服务计划**仍会产生费用，因为它们继续保留计算容量。

为了避免产生意想不到的费用，当应用服务计划中托管的最后一个应用被删除时，默认情况下也会删除生成的空应用服务计划。

## <a name="summary"></a>摘要

应用服务计划表示一组可在应用间共享的功能和容量。 可以通过应用服务计划灵活地将特定应用分配给一组资源，并进一步优化 Azure 资源的使用情况。 若要节省测试环境的资金，可通过这种方式跨多个应用共享一个计划。 也可跨多个区域和计划进行伸缩，实现生产环境吞吐量的最大化。

## <a name="whats-changed"></a>发生的更改

- 有关从网站更改为应用服务的指南，请参阅 [Azure 应用服务及其对现有 Azure 服务的影响](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
