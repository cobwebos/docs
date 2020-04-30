---
title: 自承载网关概述 |Microsoft Docs
description: 了解 Azure API 管理的自承载网关功能如何帮助组织管理混合环境和多云环境中的 Api。
services: api-management
documentationcenter: ''
author: vlvinogr
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: b560b02544eeb96167e68ed305d4d9942d2b1e0f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232966"
---
# <a name="self-hosted-gateway-overview"></a>自托管网关概述

本文介绍如何通过 Azure API 管理的自承载网关功能实现混合和多云 API 管理，展示其高级体系结构并突出显示其功能。

## <a name="hybrid-and-multi-cloud-api-management"></a>混合和多云 API 管理

自承载网关功能扩展了混合和多云环境的 API 管理支持，使组织能够安全有效地从 Azure 中的单个 API 管理服务来管理本地和跨云承载的 API。

借助自承载网关，客户可以灵活地将 API 管理网关组件的容器化版本部署到用于承载其 API 的相同环境。 所有自承载网关都是通过与它们联合的 API 管理服务进行管理的，因此，在所有内部和外部 API 中可为客户提供直观统一的管理体验。 将网关放在靠近 API 的位置可让客户优化 API 流量流，并满足安全性与合规性的要求。

每个 API 管理服务由以下关键组件构成：

-   作为 API 公开的管理平面，用于通过 Azure 门户、PowerShell 和其他支持的机制配置服务。
-   网关（或数据平面）负责代理 API 请求、应用策略以及收集遥测数据
-   开发人员门户，供开发人员用来发现、学习 API 以及在登记后使用 API

默认情况下，所有这些组件都部署在 Azure 中，因此，无论实现 API 的后端承载在何处，所有 API 流量（在下图中以黑色实线箭头指示）都会流经 Azure。 此模型虽然在操作上比较简单，但代价是增大延迟，造成合规性问题，而且在某些情况下还会产生额外的数据传输费。

![不使用自承载网关的 API 流量流](media/self-hosted-gateway-overview/without-gateways.png)

将自承载网关部署到后端 API 实现的相同环境中，可让 API 流量直接流向后端 Api，从而提高延迟、优化数据传输成本并实现符合性，同时保持对组织中所有 Api 的单一管理点、可观察性和发现的好处，而不考虑其实现的托管位置。

![使用自承载网关的 API 流量流](media/self-hosted-gateway-overview/with-gateways.png)

## <a name="packaging-and-features"></a>打包和功能

自承载网关是托管网关的容器化版本，两者的功能相当，但前者作为每个 API 管理服务的一部分部署到 Azure。 自承载的网关可作为 Microsoft 容器注册表中基于 Linux 的 Docker[容器](https://aka.ms/apim/sputnik/dhub)。 在个人计算机上，可以将其部署到 Docker、Kubernetes 或任何其他在本地服务器群集上运行的、云基础结构或评估和开发用途的容器业务流程解决方案。

托管网关上的以下功能在自承载网**关上不可用**：

- Azure Monitor 日志
- 上游（后端端） TLS 版本和密码管理
- 使用上传到 API 管理服务的[CA 根证书](api-management-howto-ca-certificates.md)验证服务器和客户端证书。 若要添加对自定义 CA 的支持，请将一层添加到自行托管的网关容器映像中，该映像将安装 CA 的根证书。
- 与[Service Fabric](../service-fabric/service-fabric-api-management-overview.md)的集成
- TLS 会话恢复
- 客户端证书重新协商。 这意味着，对于工作中的[客户端证书身份验证](api-management-howto-mutual-certificates-for-clients.md)，使用者必须在初始 TLS 握手中提供其证书。 若要确保，请在配置自承载网关自定义主机名时启用 "协商客户端证书" 设置。
- 内置缓存。 请参阅此[文档](api-management-howto-cache-external.md)，了解如何在自承载网关上使用外部缓存。

## <a name="connectivity-to-azure"></a>连接到 Azure

自承载的网关需要端口443上到 Azure 的出站 TCP/IP 连接。 每个自托管网关必须与单个 API 管理服务相关联，并通过其管理平面进行配置。 自承载网关使用与 Azure 的连接来执行以下操作：

-   通过每隔一分钟发送检测信号消息来报告自身的状态
-   定期（每隔 10 秒）检查配置更新，每当有可用更新时应用这些更新
-   将请求日志和指标发送到 Azure Monitor（如果采用此配置）
-   将事件发送到 Application Insights（如果采用此设置）

与 Azure 断开连接时，自承载网关将无法接收配置更新、报告自身的状态或上传遥测数据。

自承载的网关设计为 "静态失败"，并可以在暂时失去与 Azure 的连接。 可以通过或不带本地配置备份来部署它。 在前一种情况下，自承载网关会定期在附加到其容器或 pod 的持久卷上保存最新下载配置的备份副本。

已禁用配置备份且与 Azure 中断连接时：

-   运行自承载的网关将使用配置的内存中副本继续工作
-   已停止的自承载网关将无法启动

已启用配置备份且与 Azure 中断连接时：

-   运行自承载的网关将使用配置的内存中副本继续工作
-   已停止的自承载网关将能够开始使用配置的备份副本

连接恢复后，受中断影响的每个自承载网关将自动重新连接到其关联的 API 管理服务，并下载它处于“脱机”状态时发生的所有配置更新。

## <a name="next-steps"></a>后续步骤

-   [阅读有关本主题更多背景信息的白皮书](https://aka.ms/hybrid-and-multi-cloud-api-management)
-   [将自承载网关部署到 Docker](how-to-deploy-self-hosted-gateway-docker.md)
-   [将自承载网关部署到 Kubernetes](how-to-deploy-self-hosted-gateway-kubernetes.md)
