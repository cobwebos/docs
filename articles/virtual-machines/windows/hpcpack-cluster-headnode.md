---
title: 在 Azure VM 中创建 HPC Pack 头节点 | Microsoft Docs
description: 了解如何使用 Azure 门户和 Resource Manager 部署模型在 Azure VM 中创建 Microsoft HPC Pack 2012 R2 头节点。
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: acd4cd44dd35a5b1755d9456f683076567d62165
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915253"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>在 Azure VM 中使用 Marketplace 映像创建 HPC Pack 群集的头节点
使用 Azure Marketplace 和 Azure 门户中的 [Microsoft HPC Pack 2012 R2 虚拟机映像](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)创建 HPC 群集的头节点。 此 HPC Pack VM 映像基于预安装了 HPC Pack 2012 R2 Update 3 的 Windows Server 2012 R2 Datacenter。 使用此头节点在 Azure 中进行 HPC Pack 的概念证明部署。 然后，可以向该群集添加计算节点，以运行 HPC 工作负荷。

> [!TIP]
> 若要在 Azure 中部署完整的 HPC Pack 2012 R2 群集（包括头节点和计算节点），建议使用自动化方法。 选项包括 [HPC Pack IaaS 部署脚本](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)和 Resource Manager 模板，如[适用于 Windows 工作负荷的 HPC Pack 群集](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)。 Resource Manager 模板也可用于 [Microsoft HPC Pack 2016 群集](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates)。 
> 
> 

## <a name="planning-considerations"></a>规划注意事项
如下图所示，可在 Azure 虚拟网络的 Active Directory 域中部署 HPC Pack 头节点。

![HPC Pack 头节点][headnode]

* **Active Directory 域**：必须先将 HPC Pack 2012 R2 头节点加入到 Azure 中的 Active Directory 域，再在 VM 上启动 HPC 服务。 如本文所示，要进行概念证明部署，可以先将为头节点创建的 VM 提升为域控制器，然后再启动 HPC 服务。 另一种方法是在 Azure 中部署单独的域控制器和林，并将头节点 VM 加入到该域林。

* **部署模型**：对于大多数新部署，Microsoft 建议使用 Resource Manager 部署模型。 本文假定用户使用这种部署模型。

* **Azure 虚拟网络**：通过 Resource Manager 部署模型部署头节点时，可指定或创建 Azure 虚拟网络。 如需将头节点加入现有的 Active Directory 域，可使用虚拟网络。 在以后还需使用它将计算节点 VM 添加到群集。

## <a name="steps-to-create-the-head-node"></a>创建头节点的步骤
以下是概略性步骤，说明了如何通过 Azure 门户使用 Resource Manager 部署模型为 HPC Pack 头节点创建 Azure VM。 

1. 如果希望使用单独的域控制器 VM 在 Azure 中创建新的 Active Directory 林，其中一种方法是使用 [Resource Manager 模板](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc)。 对于简单的概念验证部署，可以忽略此步骤，将头节点 VM 本身配置为域控制器。 此选项在本文后面介绍。
2. 在 Azure Marketplace 的[“Windows Server 2012 R2 上的 HPC Pack 2012 R2”页](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)上，单击“创建虚拟机”。 
3. 在门户的“Windows Server 2012 R2 上的 HPC Pack 2012 R2”页上，选择“Resource Manager”部署模型，并单击“创建”。
   
    ![HPC Pack 映像][marketplace]
4. 使用门户配置设置并创建 VM。 如果不熟悉 Azure，请按照[在 Azure 门户中创建 Windows 虚拟机](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)教程中的说明操作。 若要进行概念证明部署，通常可以接受默认或推荐的设置。
   
   > [!NOTE]
   > 如果希望将头节点加入到 Azure 中的现有 Active Directory 域，请确保在创建 VM 时为该域指定了虚拟网络。
   > 
   > 
5. 创建 VM 并运行 VM 之后，通过远程桌面[连接到 VM](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。 
6. 选择以下任一选项，将 VM 加入到 Active Directory 域林：
   
   * 如果使用现有的域林在 Azure 虚拟网络中创建了 VM，请使用标准的 Server Manager 或 Windows PowerShell 工具将 VM 加入到该林。 然后重启。
   * 如果在新的虚拟网络中创建 VM（未使用现有域林），则将该 VM 提升为域控制器。 使用标准步骤安装和配置头节点上的 Active Directory 域服务角色。 有关详细步骤，请参阅[安装新的 Windows Server 2012 Active Directory 林](https://technet.microsoft.com/library/jj574166.aspx)。
7. 在 VM 运行并加入到 Active Directory 林后启动 HPC Pack 服务，如下所示：
   
    a. 使用一个属于本地管理员组的域帐户连接到头节点 VM。 例如，可以使用创建头节点 VM 时设置的管理员帐户。
   
    b. 对于默认头节点配置，以管理员身份启动 Windows PowerShell 并键入以下命令：
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    HPC Pack 服务启动可能需要几分钟时间。
   
    对于其他头节点配置选项，请键入 `get-help HPCHNPrepare.ps1`。

## <a name="next-steps"></a>后续步骤
* 现在即可使用 HPC Pack 群集的头节点。 例如，启动 HPC 群集管理器，并完成[部署待办事项列表](https://technet.microsoft.com/library/jj884141.aspx)。
* 若要按需提高群集计算容量，可在云服务中添加 [Azure 迸发节点](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)。 
* 尝试在群集上运行测试工作负荷。 例如，请参阅 HPC Pack [入门指南](https://technet.microsoft.com/library/jj884144)。

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
