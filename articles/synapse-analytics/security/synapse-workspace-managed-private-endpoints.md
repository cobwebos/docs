---
title: 托管专用终结点
description: 本文介绍 Azure Synapse Analytics 中的托管专用终结点
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ecca67cab486c8f3524c8c8d4c221d52689cf62a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87070112"
---
# <a name="synapse-managed-private-endpoints-preview"></a>Synapse 托管专用终结点（预览版）

本文将介绍 Azure Synapse Analytics 中的托管专用终结点。

## <a name="managed-private-endpoints"></a>托管专用终结点

托管专用终结点是在托管工作区 Microsoft Azure 虚拟网络中创建的专用终结点，用于建立到 Azure 资源的专用链接。 Azure Synapse 代表你管理这些专用终结点。

Azure Synapse 支持专用链接。 通过专用链接，你可以安全地从 Azure 虚拟网络访问 Azure 服务（例如 Azure 存储、Azure Cosmos DB 和 Azure SQL 数据仓库）和 Azure 托管的客户/合作伙伴服务。

使用专用链接时，虚拟网络与工作区之间的流量全部通过 Microsoft 主干网络进行传输。 专用链接可防止数据泄露风险。 可以通过创建专用终结点来建立到资源的专用链接。

专用终结点使用虚拟网络中的专用 IP 地址将服务有效接入虚拟网络中。 专用终结点映射到 Azure 中的特定资源，而不是整个服务。 客户可以将连接限制到其组织批准的特定资源。 详细了解[专用链接和专用终结点](https://docs.microsoft.com/azure/private-link/)。

>[!IMPORTANT]
>托管专用终结点仅在具有托管工作区虚拟网络的 Azure Synapse 工作区中受支持。

>[!NOTE]
>未来，将阻止来自托管工作区虚拟网络的所有出站流量（通过托管专用终结点的出站流量除外）。 建议创建托管专用终结点来连接到工作区外部的所有 Azure 数据源。 

在 Azure Synapse 中创建托管专用终结点时，创建的专用终结点连接将处于“挂起”状态。 将启动审批工作流。 专用链接资源所有者负责批准或拒绝该连接。

如果所有者批准该连接，则会建立专用链接。 否则，将不会建立专用链接。 在任一情况下，都会以连接状态更新托管专用终结点。

只有处于已批准状态的托管专用终结点才能将流量发送到给定的专用链接资源。

## <a name="managed-private-endpoints-for-sql-pool-and-sql-on-demand"></a>SQL 池和 SQL 按需版本的托管专用终结点

SQL 池和 SQL 按需版本是 Azure Synapse 工作区中的分析功能。 这些功能使用未部署到[托管工作区虚拟网络](./synapse-workspace-managed-vnet.md)中的多租户基础结构。

创建工作区时，Azure Synapse 会在该工作区中创建面向 SQL 池和 SQL 按需版本的两个托管专用终结点。 

这两个托管专用终结点会在 Azure Synapse Studio 中列出。 在 Studio 的左侧导航栏中选择“管理”，然后选择“托管虚拟网络”即可查看它们。

面向 SQL 池的托管专用终结点名为 synapse-ws-sql--\<workspacename\>，面向 SQL 按需版本的托管专用终结点名为 synapse-ws-sqlOnDemand--\<workspacename\> 。
![SQL 池和 SQL 按需版本的托管专用终结点](./media/synapse-workspace-managed-private-endpoints/managed-pe-for-sql-1.png)

创建 Azure Synapse 工作区时，会自动为你创建这两个托管专用终结点。 对于这两个托管专用终结点，你无需付费。

## <a name="next-steps"></a>后续步骤

[创建用于访问数据源的托管专用终结点](./how-to-create-managed-private-endpoints.md)
