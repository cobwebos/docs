---
title: Azure Cosmos DB etcd API 简介
description: 本文概述了 Azure Cosmos DB 中的 etcd API 及其主要优点
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 208f0d73b4c8bb11adc9c73796ebc6a622f5ed50
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85118162"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure Cosmos DB etcd API 简介（预览）

Azure Cosmos DB 是 Microsoft 针对任务关键型应用程序提供的全球分布式多模型数据库服务。 它提供统包全局分发、吞吐量和存储的弹性扩展、99% 的情况下低至个位数的毫秒级延迟以及得到保证的高可用性，所有这些均由行业领先的 SLA 提供支持。

[Etcd](https://github.com/etcd-io/etcd) 是一个分布式密钥/值存储。 在 [Kubernetes](https://kubernetes.io/) 中，etcd 用于存储 Kubernetes 群集的状态和配置。 确保 etcd 的可用性、可靠性和性能对于 Kubernetes 群集的整体运行状况、可伸缩性、弹性可用性和性能至关重要。

通过使用 Azure Cosmos DB 中的 etcd API，可将 Azure Cosmos DB 用作 Azure Kubernetes 的后端存储。 Azure Cosmos DB 中的 etcd API 目前处于预览状态。 Azure Cosmos DB 实现 etcd 线路协议。 使用 Azure Cosmos DB 中的 etcd API，开发人员将自动获得高度可靠的、[可用的](high-availability.md)、[全局分布的](distribute-data-globally.md) Kubernetes。 此 API 允许开发人员在完全托管的云本地 PaaS 服务上缩放 Kubernetes 状态管理。 

> [!NOTE]
> 与 Azure Cosmos DB 中的其他 API 不同，不能通过 Azure 门户、CLI 或 SDK 预配 etcd API 帐户。 可以仅通过部署资源管理器模板来预配 etcd API 帐户；有关详细步骤，请参阅[如何使用 Azure Cosmos DB 预配 Azure Kubernetes](bootstrap-kubernetes-cluster.md) 一文。 Azure Cosmos DB etcd API 目前以受限预览版提供。 可以通过填写注册表单[注册预览](https://aka.ms/cosmosetcdapi-signup)。

## <a name="wire-level-compatibility"></a>线路级别的兼容性

Azure Cosmos DB 实现 etcd 版本 3 的线路协议，并允许[主节点的](https://kubernetes.io/docs/concepts/overview/components/) API 服务器使用 Azure Cosmos DB，就像在本地安装的 etcd 环境中那样。 Etcd API 支持 TLS 相互身份验证。 

下图显示了 Kubernetes 群集的组件。 在主群集中，API 服务器使用 Azure Cosmos DB etcd API，而不是本地安装的 etcd。 

:::image type="content" source="./media/etcd-api-introduction/etcd-api-wire-protocol.png" alt-text="Azure Cosmos DB 实现 etcd 线路协议" border="false":::

## <a name="key-benefits"></a>主要优点

### <a name="no-etcd-operations-management"></a>无 etcd 操作管理

作为一个完全托管的本机云服务，Azure Cosmos DB 可使 Kubernetes 开发人员无需设置和管理 etcd。 Azure Cosmos DB 中的 etcd API 具有可伸缩性、高可用性、容错性和高性能。 Azure Cosmos DB 可处理跨多个节点设置复制、执行滚动更新和安全修补程序以及监视 etcd 运行状况所产生的开销。

### <a name="global-distribution--high-availability"></a>全局分发和高可用性 

通过使用 etcd API，Azure Cosmos DB 可保证单个区域中数据 99.99% 的读写可用性，以及多个区域中 99.999% 的可用性。 

### <a name="elastic-scalability"></a>弹性的可伸缩性

Azure Cosmos DB 为跨不同区域的读取和写入请求提供了弹性可伸缩性。
随着 Kubernetes 群集的增长，Azure Cosmos DB 中的 etcd API 帐户可以在不停机的情况下进行缩放。 在 Azure Cosmos DB 中存储 etcd 数据（而不是在 Kubernetes 主节点中存储）还可以实现更灵活的主节点缩放。 

### <a name="security--enterprise-readiness"></a>安全和企业就绪情况

当 etcd 数据存储在 Azure Cosmos DB 中时，Kubernetes 开发人员会自动获得 Azure Cosmos DB 支持的[内置静态加密](database-encryption-at-rest.md)、[认证和合规性](compliance.md)、以及[备份和还原功能](online-backup-and-restore.md)。 

## <a name="next-steps"></a>后续步骤

* [如何将 Azure Kubernetes 与 Azure Cosmos DB 配合使用](bootstrap-kubernetes-cluster.md)
* [Azure Cosmos DB 的主要优势](introduction.md)
* [AKS 引擎快速入门指南](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)