---
title: 托管的虚拟网络 & 托管的专用终结点
description: 了解 Azure 数据工厂中托管的虚拟网络和托管的专用终结点。
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/15/2020
ms.openlocfilehash: c0f23c864430b6cb2f49f924d5aaa8bde296037c
ms.sourcegitcommit: 1aef4235aec3fd326ded18df7fdb750883809ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/12/2020
ms.locfileid: "88135957"
---
# <a name="azure-data-factory-managed-virtual-network-preview"></a>Azure 数据工厂托管的虚拟网络 (预览) 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

本文将介绍 Azure 数据工厂中的托管虚拟网络和托管的专用终结点。


## <a name="managed-virtual-network"></a>托管虚拟网络

当你在 Azure 数据工厂托管的虚拟网络 (VNET) 中创建 Azure Integration Runtime (IR) 时，将使用托管虚拟网络预配集成运行时，并将利用专用终结点安全连接到支持的数据存储。 

在托管的虚拟网络中创建 Azure IR 可确保隔离和保护数据集成过程。 

使用托管虚拟网络的好处：

- 使用托管虚拟网络，可以将管理虚拟网络的负担分担到 Azure 数据工厂。 不需要为从虚拟网络最终使用多个专用 Ip 的 Azure Integration Runtime 创建一个子网，并且需要预先进行网络基础结构计划。 
- 它不需要深入的 Azure 网络知识即可安全地执行数据集成。 从安全 ETL 开始，为数据工程师提供了很多简化。 
- 托管的虚拟网络和托管的专用终结点可防止数据渗透。 

> [!IMPORTANT]
>目前，托管 VNet 仅在与 Azure 数据工厂区域相同的区域中受支持。
 

![ADF 托管虚拟网络体系结构](./media/managed-vnet/managed-vnet-architecture-diagram.png)

## <a name="managed-private-endpoints"></a>托管专用终结点

托管专用终结点是在 Azure 数据工厂托管的虚拟网络中创建的专用终结点，用于建立到 Azure 资源的专用链接。 Azure 数据工厂代表你管理这些专用终结点。 

![新托管专用终结点](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png)

Azure 数据工厂支持专用链接。 通过专用链接，你可以访问 Azure (PaaS) 服务 (如 Azure 存储、Azure Cosmos DB、Azure Synapse 分析 (以前的 Azure SQL 数据仓库) # A5。

使用专用链接时，数据存储和托管虚拟网络之间的流量完全通过 Microsoft 骨干网络进行遍历。 专用链接可防止数据泄露风险。 可以通过创建专用终结点来建立到资源的专用链接。

专用终结点在托管的虚拟网络中使用专用 IP 地址，以有效地将服务引入其中。 专用终结点映射到 Azure 中的特定资源，而不是整个服务。 客户可以将连接限制到其组织批准的特定资源。 详细了解[专用链接和专用终结点](https://docs.microsoft.com/azure/private-link/)。

> [!NOTE]
> 建议你创建托管专用终结点来连接到所有 Azure 数据源。 
 
> [!WARNING]
> 如果 PaaS 数据存储 (Blob、ADLS Gen2、SQL DW) 已经为其创建了专用终结点，即使它允许从所有网络进行访问，ADF 也只能使用托管的专用终结点来访问它。 请确保在此类方案中创建专用终结点。 

在 Azure 数据工厂中创建托管专用终结点时，将以 "挂起" 状态创建专用终结点连接。 将启动审批工作流。 专用链接资源所有者负责批准或拒绝该连接。

![管理专用终结点](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png)

如果所有者批准该连接，则会建立专用链接。 否则，将不会建立专用链接。 在任一情况下，都会以连接状态更新托管专用终结点。

![批准托管专用终结点](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

只有处于已批准状态的托管专用终结点才能将流量发送到给定的专用链接资源。

## <a name="limitations-and-known-issues"></a>限制和已知问题
### <a name="supported-data-sources"></a>支持的数据源
以下数据源支持通过来自 ADF 托管虚拟网络的私有链接进行连接。
- Azure Blob 存储
- Azure 表存储
- Azure 文件
- Azure Data Lake Gen2
- Azure SQL Database (不包括 Azure SQL 托管实例) 
- Azure Synapse Analytics（以前称为 Azure SQL 数据仓库）
- Azure CosmosDB SQL
- Azure Key Vault
- Azure 专用链接

### <a name="outbound-communications-through-public-endpoint-from-adf-managed-virtual-network"></a>从 ADF 托管虚拟网络通过公共终结点进行的出站通信
- 对于出站通信，只打开端口443。
- 不支持将 Azure 存储和 Azure Data Lake Gen2 从 ADF 托管的虚拟网络通过公共终结点进行连接。

### <a name="other-known-issues"></a>其他已知问题
CosmosDB 连接的调试运行不起作用，包括数据流调试和管道调试。


## <a name="next-steps"></a>后续步骤

- 教程：[使用托管的虚拟网络和专用终结点构建复制管道](tutorial-copy-data-portal-private.md) 
- 教程：[使用托管的虚拟网络和专用终结点生成映射数据流管道](tutorial-data-flow-private.md)
