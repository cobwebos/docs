---
title: 将突发节点添加到 HPC Pack 群集 | Microsoft Docs
description: 了解如何添加云服务中运行的辅助角色实例在 Azure 中按需扩展 HPC Pack 群集
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: eee9183321f21676271c8a9c7e023c80c4daf554
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>在 Azure 中将按需“突发”节点添加到 HPC Pack 群集
如果在 Azure 中设置了 [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) 群集，则可能希望有一种方法能够快速增加/减少群集容量，而无需维护一组预配置的计算节点 VM。 本文介绍了如何按需将“突发”节点（云服务中运行的辅助角色实例）作为计算资源添加到 Azure 中的头节点。 

> [!IMPORTANT] 
> Azure 提供两个不同的部署模型用于创建和处理资源：[资源管理器和经典模型](../../../resource-manager-deployment-model.md)。 本文介绍如何使用经典部署模型。 Microsoft 建议大多数新部署使用资源管理器模型。
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![突发节点][burst]

本文中的步骤有助于将 Azure 节点快速添加到基于云的 HPC Pack 头节点 VM，进行测试或概念证明部署。 这些高级步骤与“迸发到 Azure”的步骤相同，用于为本地 HPC Pack 群集添加云计算能力。 有关教程，请参阅[使用 Microsoft HPC Pack 设置混合计算群集](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)。 有关生产部署的详细指南和注意事项，请参阅[使用 Microsoft HPC Pack 迸发到 Azure](https://technet.microsoft.com/library/gg481749.aspx)。

## <a name="prerequisites"></a>先决条件
* **Azure VM 中部署的 HPC Pack 头节点** - 可使用独立头节点或较大群集中的头节点。 若要创建独立头节点，请参阅[在 Azure VM 中部署 HPC Pack 头节点](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 有关 HPC Pack 群集的自动部署选项，请参阅[在 Azure 中使用 Microsoft HPC Pack 创建和管理 Windows HPC 群集时可用的选项](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。
  
  > [!TIP]
  > 如果使用 [HPC Pack IaaS 部署脚本](hpcpack-cluster-powershell-script.md)在 Azure 中创建群集，则可以在自动化部署中包含 Azure 突发节点。 请参阅该文中的示例。
  > 
  > 
* **Azure 订阅** - 若要添加 Azure 节点，可选择部署头节点 VM 时所用的相同订阅，还可选用一个或多个不同订阅。
* **内核配额** - 可能需要增加核心配额，尤其是在选择部署具有多核大小的多个 Azure 节点时。 若要增加配额，可免费[建立联机客户支持请求](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)。

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>步骤 1：为 Azure 节点创建云服务和存储帐户
使用 Azure 门户或等效工具来配置 Azure 节点部署时所需的以下资源：

* 新的 Azure 云服务（经典）
* 新的 Azure 存储帐户（经典）

> [!NOTE]
> 请勿重复使用订阅中的现有云服务。 
> 
> 

**注意事项**

* 为计划创建的各个 Azure 节点模板配置一个单独的云服务。 但是，多个节点模板可以使用相同的存储帐户。
* 建议在同一 Azure 区域中查找用于部署的云服务和存储帐户。

## <a name="step-2-configure-an-azure-management-certificate"></a>步骤 2：配置 Azure 管理证书
要将 Azure 节点添加为计算资源，头节点上必须有管理证书，并必须将相应证书上传到用于部署的 Azure 订阅。

对于此案例，可以选择 HPC Pack 在头节点上自动安装和配置的**默认 HPC Azure 管理证书**。 此证书对进行测试和概念证明部署很有用。 要使用此证书，请将文件 C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer 从头节点 VM 上传到订阅。 要在 [Azure 门户](https://portal.azure.com)中上传证书：

1. 单击“订阅” > your_subscription_name。

2. 单击“管理证书” > “上传”。

有关配置管理证书的其他选项，请参阅[为 Azure 突发部署配置 Azure 管理证书的方案](http://technet.microsoft.com/library/gg481759.aspx)。

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>步骤 3：向群集部署 Azure 节点
本方案中添加和启动 Azure 节点的步骤通常与本地头节点中使用的步骤相同。 有关详细信息，请参阅[使用 Microsoft HPC Pack 部署 Azure 节点的步骤](https://technet.microsoft.com/library/gg481758.aspx)中的以下部分：

* 创建 Azure 节点模板
* 将 Azure 节点添加到 Windows HPC 群集
* 启动（设置）Azure 节点

添加和启动节点后，它们就已准备好用于运行群集作业。

如果部署 Azure 节点时遇到问题，请参阅[排除使用 Microsoft HPC Pack 部署 Azure 节点时发生的故障](http://technet.microsoft.com/library/jj159097.aspx)。

## <a name="next-steps"></a>后续步骤
* 若要对突发节点使用计算密集型实例大小，请参阅[高性能计算 VM 大小](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)中的注意事项。
* 如果想根据群集工作负荷自动扩展或收缩 Azure 计算资源，请参阅 [Automatically grow and shrink Azure compute resources in an HPC Pack cluster](hpcpack-cluster-node-autogrowshrink.md)（自动扩展和收缩 HPC Pack 群集中的 Azure 计算资源）。

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
