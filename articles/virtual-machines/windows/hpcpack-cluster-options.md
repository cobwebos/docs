---
title: Azure 中的 Windows HPC Pack 群集选项 | Microsoft Docs
description: 了解使用 Microsoft HPC Pack 在 Azure 云中创建和管理 Windows 高性能计算 (HPC) 群集时可用的选项
services: virtual-machines-windows,cloud-services,batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management,hpc-pack
ms.assetid: 02c5566d-2129-483c-9ecf-0d61030442d7
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 05/14/2018
ms.author: danlep
ms.openlocfilehash: 1232228d5e2fcd05f41a096a933072dffcca2119
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34165751"
---
# <a name="options-with-hpc-pack-to-create-and-manage-a-cluster-for-windows-hpc-workloads-in-azure"></a>使用 HPC Pack 为 Azure 中的 Windows HPC 工作负荷创建和管理群集的选项
[!INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

本文重点介绍用于创建 HPC Pack 群集以运行 Windows 工作负载的 Azure 选项。 此外，还介绍用于创建 HPC Pack 群集以运行 [Linux HPC 工作负荷](../linux/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)的选项。


## <a name="hpc-pack-cluster-in-azure-vms-and-vm-scale-sets"></a>Azure VM 和 VM 规模集中的 HPC Pack 群集
### <a name="azure-resource-manager-templates"></a>Azure 资源管理器模板
* (GitHub) [HPC Pack 2016 Update 1 群集模板](https://github.com/MsHpcPack/HPCPack2016)
* (GitHub) [HPC Pack 2012 R2 群集模板](https://github.com/MsHpcPack/HPCPack2012R2)
* （快速入门）[创建 HPC Pack 2012 R2 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)
* （快速入门）[使用自定义计算节点映像创建 HPC Pack 2012 R2 群集](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Azure VM 映像
如果你想要在 Azure 中构建自己的 HPC Pack 群集，请浏览 [Azure Marketplace 中的 HPC Pack 映像](https://azuremarketplace.microsoft.com/en-us/marketplace/apps?page=1&search=%22HPC%20%20Pack%22)。


### <a name="tutorials"></a>教程
* [教程：在 Azure 中部署 HPC Pack 2016 群集](hpcpack-2016-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [使用 Azure Active Directory 在 Azure 中管理 HPC Pack 2016 群集](hpcpack-cluster-active-directory.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="hpc-pack-2012-r2-cluster-deployment-in-the-classic-deployment-model"></a>经典部署模型中的 HPC Pack 2012 R2 群集部署
* [使用 HPC Pack IaaS 部署脚本创建 HPC 群集](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [教程：开始使用 Azure 中的 HPC Pack 群集运行 Excel 和 SOA 工作负荷](excel-cluster-hpcpack.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [在 Azure 中管理 HPC Pack 群集的计算节点](classic/hpcpack-cluster-node-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [增加和减少 HPC Pack 群集中的 Azure 计算资源](classic/hpcpack-cluster-node-autogrowshrink.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [使用 HPC Pack 设置一个运行 MPI 应用程序的 Windows RDMA 群集](classic/hpcpack-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="burst-to-azure-from-hpc-pack-2012-r2"></a>从 HPC Pack 2012 R2 迸发到 Azure
* [使用 Microsoft HPC Pack 迸发到 Azure 辅助角色实例](https://technet.microsoft.com/library/gg481749.aspx)
* [使用 HPC Pack 迸发到 Azure Batch](https://technet.microsoft.com/library/mt612877.aspx)
* [教程：使用 Azure 中的 HPC Pack 设置混合群集](../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

## <a name="job-submission"></a>作业提交

* [将作业提交到 Azure 中的 HPC Pack 群集](hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)






