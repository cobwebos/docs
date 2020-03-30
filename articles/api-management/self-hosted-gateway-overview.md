---
title: 自承载 Azure API 管理网关概述 | Microsoft Docs
description: 了解自承载 Azure API 管理网关如何帮助组织在混合环境和多云环境中管理 API。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/31/2019
ms.author: apimpm
ms.openlocfilehash: 415f0e209e607a863d715b1a66a2435603a662f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73513713"
---
# <a name="self-hosted-api-management-gateway-overview"></a>自承载 API 管理网关概述

本文介绍自承载网关功能如何实现混合和多云 API 管理，从较高层面演示其体系结构，并重点介绍其基本功能。

> [!NOTE]
> 自承载网关功能目前以预览版提供。 在预览期间，自承载网关仅在“开发人员”层和“高级”层提供，不额外收费。 “开发人员”层仅限单个自承载网关部署。

## <a name="hybrid-and-multi-cloud-api-management"></a>混合和多云 API 管理

自承载网关功能扩展了混合和多云环境的 API 管理支持，使组织能够安全有效地从 Azure 中的单个 API 管理服务来管理本地和跨云承载的 API。

借助自承载网关，客户可以灵活地将 API 管理网关组件的容器化版本部署到用于承载其 API 的相同环境。 所有自承载网关都是通过与它们联合的 API 管理服务进行管理的，因此，在所有内部和外部 API 中可为客户提供直观统一的管理体验。 将网关放在靠近 API 的位置可让客户优化 API 流量流，并满足安全性与合规性的要求。

每个 API 管理服务由以下关键组件构成：

-   作为 API 公开的管理平面，用于通过 Azure 门户、PowerShell 和其他支持的机制配置服务。
-   网关（或数据平面）负责代理 API 请求、应用策略以及收集遥测数据
-   开发人员门户，供开发人员用来发现、学习 API 以及在登记后使用 API

默认情况下，所有这些组件都部署在 Azure 中，因此，无论实现 API 的后端承载在何处，所有 API 流量（在下图中以黑色实线箭头指示）都会流经 Azure。 此模型虽然在操作上比较简单，但代价是增大延迟，造成合规性问题，而且在某些情况下还会产生额外的数据传输费。

![不使用自承载网关的 API 流量流](media/self-hosted-gateway-overview/without-gateways.png)

如果将自承载网关部署到后端 API 实现所在的相同环境，并将其添加到 API 管理服务，则可以使 API 流量直接流向后端 API，从而改善延迟，优化数据传输成本，并实现合规，同时，可以继续享有单一管理点和发现组织中所有 API 的优势，而不管这些 API 的实现承载在何处。

![使用自承载网关的 API 流量流](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>打包和功能

自承载网关是托管网关的容器化版本，两者的功能相当，但前者作为每个 API 管理服务的一部分部署到 Azure。 自承载网关在 Microsoft 容器注册表中以基于 Linux 的 Docker 容器的形式提供。 可将它部署到 Docker、Kubernetes，或者在桌面、服务器群集或云基础结构上运行的任何其他容器业务流程解决方案。

> [!IMPORTANT]
> 托管网关提供的某些功能在预览版中尚未提供。 最值得注意的是：登录到事件中心策略，服务交换矩阵集成，下游 HTTP/2。 我们尚未计划在自承载网关中提供内置缓存。

## <a name="connectivity-to-azure"></a>连接到 Azure

自承载网关需要通过端口 443 来与 Azure 建立出站 TCP/IP 连接。 每个自承载网关必须与单个 API 管理服务相关联，并通过其管理平面进行配置。 自承载网关使用与 Azure 的连接来执行以下操作：

-   通过每隔一分钟发送检测信号消息来报告自身的状态
-   定期（每隔 10 秒）检查配置更新，每当有可用更新时应用这些更新
-   将请求日志和指标发送到 Azure Monitor（如果采用此配置）
-   将事件发送到 Application Insights（如果采用此设置）

与 Azure 断开连接时，自承载网关将无法接收配置更新、报告自身的状态或上传遥测数据。

自承载网关采用“静态失败”设计，在暂时与 Azure 断开连接时可以留存。 可以在启用或不启用本地配置备份的情况下部署自承载网关。 对于前一种情况，自承载网关会定期将配置的备份副本保存在已附加到容器或 pod 的持久性卷上。

已禁用配置备份且与 Azure 中断连接时：

-   正在运行的自承载网关将使用配置的内存中副本继续正常工作
-   已停止的自承载网关将无法启动

已启用配置备份且与 Azure 中断连接时：

-   正在运行的自承载网关将使用配置的内存中副本继续正常工作
-   已停止的自承载网关将使用配置的备份副本启动

连接恢复后，受中断影响的每个自承载网关将自动重新连接到其关联的 API 管理服务，并下载它处于“脱机”状态时发生的所有配置更新。

## <a name="next-steps"></a>后续步骤

-   [阅读有关本主题更多背景信息的白皮书](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [将自承载网关部署到 Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [将自承载网关部署到 Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
