---
title: 自承载 Azure API 管理网关概述 |Microsoft Docs
description: 了解自承载 Azure API 管理网关如何帮助组织管理混合环境和多云环境中的 Api。
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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73513713"
---
# <a name="self-hosted-api-management-gateway-overview"></a>自承载 API 管理网关概述

本文介绍如何启用混合和多云 API 管理的自承载网关功能，并介绍其基本功能。

> [!NOTE]
> 自承载网关功能处于预览阶段。 在预览期间，仅在开发人员和高级层提供自承载的网关，无额外费用。 开发人员层限制为单个自行托管的网关部署。

## <a name="hybrid-and-multi-cloud-api-management"></a>混合和多云 API 管理

自承载网关功能扩展了 API 管理对混合和多云环境的支持，并使组织能够有效地从 Azure 中的单个 API 管理服务管理本地和跨云托管的 Api。

使用自承载网关，客户可以灵活地将 API 管理网关的容器化版本部署到托管其 Api 的相同环境。 所有自承载的网关都是从其联合的 API 管理服务管理的，因此为客户提供跨所有内部和外部 Api 的可见性和统一的管理体验。 如果将网关放置在 Api 附近，客户可以优化 API 流量流，并满足安全性和符合性要求。

每个 API 管理服务由以下关键组件组成：

-   作为 API 公开的管理平面，用于通过 Azure 门户、PowerShell 和其他受支持的机制来配置服务。
-   网关（或数据平面）负责代理 API 请求、应用策略以及收集遥测数据
-   开发人员门户，用于发现、学习和载入以使用 Api

默认情况下，所有这些组件都部署在 Azure 中，导致所有 API 流量（如下面的图片中所示的实心黑色箭头）流过 Azure，而不考虑实现 Api 的后端的托管位置。 此模型的操作简易性的代价是增加了延迟、合规性问题，在某些情况下还需要支付额外的数据传输费用。

![无需自承载网关的 API 流量流](media/self-hosted-gateway-overview/without-gateways.png)

将自承载网关部署到后端 API 实现的相同环境中，并将其添加到 API 管理服务，可以使 API 流量直接流向后端 Api，从而提高延迟、优化数据传输成本，并使符合性，同时保持对组织中所有 Api 的单一管理和发现的好处，而不考虑其实现的托管位置。

![具有自承载网关的 API 流量流](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>打包和功能

自承载网关是一种容器化、功能等效的托管网关版本，作为每个 API 管理服务的一部分部署到 Azure。 自承载的网关可作为 Microsoft 容器注册表中基于 Linux 的 Docker 容器。 它可以部署到 Docker、Kubernetes 或任何其他在桌面、服务器群集或云基础结构上运行的容器业务流程解决方案。

> [!IMPORTANT]
> 托管网关上提供的某些功能在预览中尚不可用。 最值得注意的是：记录到事件中心策略，Service Fabric 集成，下游 HTTP/2。 无计划在自承载网关中提供内置缓存。

## <a name="connectivity-to-azure"></a>连接到 Azure

自承载的网关需要端口443上到 Azure 的出站 TCP/IP 连接。 每个自托管网关都必须与单个 API 管理服务相关联，并通过其管理平面进行配置。 自承载网关将连接到 Azure 以实现以下用途：

-   每分钟发送检测信号消息，报告其状态
-   每隔10秒检查一次，并在配置更新可用时定期进行检查
-   将请求日志和指标发送到 Azure Monitor （如果配置为这样做）
-   如果设置为，则向 Application Insights 发送事件

当与 Azure 的连接丢失时，自承载网关将无法接收配置更新、报告其状态或上传遥测。

自承载的网关设计为 "静态失败"，并可在暂时失去与 Azure 的连接时保留。 可以通过或不启用本地配置备份来部署它。 在前一种情况下，自承载网关会定期在附加到容器或 pod 的持久卷上保存配置的备份副本。

当配置备份处于关闭状态并且与 Azure 的连接中断时：

-   正在运行的自承载的网关将使用配置的内存中副本继续工作
-   停止的自承载网关将无法启动

当启用配置备份并中断与 Azure 的连接时：

-   正在运行的自承载的网关将使用配置的内存中副本继续工作
-   已停止的自承载网关将开始使用配置的备份副本

当恢复连接时，受中断影响的每个自承载网关都将自动重新连接到其关联的 API 管理服务，并下载网关处于 "脱机" 状态时所发生的所有配置更新。

## <a name="next-steps"></a>后续步骤

-   [阅读有关本主题的更多背景的白皮书](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [将自承载网关部署到 Docker](api-management-howto-deploy-self-hosted-gateway-to-docker.md)
-   [将自承载网关部署到 Kubernetes](api-management-howto-deploy-self-hosted-gateway-to-k8s.md)
