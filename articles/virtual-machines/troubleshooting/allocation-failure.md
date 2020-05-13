---
title: 排查 Azure VM 分配失败 | Microsoft Docs
description: 在 Azure 中创建、重启 VM 或重设其大小时排查分配失败
services: virtual-machines
documentationcenter: ''
author: DavidCBerry13
manager: felixwu
editor: ''
tags: top-support-issue,azure-resource-manager,azure-service-management
ms.assetid: 1ef41144-6dd6-4a56-b180-9d8b3d05eae7
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 04/13/2018
ms.author: daberry
ms.openlocfilehash: fdbf07fa51adf8151e80d230734ebe53d36b5390
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124782"
---
# <a name="troubleshoot-allocation-failures-when-you-create-restart-or-resize-vms-in-azure"></a>在 Azure 中创建、重启 VM 或重设其大小时排查分配失败

创建虚拟机 (VM)、重新启动已停止（解除分配）的 VM 和重设 VM 大小时，Microsoft Azure 会为订阅分配计算资源。 我们会不断投入其他基础结构和功能，确保始终提供可用于支持客户需求的所有 VM 类型。 但是，偶尔可能因特定区域中前所未有的 Azure 服务需求增长而遇到资源分配失败的情况。 当尝试在区域中创建或启动 VM，同时 VM 显示以下错误代码和消息时，会出现此问题：

**错误代码**：AllocationFailed 或 ZonalAllocationFailed

**错误消息**：“分配失败。 对于此区域中请求的 VM 大小，我们的容量不够。 在 https:\//aka.ms/allocation-guidance 阅读有关提高分配成功可能性的详细信息”

本文说明一些常见分配故障的原因，并建议可能的补救方法。

