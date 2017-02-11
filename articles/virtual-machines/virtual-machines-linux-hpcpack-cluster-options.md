---
title: "云中的 Linux HPC Pack 群集选项 | Microsoft Docs"
description: "了解使用 Microsoft HPC Pack 在 Azure 云中创建和管理 Linux 高性能计算 (HPC) 群集时可用的选项"
services: virtual-machines-linux,cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: ac60624e-aefa-40c3-8bc1-ef6d5c0ef1a2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/26/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 38480e52de891a3432eefadff16a057a864817de


---
# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>使用 HPC Pack 在 Azure 中为 Linux 工作负荷创建和管理 HPC 群集的选项
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

这篇文章重点介绍使用 HPC Pack 运行 Linux 工作负荷的选项。 还有用于运行[具有 HPC Pack 的 Windows HPC 工作负荷](virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)的选项。

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>在 Azure VM 中运行 HPC Pack 群集
### <a name="azure-templates"></a>Azure 模板
* （应用商店）[Linux 工作负荷的 HPC Pack 群集](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)
* （快速入门）[使用 Linux 计算节点创建 HPC 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)

### <a name="powershell-deployment-script"></a>PowerShell 部署脚本
* [使用 HPC Pack IaaS 部署脚本创建 Linux HPC 群集](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="tutorials"></a>教程
* [教程：Azure 的 HPC Pack 群集中的 Linux 计算节点入门](virtual-machines-linux-classic-hpcpack-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [教程：在 Azure 中的 Linux 计算节点上使用 Microsoft HPC Pack 运行 NAMD](virtual-machines-linux-classic-hpcpack-cluster-namd.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [教程：在 Azure 中的 Linux RDMA 群集上运行 OpenFOAM 和 Microsoft HPC Pack](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [教程：在 Azure 中的 Linux RDMA 群集上运行 STAR-CCM+ 和 Microsoft HPC Pack](virtual-machines-linux-classic-hpcpack-cluster-starccm.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)

### <a name="cluster-management"></a>群集管理
* [将作业提交到 Azure 中的 HPC Pack 群集](virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [HPC Pack 中的作业管理](https://technet.microsoft.com/library/jj899585.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>为 MPI 工作负荷创建 RDMA 群集
* [教程：在 Azure 中的 Linux RDMA 群集上运行 OpenFOAM 和 Microsoft HPC Pack](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [设置 Linux RDMA 群集以运行 MPI 应用程序](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)




<!--HONumber=Nov16_HO3-->


