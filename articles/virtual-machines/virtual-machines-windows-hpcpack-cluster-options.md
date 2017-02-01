---
title: "云中的 Windows HPC Pack 群集选项 | Microsoft Docs"
description: "了解使用 Microsoft HPC Pack 在 Azure 云中创建和管理 Windows 高性能计算 (HPC) 群集时可用的选项"
services: virtual-machines-windows,cloud-services,batch
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 11/17/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 08499c4242fdc59ef932d6b8f2e8442e5cdc55b2
ms.openlocfilehash: ca27dadb7b6e18dd5d81e89564059928ffe7dac6


---
# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>使用 HPC Pack 来创建和管理在 Azure 中的 Windows HPC 群集的选项
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

本文重点介绍用于创建 HPC Pack 群集以便运行 Windows 工作负荷的选项。 此外，还介绍用于创建 HPC Pack 群集以运行 [Linux HPC 工作负荷](virtual-machines-linux-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的选项。


## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>在 Azure VM 中运行 HPC Pack 群集
### <a name="azure-templates"></a>Azure 模板
* (GitHub) [HPC Pack 2016 群集模板](https://github.com/MsHpcPack/HPCPack2016)
* （应用商店）[Windows 工作负荷的 HPC Pack 群集](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)
* （应用商店）[Excel 工作负荷的 HPC Pack 群集](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)
* （快速入门）[创建 HPC 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* （快速入门）[使用自定义计算节点映像创建 HPC 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM 映像
* [Windows Server 2012 R2 上的 HPC Pack](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)
* [Windows Server 2012 R2 上的 HPC Pack 计算节点](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)
* [Windows Server 2012 R2 上包含 Excel 的 HPC Pack 计算节点](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)

### <a name="powershell-deployment-script"></a>PowerShell 部署脚本
* [使用 HPC Pack IaaS 部署脚本创建 HPC 群集](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

### <a name="tutorials"></a>教程
* [教程：在 Azure 中部署 HPC Pack 2016 群集](virtual-machines-windows-hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [教程：开始使用 Azure 中的 HPC Pack 群集运行 Excel 和 SOA 工作负荷](virtual-machines-windows-excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="manual-deployment-with-the-azure-portal"></a>使用 Azure 门户手动部署
* [在 Azure VM 中设置 HPC Pack 群集的头节点](virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="cluster-management"></a>群集管理
* [在 Azure 中管理 HPC Pack 群集的计算节点](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [增加和减少 HPC Pack 群集中的 Azure 计算资源](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [将作业提交到 Azure 中的 HPC Pack 群集](virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack 中的作业管理](https://technet.microsoft.com/library/jj899585.aspx)
* [使用 Azure Active Directory 在 Azure 中管理 HPC Pack 群集](virtual-machines-windows-hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>将辅助角色节点添加到 HPC Pack 群集
* [使用 HPC Pack 迸发到 Azure 辅助角色实例](https://technet.microsoft.com/library/gg481749.aspx)
* [教程：使用 Azure 中的 HPC Pack 设置混合群集](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)
* [将 Azure“突发”节点添加到 Azure 中的 HPC Pack 头节点](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="integrate-with-azure-batch"></a>与 Azure 批处理集成
* [使用 HPC Pack 迸发到 Azure 批处理](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>为 MPI 工作负荷创建 RDMA 群集
* [使用 HPC Pack 设置一个运行 MPI 应用程序的 Windows RDMA 群集](virtual-machines-windows-classic-hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)




<!--HONumber=Dec16_HO2-->