如果本文未解决你的 Azure 问题，请访问[MSDN 上的 azure 论坛并 Stack Overflow](https://azure.microsoft.com/support/forums/)。 可以将问题发布到这些论坛上，或者发布到 Twitter 上的 @AzureSupport。 此外，还可以通过在 [Azure 支持](https://azure.microsoft.com/support/options/)站点上选择“获取支持”来发出 Azure 支持请求。

在你首选的 VM 类型在首选区域中提供前，建议遇到部署问题的客户考虑下表中的指南，作为临时解决办法。 

确定最适合你的情况的方案，然后使用对应的建议解决办法重试分配请求，增加分配成功的可能性。 或者，始终可以稍后重试。 这是因为群集、区域 (region) 或区域 (zone) 中已释放足够资源，可以满足你的请求。 


## <a name="resize-a-vm-or-add-vms-to-an-existing-availability-set"></a>重设 VM 大小或向现有可用性集添加 VM

### <a name="cause"></a>原因

必须在托管现有可用性集的原始群集上，尝试请求重设 VM 大小或向现有可用性集添加 VM。 群集支持请求的 VM 大小，但群集当前可能容量不够。 

### <a name="workaround"></a>解决方法

如果 VM 可以属于不同的可用性集，请在不同的可用性集（位于相同区域）中创建 VM。 然后，可以将这个新的 VM 添加到相同的虚拟网络中。

停止（解除分配）同一可用性集中的所有 VM，然后重启每个 VM。
若要停止：单击“资源组”> [资源组] >“资源”> [可用性集] >“虚拟机”> [虚拟机] >“停止”。
所有 VM 都停止后，选中第一个 VM 并单击“启动”。
此步骤可确保运行新的分配尝试，而且可以选择有足够容量的新群集。

## <a name="restart-partially-stopped-deallocated-vms"></a>重启部分停止（已解除分配）的 VM

### <a name="cause"></a>原因

部分解除分配表示已停止（解除分配）可用性集中的一或多个（但不是全部）VM。 解除分配 VM 时会释放相关联的资源。 重新启动已部分解除分配的可用性集相当于向现有可用性集添加 VM。 因此，必须在承载的现有可用性集的容量可能不足的原始群集上尝试分配请求。

### <a name="workaround"></a>解决方法

停止（解除分配）同一可用性集中的所有 VM，然后重启每个 VM。
若要停止：单击“资源组”> [资源组] >“资源”> [可用性集] >“虚拟机”> [虚拟机] >“停止”。
所有 VM 都停止后，选中第一个 VM 并单击“启动”。
这可确保运行新的分配尝试，而且可以选择有足够容量的新群集。

## <a name="restart-fully-stopped-deallocated-vms"></a>重启完全停止（已解除分配）的 VM

### <a name="cause"></a>原因

完全解除分配表示已停止（解除分配）可用性集中的所有 VM。 发出分配请求来重新启动这些 VM 时，会以支持区域内所需大小的所有群集为目标。 根据本文中的建议更改分配请求，并重试请求，以提高分配成功的机会。 

### <a name="workaround"></a>解决方法

如果使用 Dv1、DSv1、Av1、D15v2 或 DS15v2 之类的较旧 VM 系列或大小，请考虑移到较新版本。 对特定的 VM 大小参阅以下建议。
如果没有使用其他 VM 大小的选项，请尝试部署到同一地域中的不同区域。 有关每个区域中可用的 VM 大小的详细信息，请访问 https://aka.ms/azure-regions

如果使用可用性区域，请尝试对所请求 VM 大小有可用容量的区域 (region) 内的其他区域 (zone)。

如果分配请求较大（超过 500 个内核），请参阅下节中的指南，将请求分解为较小的部署。

尝试[重新部署 VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/redeploy-to-new-node-windows)。 重新部署 VM 会将 VM 分配到该区域中的新群集。

## <a name="allocation-failures-for-older-vm-sizes-av1-dv1-dsv1-d15v2-ds15v2-etc"></a>针对较旧 VM 大小（Av1、Dv1、DSv1、D15v2、DS15v2 等）的分配失败

随着我们扩展 Azure 基础结构，会部署旨在支持最新虚拟机类型的更高版本硬件。 某些较旧 VM 系列不在我们最新的基础结构上运行。 为此，客户偶尔可能遇到这些旧版 SKU 的分配失败。 为避免此问题，建议使用旧版虚拟机系列的客户考虑按以下建议迁移至等效的新版 VM：这些 VM 已针对最新的硬件进行优化，具有更高的性价比。 

|旧版 VM 系列/大小|建议使用新版 VM 系列/大小|详细信息|
|----------------------|----------------------------|--------------------|
|Av1 系列|[Av2 系列](../av2-series.md)|https://azure.microsoft.com/blog/new-av2-series-vm-sizes/
|Dv1 或 DSv1 系列（D1 到 D5）|[Dv3 或 DSv3 系列](../dv3-dsv3-series.md)|https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/
|Dv1 或 DSv1 系列（D11 到 D14）|[Ev3 或 ESv3 系列](../ev3-esv3-series.md)|
|D15v2 或 DS15v2|如果你使用资源管理器部署模型以便充分利用更大的 VM 大小，请考虑移动到 D16v3/DS16v3 或 D32v3/DS32v3。 这些为在最新硬件上运行而设计。 如果使用资源管理器部署模型以确保你的 VM 实例独立于单个客户专用的硬件，请考虑移动到新的独立 VM 大小 E64i_v3 或 E64is_v3，它们为在最新硬件上运行而设计。 |https://azure.microsoft.com/blog/new-isolated-vm-sizes-now-available/

## <a name="allocation-failures-for-large-deployments-more-than-500-cores"></a>大型部署（超过 500 个内核）的分配失败

减少请求的 VM 大小的实例数，然后重试部署操作。 此外，对于大型部署，建议评估 [Azure 虚拟机规模集](https://docs.microsoft.com/azure/virtual-machine-scale-sets/)。 VM 实例数可自动增加或减少以响应需求或定义的计划，并且分配成功的可能性更大，因为部署可以分布在多个群集中。 

## <a name="background-information"></a>背景信息
### <a name="how-allocation-works"></a>分配的工作原理
Azure 数据中心的服务器分区成群集。 通常会尝试向多个群集发出分配请求，但分配请求可能带有某些约束，从而强制 Azure 平台只尝试向一个群集发出请求。 在本文中，这种情况称为“固定到群集”。 下图 1 演示了在多个群集中尝试进行一般分配的情况。 图 2 演示了固定到群集 2（因为现有的云服务 CS_1 或可用性集托管于此处）的分配情况。
![分配图](./media/virtual-machines-common-allocation-failure/Allocation1.png)

### <a name="why-allocation-failures-happen"></a>发生分配失败的原因
当分配请求固定到某个群集时，由于可用的资源池较小，很可能找不到可用的资源。 此外，如果分配请求固定到某个群集，但该群集不支持你所请求的资源类型，那么，即使该群集有可用的资源，请求仍会失败。 下图 3 说明由于唯一候选群集没有可用的资源，导致已固定的分配失败的情况。 图 4 说明由于唯一候选群集不支持所请求的 VM 大小（虽然群集有可用的资源），导致已固定的分配失败的情况。

![固定分配故障](./media/virtual-machines-common-allocation-failure/Allocation2.png)


