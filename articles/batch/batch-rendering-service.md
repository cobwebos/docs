---
title: Azure Batch 渲染概述
description: 介绍如何使用 Azure 进行渲染，并提供 Azure Batch 渲染功能的概述
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 4101f6819dff81376dcab47adb57e4b8ef35e094
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036708"
---
# <a name="rendering-using-azure"></a>使用 Azure 进行渲染

渲染是创建 3D 模型并将其转换为 2D 图像的过程。 在 Autodesk 3ds Max、Autodesk Maya 和 Blender 等应用程序中创作 3D 场景文件。  Autodesk Maya、Autodesk Arnold、Chaos Group V-Ray 和 Blender Cycles 等渲染应用程序可生成 2D 图像。  有时，可以从场景文件创建单一的图像。 但是，常见的操作是建模并渲染多个图像，然后将其组合成动画。

传媒娱乐行业往往使用渲染工作负荷来生成特效 (VFX)。 广告、零售、石油和天然气及制造等其他众多行业也会使用渲染。

渲染过程属于计算密集型工作；要生成的帧/图像数可能很多，而渲染每个图像可能需要大量的时间。  因此，渲染是一个完美的批处理工作负荷，可以利用 Azure 和 Azure Batch 来并行运行多个渲染器。

## <a name="why-use-azure-for-rendering"></a>为何使用 Azure 进行渲染？

出于多种原因，渲染是非常适合 Azure 与 Azure Batch 的工作负荷：

* 可将渲染作业拆分为多个片段，然后使用多个 VM 并行运行这些片段：
  * 动画由许多帧组成，每个帧可以并行渲染。  可用于处理每个帧的 VM 越多，生成所有帧和动画的速度就越快。
  * 某些渲染软件允许将单个帧分解为多个片段，例如平铺图像或切片。  每个片段可以单独渲染，并在完成所有片段后合并成最终的图像。  可用的 VM 越多，渲染帧的速度就越快。
* 渲染项目可能需要庞大的规模：
  * 单个帧可能很复杂，即使在高端硬件上，也需要花费多个小时才能完成渲染；而动画可能由几十万个帧组成。  优质动画需要占用大量的计算资源，才能在合理的时间内完成渲染。  在某些情况下，并行渲染数千个帧需要使用 100,000 多个核心。
* 渲染项目基于项目，需要不同数量的计算资源：
  * 根据需要分配计算和存储容量，根据项目期间的负载扩展或缩减容量，并在完成项目后删除容量。
  * 为分配的容量付费，没有负载时不需要付费（例如，在项目切换时）。
  * 为意外变化导致的需求喷发做好准备；如果以后项目发生意外的变化，并且需要在短时间内处理好这些变化，则可以进行扩展。
* 根据应用程序、工作负荷和时间范围，从各种硬件中进行选择：
  * Azure 中提供了可以使用 Batch 进行分配和管理的各种硬件。
  * 根据具体的项目，要求可能体现在最佳性价比或者最佳总体性能方面。  不同的场景和/或渲染应用程序具有不同的内存要求。  某些渲染应用程序可以利用 GPU 来获得最佳性能或某些功能。 
* 低优先级 VM 可降低成本：
  * 与普通的按需 VM 相比，低优先级 VM 享有较高的折扣，适用于某些作业类型。
  * 低优先级 VM 可由 Azure Batch 分配。在 Batch 中，可以灵活使用这些 VM 来应对各种要求。  Batch 池可以包含专用 VM 和低优先级 VM，并且随时可以更改 VM 类型的混合形式。

## <a name="options-for-rendering-on-azure"></a>Azure 上的渲染选项

可为渲染工作负荷使用多种 Azure 功能。  要使用哪些功能取决于任何现有环境和要求。

### <a name="existing-on-premises-rendering-environment-using-a-render-management-application"></a>使用渲染管理应用程序的现有本地渲染环境

最常见的案例是，通过 PipelineFX Qube、Royal Render 或 Thinkbox Deadline 等渲染管理应用程序管理某个现有的本地渲染场。  要求是使用 Azure VM 扩展本地渲染场的容量。

