---
title: 在 Windows Server 和 Linux 上创建 Azure Service Fabric 群集 | Microsoft 文档
description: Service Fabric 群集会在 Windows Server 或 Linux 上运行，这意味着可以在能够运行 Windows Server 和 Linux 的任何位置部署和承载 Service Fabric 应用程序。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/28/2018
ms.author: dekapur
ms.openlocfilehash: 3d427d99f6919991c29fc5947ebe0082670a1cc1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/16/2018
---
# <a name="create-service-fabric-clusters-on-windows-server-or-linux"></a>在 Windows Server 或 Linux 上创建 Service Fabric 群集
Azure Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 群集中的计算机或 VM 称为群集节点。 群集可以扩展到数千个节点。 如果向群集添加新节点，Service Fabric 会在新增加的节点间重新平衡服务分区副本和实例。 应用程序总体性能提高，访问内存的争用减少。 如果没有高效使用群集中的节点，可以减少群集中节点的数量。 Service Fabric 会再次在减少的节点间重新平衡分区副本和实例以更加充分利用每个节点上的硬件。

使用 Service Fabric 可在运行 Windows Server 或 Linux 的任何 VM 或计算机上创建 Service Fabric 群集。 这意味着，可以在包含一组互连 Windows Server 或 Linux 计算机（无论是本地计算机、Microsoft Azure 计算机还是任何云提供商的计算机）的任何环境中部署和运行 Service Fabric 应用程序。

## <a name="create-service-fabric-clusters-on-azure"></a>在 Azure 上创建 Service Fabric 群集
通过资源模型模板或 [Azure 门户](https://portal.azure.com)在 Azure 上创建群集。 有关详细信息，请参阅[使用 Resource Manager 模板创建 Service Fabric 群集](service-fabric-cluster-creation-via-arm.md)或[在 Azure 门户中创建 Service Fabric 群集](service-fabric-cluster-creation-via-portal.md)。

## <a name="supported-operating-systems-for-clusters-on-azure"></a>Azure 上支持的群集操作系统
可以在运行以下操作系统的虚拟机上创建群集：

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 1709
* Linux Ubuntu 16.04

> [!NOTE]
> 如果决定要在 Windows Server 1709 上部署 Service Fabric，请注意，(1) 它不是长期服务分支，因此你可能必须在将来迁移版本 (2) 如果部署容器，基于 Windows Server 2016 构建的容器不适用于 Windows Server 1709，反之亦然（你必须重新生成它们才能对其进行部署）。
>

## <a name="create-service-fabric-standalone-clusters-on-premises-or-with-any-cloud-provider"></a>在本地或者与任何云提供商合作创建 Service Fabric 独立群集
Service Fabric 提供一个安装包，用于在本地或者与任何云提供商合作创建独立的 Service Fabric 群集。

有关在 Windows Server 上设置独立 Service Fabric 群集的详细信息，请参阅[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)

  > [!NOTE]
  > Linux 目前不支持独立群集。 开发单机和 Azure Linux 多机群集支持 Linux。
  >

### <a name="any-cloud-deployments-vs-on-premises-deployments"></a>任何云部署与本地部署
用于在本地创建 Service Fabric 群集的过程类似于在具有一组 VM 的任何所选云上创建群集的过程。 预配 VM 的初始步骤由所使用的云提供程序或本地环境进行控制。 具有一组在它们之间启用了网络连接的 VM 之后，随后用于设置 Service Fabric 包、编辑群集设置以及运行群集创建和管理脚本的步骤相同。 这可以在选择面向新宿主环境时，确保操作和管理 Service Fabric 群集的知识和经验可以转移。

### <a name="benefits-of-creating-standalone-service-fabric-clusters"></a>创建独立 Service Fabric 群集的优点
* 可以自由选择任何云提供商来托管群集。
* Service Fabric 应用程序在编写之后，可以在多个宿主环境中运行，只需进行最小程度的更改，甚至无需更改。
* 构建 Service Fabric 应用程序的知识可从一个宿主环境转移到另一个环境。
* 运行和管理 Service Fabric 群集的操作经验可从一个环境转移到另一个环境。
* 广泛的客户范围不会受宿主环境约束的限制。
* 存在针对大范围中断的额外一层可靠性和保护，因为可以在数据中心或云提供商中断时会服务转移到其他部署环境。

## <a name="supported-operating-systems-for-standalone-clusters"></a>独立群集支持的操作系统
可以在运行以下操作系统的 VM 或计算机上创建群集（目前尚不支持 Linux）：

* Windows Server 2012 R2
* Windows Server 2016 

## <a name="advantages-of-service-fabric-clusters-on-azure-over-standalone-service-fabric-clusters-created-on-premises"></a>与在本地创建的独立 Service Fabric 群集相比 Azure 上的 Service Fabric 群集的优势
在 Azure 上运行 Service Fabric 群集相对于本地运行具有一些优势，因此，如果对于群集的运行位置没有特定需求，则我们建议在 Azure 上运行它们。 在 Azure 上，我们提供与其他 Azure 功能和服务的集成，这样可使群集的操作和管理更容易且更可靠。

* **Azure 门户：** Azure 门户使群集易于创建和管理。
* **Azure 资源管理器：** 使用 Azure 资源管理器可以单元的形式方便地管理群集使用的所有资源，并简化了成本跟踪和计费。
* **用作 Azure 资源的 Service Fabric 群集** Service Fabric 群集是一种 Azure 资源，因此可以像在 Azure 中对其他资源建模一样为它建模。
* **与 Azure 基础结构集成** Service Fabric 与适用于 OS、网络和其他升级的 Azure 基础结构相协调，以提高应用程序的可用性与可靠性。  
* **诊断：** 在 Azure 中，我们提供与 Azure 诊断和 Log Analytics 的集成。
* **自动缩放：** 对于 Azure 上的群集，我们借助虚拟机缩放集提供内置自动缩放功能。 在本地和其他云环境中，必须构建自己的自动调整规模功能或使用 Service Fabric 为调整群集规模而公开的 API 来手动调整规模。

## <a name="next-steps"></a>后续步骤

* 在运行 Windows Server 的 VM 或计算机上创建群集：[创建适用于 Windows Server 的 Service Fabric 群集](service-fabric-cluster-creation-for-windows-server.md)
* 在运行 Linux 的 VM 或计算机上创建群集：[创建 Linux 群集](service-fabric-cluster-creation-via-portal.md)
* 了解 [Service Fabric 支持选项](service-fabric-support.md)

