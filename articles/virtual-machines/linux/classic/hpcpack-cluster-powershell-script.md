---
title: 用于部署 Linux HPC 群集的 PowerShell 脚本 | Microsoft Docs
description: 运行 PowerShell 脚本，以在 Azure 虚拟机中部署 Linux HPC Pack 2012 R2 群集
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 73041960-58d3-4ecf-9540-d7e1a612c467
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 66affb47190ba0c6fccaae8e8267b310682aee46
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/05/2018
ms.locfileid: "30841837"
---
# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>使用 HPC Pack IaaS 部署脚本创建 Linux 高性能计算 (HPC) 群集
运行 HPC Pack IaaS 部署 PowerShell 脚本，以便为 Azure 虚拟机中的 Linux 工作负荷部署完整的 HPC Pack 2012 R2 群集。 群集包含一个运行 Windows Server 和 Microsoft HPC Pack 的已加入 Active Directory 的头节点和多个运行 HPC Pack 支持的 Linux 分发之一的计算节点。 如果想要在 Azure 中部署适用于 Windows 工作负荷的 HPC Pack 群集，请参阅 [Create a Windows HPC cluster with the HPC Pack IaaS deployment script](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)（使用 HPC Pack IaaS 部署脚本创建 Windows HPC 群集）。
> [!IMPORTANT] 
> 本文中介绍的 PowerShell 脚本使用经典部署模型在 Azure 中创建 Microsoft HPC Pack 2012 R2 群集。 Microsoft 建议大多数新部署使用资源管理器模型。
> 此外，本文中所述的脚本不支持 HPC Pack 2016。 有关用于 HPC Pack 2012 R2 和 HPC Pack 2016 的资源管理器模板的信息，请参阅 [Azure 中的 HPC Pack 群集部署选项](../hpcpack-cluster-options.md)。


[!INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>示例配置文件
以下配置文件创建域控制器和域林并部署 HPC Pack 群集，其中包含 1 个具有本地数据库的头节点和 10 个 Linux 计算节点。 所有云服务直接在“亚洲东部”位置创建。 Linux 计算节点在 2 个云服务和 2 个存储帐户中创建（即，*MyLnxCNService01* 和 *mylnxstorage01* 中的 *MyLnxCN-0001* 到 *MyLnxCN-0005*，以及 *MyLnxCNService02* 和 *mylnxstorage02* 中的 *MyLnxCN-0006* 到 *MyLnxCN-0010*）。 计算节点是基于 OpenLogic CentOS 7.0 版 Linux 映像创建的。 

将订阅名称以及帐户和服务名称替换为自己的值。

```Xml
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>故障排除
* **“VNet 不存在”错误**。 如果运行 HPC Pack IaaS 部署脚本以便在 Azure 中的一个订阅下同时部署多个群集，那么一个或多个部署可能会失败并显示错误“VNet *VNet\_Name* 不存在”。
  如果发生此错误，请对失败的部署重新运行该脚本。
* **从 Azure 虚拟网络访问 Internet 时出现问题**。 如果使用部署脚本创建具有新域控制器的 HPC Pack 群集，或将头节点 VM 手动提升为域控制器，则将 Azure 虚拟网络中的 VM 连接到 Internet 时可能会遇到问题。 如果已在域控制器上自动配置转发器 DNS 服务器，但此转发器 DNS 服务器未正确解析，则会出现这种情况。
  
    若要解决此问题，请登录到域控制器，删除转发器配置设置或配置一个有效的转发器 DNS 服务器。 为此，请在服务器管理器中单击 **工具** > **DNS** 打开 DNS 管理器，并双击 **转发器** 。

## <a name="next-steps"></a>后续步骤
* 请参阅 [Azure 的 HPC Pack 群集中的 Linux 计算节点入门](hpcpack-cluster.md)，了解有关支持的 Linux 分发、移动数据、以及使用 Linux 计算节点将作业提交到 HPC Pack 群集的信息。
* 有关使用脚本创建群集和运行 Linux HPC 工作负荷的教程，请参阅：
  * [在 Azure 中的 Linux 计算节点上使用 Microsoft HPC Pack 运行 NAMD](hpcpack-cluster-namd.md)
  * [在 Azure 中的 Linux 计算节点上使用 Microsoft HPC Pack 运行 OpenFOAM](hpcpack-cluster-openfoam.md)
  * [在 Azure 中的 Linux 计算节点上使用 Microsoft HPC Pack 运行 STAR-CCM+](hpcpack-cluster-starccm.md)

