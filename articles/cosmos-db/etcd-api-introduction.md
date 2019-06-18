---
title: Azure Cosmos DB etcd API 简介
description: 本文提供 Azure Cosmos DB 中 etcd API 的概述和重要优势
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205809"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Azure Cosmos DB etcd API（预览版）简介

Azure Cosmos DB 是 Microsoft 的全球分布式多模型数据库服务的关键任务应用程序。 它提供统包全局分发、 弹性缩放吞吐量和存储，达 99%的个位数的毫秒级延迟以及得到保证的高可用性，所有这些均由行业领先的 SLA。

[Etcd](https://github.com/etcd-io/etcd) 是分布式键/值存储。 在 [Kubernetes](https://kubernetes.io/) 中，etcd 用于存储 Kubernetes 群集的状态和配置。 确保 etcd 的可用性、可靠性和性能对于 Kubernetes 群集的整体群集运行状况、可伸缩性、弹性可用性和性能至关重要。 

通过使用 Azure Cosmos DB 中的 etcd API，可将 Azure Cosmos DB 用作 [Azure Kubernetes](../aks/index.yml) 的后端存储。 Azure Cosmos DB 中的 etcd API 目前处于预览状态。 Azure Cosmos DB 实施 etcd 线路协议。 Azure Cosmos DB 中 etcd API，开发人员将自动获得高度可靠[可用](high-availability.md)，[全球分布式](distribute-data-globally.md)Kubernetes。 此 API 可让开发人员在完全托管的云原生 PaaS 服务中缩放 Kubernetes 状态管理。 

> [!NOTE]
> 与 Azure Cosmos DB 中的其他 API 不同，无法通过 Azure 门户、CLI 或 SDK 预配 etcd API 帐户。 只能通过部署资源管理器模板来预配 etcd API 帐户；有关详细步骤，请参阅[如何使用 Azure Cosmos DB 预配 Azure Kubernetes](bootstrap-kubernetes-cluster.md) 一文。 Azure Cosmos DB etcd API 目前以受限预览版提供。 可以通过填写注册表单来[注册预览版](https://aka.ms/cosmosetcdapi-signup)。

## <a name="wire-level-compatibility"></a>线路级兼容性

Azure Cosmos DB 实施 etcd 线路协议版本 3，允许[主节点的](https://kubernetes.io/docs/concepts/overview/components/) API 服务器使用 Azure Cosmos DB，就像在本地安装的 etcd 环境中一样。 etcd API 支持 TLS 相互身份验证。 

下图显示了 Kubernetes 群集的组件。 在群集主节点中，API 服务器使用 Azure Cosmos DB etcd API 而不是本地安装的 etcd。 

![实施 etcd 线路协议的 Azure Cosmos DB](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>主要优点

### <a name="no-etcd-operations-management"></a>无需 etcd 操作管理

作为一种完全托管的云原生服务，Azure Cosmos DB 无需 Kubernetes 开发人员设置和管理 etcd。 Azure Cosmos DB 中的 API etcd 具有可伸缩性、高可用性和容错能力，并提供高性能。 设置跨多个节点的复制、执行滚动更新、安全修补和监视 etcd 运行状况所造成的开销均由 Azure Cosmos DB 处理。

### <a name="global-distribution--high-availability"></a>全局分发和高可用性 

Azure Cosmos DB 使用 etcd API 来保证单个区域中 99.99% 的数据读写可用性，以及多个区域中 99.999% 的读写可用性。 

### <a name="elastic-scalability"></a>弹性的可伸缩性

Azure Cosmos DB 为不同区域中的读写请求提供弹性缩放能力。
随着 Kubernetes 群集的不断扩大，Azure Cosmos DB 中的 etcd API 帐户可弹性缩放，且不会造成停机。 将 etcd 数据存储在 Azure Cosmos DB 而不是 Kubernetes 主节点中还可以进一步灵活缩放主节点。 

### <a name="security--enterprise-readiness"></a>安全性和企业就绪

将 etcd 数据存储到 Azure Cosmos DB 后，Kubernetes 开发人员会自动获得 Azure Cosmos DB 支持的[内置静态加密](database-encryption-at-rest.md)、[认证与法规遵从性](compliance.md)和[备份与还原功能](online-backup-and-restore.md)。 

## <a name="next-steps"></a>后续步骤

* [如何将 Azure Kubernetes 与 Azure Cosmos DB 配合使用](bootstrap-kubernetes-cluster.md)
* [Azure Cosmos DB 的重要优势](introduction.md)
* [AKS 引擎快速入门指南](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)