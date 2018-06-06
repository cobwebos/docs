---
title: include 文件
description: include 文件
services: virtual-machines-windows, azure-resource-manager
author: genlin
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/14/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: f403e060859df6d1de96a3c0d478d57df2677eee
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "31531049"
---
以下是造成分配请求被固定的常见分配案例。 我们会在本文稍后深入探讨每一个案例。

- 重设 VM 大小或向现有云服务添加 VM 或角色实例
- 重新启动部分停止（已解除分配）的 VM
- 重新启动完全停止（已解除分配）的 VM
- 过渡和生产环境部署（仅适用于平台即服务）
- 地缘组（VM 或服务邻近性）
- 基于地缘组的虚拟网络

收到分配错误时，请确认所列出的任何方案是否适用于此错误。 使用 Azure 平台返回的分配错误来识别对应的案例。 如果请求已固定，请尝试删除一些固定约束，向更多群集展现请求，以增加分配成功的机会。
一般情况下，如果错误未表示“不支持请求的 VM 大小”，则始终可以稍后重试。 这是因为群集中已释放足够资源，可以满足你的请求。 如果问题在于不支持所请求的 VM 大小，请尝试使用不同的 VM 大小。 否则，唯一的做法就是删除固定约束。

有两个常见的故障案例与地缘组有关。 在过去，地缘组用于支持 VM 和服务实例的邻近性，或用于支持创建虚拟网络。 在引进区域虚拟网络之后，创建虚拟网络已不再需要地缘组。 由于 Azure 基础结构中的网络延迟时间缩短，原本建议使用地缘组来支持 VM 或服务邻近性的情况已有所改变。

下图显示（固定的）分配案例的分类。 

![固定分配分类](./media/virtual-machines-common-allocation-failure/Allocation3.png)

## <a name="resize-a-vm-or-add-vms-or-role-instances-to-an-existing-cloud-service"></a>重设 VM 大小或向现有云服务添加 VM 或角色实例
**错误**

Upgrade_VMSizeNotSupported 或 GeneralError

**群集固定的原因**

必须在托管现有云服务的原始群集上，尝试请求重设 VM 大小或向现有云服务添加 VM 或角色实例。 创建新的云服务可让 Azure 平台查找其他有可用资源或支持所请求的 VM 大小的群集。

**解决方法**

如果错误是 Upgrade_VMSizeNotSupported*，请尝试使用不同的 VM 大小。 如果使用不同的 VM 大小不可行，但可接受使用不同的虚拟 IP 地址 (VIP)，则创建新的云服务来托管新的 VM，并将新的云服务添加到运行现有 VM 的区域虚拟网络中。 如果现有的云服务未使用区域虚拟网络，用户仍然可以为新的云服务创建新的虚拟网络，然后[将现有虚拟网络连接到新的虚拟网络](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)。 详细了解[区域虚拟网络](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)。

如果错误是 GeneralError*，很可能是因为群集虽然支持资源的类型（例如特定的 VM 大小），但群集目前没有可用的资源。 类似上述，通过创建新的云服务（请注意，新的云服务必须使用不同的 VIP）尝试添加所需的计算资源，并使用区域虚拟网络连接云服务。

## <a name="restart-partially-stopped-deallocated-vms"></a>重新启动部分停止（已解除分配）的 VM
**错误**

GeneralError*

**群集固定的原因**

部分解除分配表示已停止（解除分配）云服务中的一或多个 VM，但不是全部。 停止（解除分配）VM 时会释放相关联的资源。 因此，重新启动已停止（解除分配）的 VM 是一项新的分配请求。 重新启动已部分解除分配的云服务中的 VM 相当于向现有云服务添加 VM。 必须在托管现有云服务的原始群集上尝试发出分配请求。 创建不同云服务可让 Azure 平台查找其他有可用资源或支持所请求的 VM 大小的群集。

**解决方法**

如果可接受使用不同的 VIP，请删除已停止（解除分配）的 VM（但保留相关联的磁盘），并通过不同的云服务加回 VM。 使用区域虚拟网络连接云服务：

* 如果现有的云服务使用区域虚拟网络，只要将新的云服务添加到相同的虚拟网络即可。
* 如果现有的云服务未使用区域虚拟网络，请为新的云服务创建新的虚拟网络，然后[将现有虚拟网络连接到新的虚拟网络](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)。 详细了解[区域虚拟网络](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)。

## <a name="restart-fully-stopped-deallocated-vms"></a>重新启动完全停止（已解除分配）的 VM
**错误**

GeneralError*

**群集固定的原因**

完全解除分配表示已从云服务停止（解除分配）所有 VM。 必须在托管云服务的原始群集上尝试发出分配请求来重新启动这些 VM。 创建新的云服务可让 Azure 平台查找其他有可用资源或支持所请求的 VM 大小的群集。

**解决方法**

如果可接受使用不同的 VIP，请删除已停止（解除分配）的原始 VM（但保留相关联的磁盘），并删除对应的云服务（停止（解除分配）VM 时就已释放相关联的计算资源）。 创建新的云服务来加回 VM。

## <a name="stagingproduction-deployments-platform-as-a-service-only"></a>过渡/生产环境部署（仅适用于平台即服务）
**错误**

New_General\* 或 New_VMSizeNotSupported\*

**群集固定的原因**

云服务的过渡环境部署和生产环境部署托管于同一个群集中。 添加第二个部署时，会在托管第一个部署的相同群集中尝试发出对应的分配请求。

**解决方法**

删除第一个部署和原始的云服务，并重新部署云服务。 此操作可能将第一个部署安排到有足够可用资源可满足这两个部署的群集，或安排到支持所请求 VM 大小的群集。

## <a name="affinity-group-vmservice-proximity"></a>地缘组（VM/服务邻近性）
**错误**

New_General\* 或 New_VMSizeNotSupported\*

**群集固定的原因**

任何分配给地缘组的计算资源都绑定到一个群集。 该地缘组中新的计算资源请求将于托管现有资源的相同群集中尝试发出。 无论是通过新的云服务还是现有的云服务创建新资源，都是如此。

**解决方法**

如果不一定需要地缘组，请不要使用地缘组或尝试将计算资源分组为多个地缘组。

## <a name="affinity-group-based-virtual-network"></a>基于地缘组的虚拟网络
**错误**

New_General\* 或 New_VMSizeNotSupported\*

**群集固定的原因**

在引入区域虚拟网络之前，必须先将虚拟网络与地缘组进行关联。 这样，便会根据上一部分“分配案例：地缘组：（VM/服务邻近性）”中所述的相同约束，绑定已放入地缘组中的计算资源。 计算资源已绑定到一个群集。

**解决方法**

如果不需要地缘组，请为要添加的新资源创建新的区域虚拟网络，然后[将现有虚拟网络连接到新的虚拟网络](https://azure.microsoft.com/blog/vnet-to-vnet-connecting-virtual-networks-in-azure-across-different-regions/)。 详细了解[区域虚拟网络](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/)。

此外，也可以[将基于地缘组的虚拟网络迁移到区域虚拟网络](https://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/)，然后重新添加所需的资源。
