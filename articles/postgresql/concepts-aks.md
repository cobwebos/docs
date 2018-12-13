---
title: 将 Azure Kubernetes 服务 (AKS) 与 Azure Database for PostgreSQL 连接
description: 了解如何将 Azure Kubernetes 服务与 Azure Database for PostgreSQL 连接
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.date: 11/27/2018
ms.topic: article
ms.openlocfilehash: 86474fe612fb93f3a5853f9fea98eb9ab2dd00e5
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52336401"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql"></a>连接 Azure Kubernetes 服务和 Azure Database for PostgreSQL

Azure Kubernetes 服务 (AKS) 提供可在 Azure 中使用的托管 Kubernetes 群集。 下面是将 AKS 和 Azure Database for PostgreSQL 一起使用以创建应用程序时要考虑的一些选项。


## <a name="accelerated-networking"></a>加速网络
在 AKS 群集中使用支持加速网络的底层 VM。 在 VM 上启用加速网络时，可以降低延迟、降低抖动和降低 VM 上的 CPU 利用率。 详细了解加速网络的工作原理、支持的 OS 版本以及 [Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 支持的 VM 实例。

从 2018 年 11 月起，AKS 支持在这些受支持的 VM 实例上启用加速网络。 默认情况下，加速网络已在使用这些 VM 的新 AKS 群集上启用。

可以确认 AKS 群集是否已加速了网络：
1. 转到 Azure 门户并选择 AKS 群集。
2. 选择“属性”选项卡。
3. 复制**基础结构资源组**的名称。
4. 使用门户搜索栏找到并打开基础结构资源组。
5. 在该资源组中选择一个 VM。
6. 转到 VM 的“网络”选项卡。
7. 确认是否已启用**加速网络**。


## <a name="open-service-broker-for-azure"></a>打开 Service Broker for Azure 
[Open Service Broker for Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) 允许你直接从 Kubernetes 或 Cloud Foundry 预配 Azure 服务。 它是适用于 Azure 的 [Open Service Broker API](https://www.openservicebrokerapi.org/) 实现。

通过 OSBA，可以使用 Kubernetes 的本机语言创建 Azure Database for PostgreSQL 服务器并将其绑定到 AKS 群集。 在 [OSBA Github 页](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md)上了解如何将 OSBA 和 Azure Database for PostgreSQL 一起使用。 


## <a name="connection-pooling"></a>连接池
在创建和关闭到数据库的新连接时，连接池程序可以尽量减少相关的成本和时间。 该池是可以重复使用的连接的集合。 

可以将多个连接池程序与 PostgreSQL 配合使用。 其中一个是 [PgBouncer](https://pgbouncer.github.io/)。 在 Microsoft 容器注册表中，我们提供一个轻量型容器化 PgBouncer，它可以在挎斗中用来池化从 AKS 到 Azure Database for PostgreSQL 的连接。 请访问 [Docker 中心页](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/)，了解如何访问并使用此映像。 


## <a name="next-steps"></a>后续步骤
-  [创建 Azure Kubernetes 服务群集](../aks/kubernetes-walkthrough.md)
