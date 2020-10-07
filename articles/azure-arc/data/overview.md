---
title: 什么是已启用 Azure Arc 的数据服务
description: 介绍已启用 Azure Arc 的数据服务
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 09/22/2020
ms.topic: overview
ms.openlocfilehash: 47fef490c5ece577823a14e3fa4c415f0f613ccb
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "90943485"
---
# <a name="what-are-azure-arc-enabled-data-services-preview"></a>什么是已启用 Azure Arc 的数据服务（预览）？

借助 Azure Arc，可以使用 Kubernetes 和你选择的基础结构在本地、边缘和公有云环境中运行 Azure 数据服务。

当前，以下已启用 Azure Arc 的数据服务以预览形式提供：

- SQL 托管实例
- PostgreSQL 超大规模

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="always-current"></a>始终最新

已启用 Azure Arc 的数据服务（例如已启用 Azure Arc 的 SQL 托管实例和已启用 Azure Arc 的 PostgreSQL 超大规模）频繁接收更新，包括服务补丁和新功能，与 Azure 中的体验相似。 我们将为你提供 Microsoft 容器注册表的更新，而部署进度由你根据你自己的策略进行设置。 这样，本地数据库可以保持最新状态，同时确保你保持控制。 由于已启用 Azure Arc 的数据服务是一项订阅服务，你将不再面临终止对数据库的支持这种情况。

## <a name="elastic-scale"></a>弹性缩放

利用类似于云的本地弹性能力，可以根据基础结构的可用容量，以与 Azure 中几乎相同的方式动态纵向扩展或纵向缩减数据库。 此功能可以满足具有不稳定的需求的突发情况，包括需要以亚秒级响应时间实时引入和查询任意规模数据的情况。 此外，还可以使用 Azure Database for PostgreSQL 超大规模的唯一超大规模部署选项来横向扩展数据库实例。 通过使用独特的横向扩展读写，此功能为数据工作负载提供了容量优化方面的额外提升。

## <a name="self-service-provisioning"></a>自助式预配

Azure Arc 还提供了其他云优势，例如快速部署和大规模自动化。 由于基于 Kubernetes 的编排，可以使用 GUI 或 CLI 工具在几秒钟内部署数据库。

## <a name="unified-management"></a>统一管理

现在，使用 Azure 门户、Azure Data Studio 和 Azure Data CLI 等熟悉的工具，可以统一查看使用 Azure Arc 部署的所有数据资产。你不仅可以查看和管理整个环境和 Azure 中的各种关系数据库，还可以从 Kubernetes API 获取日志和遥测，以分析基础结构的容量和运行状况。 除了拥有本地化的日志分析和性能监视之外，你现在还可以利用 Azure Monitor 在整个资产范围内获得全面的操作见解。

## <a name="disconnected-scenario-support"></a>断开连接的方案支持

无论是否直接连接到 Azure，许多服务（例如自助式预配、自动备份/还原和监视）都可以在基础结构中在本地运行。 若直接连接到 Azure，这样可提供用于与其他 Azure 服务（例如 Azure Monitor）集成的其他选项，并且可以在世界各地使用 Azure 门户和 Azure 资源管理器 API 来管理已启用 Azure Arc 的数据服务。

## <a name="next-steps"></a>后续步骤

> 想尝试一下吗？  
> 在 Azure Kubernetes 服务 (AKS)、AWS Elastic Kubernetes 服务 (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中，通过 [Azure Arc 快速入门](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services)快速开始操作。

[安装客户端工具](install-client-tools.md)

[创建 Azure Arc 数据控制器](create-data-controller.md)（首先需要安装客户端工具）

[在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)（首先需要创建 Azure Arc 数据控制器）

[在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md)（首先需要创建 Azure Arc 数据控制器）