渲染管理软件原生支持 Azure，或者，我们可以提供用于添加 Azure 支持的插件。 有关支持的渲染管理器和功能的详细信息，请参阅有关[使用渲染管理器](https://docs.microsoft.com/azure/batch/batch-rendering-render-managers)的文章。

### <a name="custom-rendering-workflow"></a>自定义渲染工作流

VM 的要求是扩展现有的渲染场。  Azure Batch 池可以分配大量的 VM，使低优先级 VM 可供使用并像全价 VM 一样动态自动缩放，同时，为流行的渲染应用程序提供即用即付许可。

### <a name="no-existing-render-farm"></a>没有现有的渲染场

客户端工作站可能正在执行渲染，但渲染工作负荷不断增大，并长时间独占了工作站容量。  可以使用 Azure Batch 按需分配渲染场计算资源，并计划 Azure 渲染场的渲染作业。

## <a name="azure-batch-rendering-capabilities"></a>Azure Batch 的渲染功能

Azure Batch 允许在 Azure 中并行运行工作负荷。  Azure Batch 可用于创建和管理要在其上安装与运行应用程序的大量 VM。  它还提供全面的作业计划功能来运行这些应用程序的实例、将任务分配提供给 VM、执行排队和应用程序监视，等等。

Azure Batch 可用于许多工作负荷，但以下功能专门用于简化和加速渲染工作负荷的运行。

* 预装了图形和渲染应用程序的 VM 映像：
  * Azure 市场中的某些 VM 映像包含流行的图形和渲染应用程序，使用它们无需自行安装这些应用程序，或者创建装有这些应用程序的自定义映像。 
* 渲染应用程序的即用即付许可：
  * 可以选择按分钟支付应用程序的费用，此外，可以支付计算 VM 的费用（这可以避免购买许可证），有时还可以配置应用程序的许可证服务器。  付费使用也意味着，当许可证的数量不固定时，可以应对不同和意外的负载。
  * 此外，可以结合自己的许可证使用预装的应用程序，而不是使用即用即付许可。
* 用于客户端设计和建模应用程序的插件：
  * 最终用户可以通过插件直接从客户端应用程序（例如 Autodesk Maya）使用 Azure Batch，以便创建池、提交作业，以及利用更多的计算容量来以更快的速度执行渲染。
* 渲染管理器集成：
  * Azure Batch 将集成到渲染管理应用程序，或者，我们会提供插件来实现 Azure Batch 集成。

可通过多种方法使用 Azure Batch，所有这些方法同样适用于 Azure Batch 渲染。

* API：
  * 使用 [REST](https://docs.microsoft.com/rest/api/batchservice)、[.NET](https://docs.microsoft.com/dotnet/api/overview/azure/batch)、[Python](https://docs.microsoft.com/python/api/overview/azure/batch)、[Java](https://docs.microsoft.com/java/api/overview/azure/batch) 或其他支持的 API 编写代码。  开发人员可将 Azure Batch 功能集成到其现有应用程序或工作流（不管是在云中还是本地）中。  例如，[Autodesk Maya 插件](https://github.com/Azure/azure-batch-maya)利用 Batch Python API 来调用 Batch、创建和管理池、提交作业和任务，以及监视状态。
* 命令行工具：
  * 可以使用 [Azure 命令行](https://docs.microsoft.com/cli/azure/)或 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 来编写 Batch 脚本。
  * 具体而言，Batch CLI 模板支持可以简化创建池和提交作业的操作。
* UI：
  * [Batch Explorer](https://github.com/Azure/BatchExplorer) 是一个跨平台客户端工具，可在其中管理和监视 Batch 帐户。此外，与 Azure 门户 UI 相比，它提供的功能更丰富。  我们为每个支持的应用程序提供了一组定制的池和作业模板，用于轻松创建池和提交作业。
  * 可以使用 Azure 门户来管理和监视 Azure Batch。
* 客户端应用程序插件：
  * 借助提供的插件，可以直接从客户端设计和建模应用程序内部使用 Batch 渲染。 插件主要调用 Batch Explorer 应用程序，并提供有关当前 3D 模型的上下文信息。
  * 可使用以下插件：
    * [Azure Batch for Maya](https://github.com/Azure/azure-batch-maya)
    * [3ds Max](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/3ds-max)
    * [Blender](https://github.com/Azure/azure-batch-rendering/tree/master/plugins/blender)

## <a name="getting-started-with-azure-batch-rendering"></a>Azure Batch 渲染入门

请参阅以下简介教程来体验 Azure Batch 渲染：

* [使用 Batch Explorer 渲染 Blender 场景](https://docs.microsoft.com/azure/batch/tutorial-rendering-batchexplorer-blender)
* [使用 Batch CLI 渲染 Autodesk 3ds Max 场景](https://docs.microsoft.com/azure/batch/tutorial-rendering-cli)

## <a name="next-steps"></a>后续步骤

在[此文](https://docs.microsoft.com/azure/batch/batch-rendering-applications)中查看 Azure 市场 VM 映像包含的渲染应用程序和版本列表。