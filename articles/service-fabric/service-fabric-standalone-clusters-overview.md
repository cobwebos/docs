---
title: 独立 Service Fabric 群集概述 | Microsoft Docs
description: Service Fabric 群集会在 Windows Server 或 Linux 上运行，这意味着可以在能够运行 Windows Server 和 Linux 的任何位置部署和承载 Service Fabric 应用程序。
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: dekapur
ms.openlocfilehash: 5997526098980220014d9bb2d47efe6c9aedee3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66752333"
---
# <a name="overview-of-service-fabric-standalone-clusters"></a>Service Fabric 独立群集概述

Service Fabric 群集是一组通过网络连接在一起的虚拟机或物理计算机，微服务会在其中部署和管理。 群集中的计算机或 VM 称为群集节点。 群集可以扩展到数千个节点。 如果向群集添加新节点，Service Fabric 会在新增加的节点间重新平衡服务分区副本和实例。 应用程序总体性能提高，访问内存的争用减少。 如果没有高效使用群集中的节点，可以减少群集中节点的数量。 Service Fabric 会再次在减少的节点间重新平衡分区副本和实例以更加充分利用每个节点上的硬件。

节点类型定义群集中一组节点的大小、数量和属性。 然后，每个节点类型可以独立扩展或缩减、打开不同的端口集，并可以有不同的容量指标。 节点类型用于定义一组群集节点（如“前端”或“后端”）的角色。 群集可以有多个节点类型，但主节点类型必须至少有 5 个 VM 供群集用于生产（或至少有 3 个 VM 用于测试群集）。 [Service Fabric 系统服务](service-fabric-technical-overview.md#system-services)位于主节点类型的节点上。

用于在本地创建 Service Fabric 群集的过程类似于在具有一组 VM 的任何所选云上创建群集的过程。 预配 VM 的初始步骤由所使用的云提供程序或本地环境进行控制。 具有一组在它们之间启用了网络连接的 VM 之后，随后用于设置 Service Fabric 包、编辑群集设置以及运行群集创建和管理脚本的步骤相同。 这可以在选择面向新宿主环境时，确保操作和管理 Service Fabric 群集的知识和经验可以转移。

## <a name="cluster-security"></a>群集安全性
Service Fabric 群集是你拥有的资源。  你应保护群集，防止未经授权的用户与其连接。 当在群集上运行生产工作负荷时，安全的群集环境尤为重要。

### <a name="node-to-node-security"></a>节点到节点安全性
节点到节点安全性可保护群集中 VM 或计算机之间的通信。 这种安全性方案确保只有已获授权加入群集的计算机可以参与到托管群集中的应用程序和服务。 Service Fabric 使用 X.509 证书保护群集，提供应用程序安全功能。  需要使用群集证书来保护群集流量并提供群集和服务器身份验证。  自签名证书可用于测试群集，但在保护生产群集时应使用来自受信任证书颁发机构的证书。

也可为 Windows 独立群集启用 Windows 安全性。 如果有 Windows Server 2012 R2 和 Windows Active Directory，建议结合使用 Windows 安全性和组托管服务帐户。 否则，可以结合使用 Windows 安全性和 Windows 帐户。

有关详细信息，请阅读[节点到节点安全性](service-fabric-cluster-security.md#node-to-node-security)

### <a name="client-to-node-security"></a>客户端到节点安全性
客户端到节点安全性对客户端进行身份验证，并保护客户端与群集中单个节点之间的通信。 这种类型的安全性确保只有已获授权的用户可以访问群集与群集上部署的应用程序。 客户端通过其 X.509 证书安全凭据进行唯一标识。 可以使用任意数量的可选客户端证书向群集验证管理员或用户客户端的身份。

除客户端证书外，还可以将 Azure Active Directory 配置为向群集验证客户端身份。

有关详细信息，请阅读[客户端到节点安全性](service-fabric-cluster-security.md#client-to-node-security)

### <a name="role-based-access-control-rbac"></a>基于角色的访问控制 (RBAC)
Service Fabric 还支持使用访问控制限制对不同用户组的某些群集操作的访问。 这就使得群集更加安全。 连接到群集的客户端支持两种访问控制类型：管理员角色和用户角色。  

有关详细信息，请阅读[基于角色的访问控制 (RBAC)](service-fabric-cluster-security.md#role-based-access-control-rbac)。

## <a name="scaling"></a>缩放

应用程序的需求会不断变化。 可能需要增加群集资源来满足更多的应用程序工作负荷或网络流量，或者在需求下降时减少群集资源。 创建 Service Fabric 群集后，可以群集横向缩放（更改节点数）或纵向缩放（更改节点资源）该群集。 随时可以缩放群集，即使该群集上正在运行工作负荷。 在缩放群集的同时，应用程序也会随之自动缩放。

有关详细信息，请阅读[缩放独立群集](service-fabric-cluster-scaling-standalone.md)。

## <a name="upgrading"></a>正在升级

独立群集是你完全拥有的资源。 负责修补基础 OS 和启动结构升级。 当 Microsoft 发布新版本时，可以将群集设置为接收自动运行时升级，或选择所需的受支持运行时版本。 除了结构升级，还可以修补 OS 和更新群集配置（例如证书或应用程序端口）。 

有关详细信息，请阅读[升级独立群集](service-fabric-cluster-upgrade-standalone.md)。

## <a name="supported-operating-systems"></a>支持的操作系统
可以在运行以下操作系统的 VM 或计算机上创建群集（目前尚不支持 Linux）：

* Windows Server 2012 R2
* Windows Server 2016 
* Windows Server 2019

## <a name="next-steps"></a>后续步骤
详细了解如何[保护](service-fabric-cluster-security.md)、[缩放](service-fabric-cluster-scaling-standalone.md)和[升级](service-fabric-cluster-upgrade-standalone.md)独立群集。

了解 [Service Fabric 支持选项](service-fabric-support.md)。
