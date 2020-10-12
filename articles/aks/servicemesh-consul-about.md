---
title: Consul 概述
description: 获取 Consul 的概述
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83774006"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>概述

[Consul][consul] 是一项能够感知多数据中心的服务网络解决方案，可以跨运行时平台连接服务并确保其安全。 [Connect][consul-features] 是提供服务网格功能的组件。

## <a name="architecture"></a>体系结构

默认情况下，Consul 提供由基于 [Envoy][envoy-proxy] 的[挎斗][consul-sidecar]组成的数据平面。 Consul 有一个可插入的代理体系结构。 这些智能代理控制进出网格应用和工作负荷的所有网络流量。

控制平面通过以下[组件][consul-architecture]管理配置和策略：

- **服务器** - 一个以“服务器”模式运行的 Consul 代理，可保留 Consul 群集状态。

- **客户端** - 一个 Consul 代理，在轻型客户端模式下运行。 每个计算节点必须有一个正在运行的客户端代理。 此客户端代理工作负荷和 Consul 配置之间的配置和策略。 

下面的体系结构关系图演示了数据平面和控制平面内的各种组件如何交互。

![Consul 组件和体系结构概述。](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>选择条件

为工作负荷评估 Consul 时，请务必了解并考虑以下方面：

- [Consul 准则](#consul-principles)
- [功能](#capabilities)
- [方案](#scenarios)


### <a name="consul-principles"></a>Consul 原则

以下原则[指导][consul-principles] Consul 项目：

- **API 驱动** - 对所有配置和策略进行编码。

- **在任意位置运行并连接** - 跨运行时平台（Kubernetes、VM、无服务器）连接工作负荷。

- **扩展和集成** - 跨基础结构安全地连接工作负荷。


### <a name="capabilities"></a>功能

Consul 提供下述功能集：

- **网格** – 网关（多数据中心）、虚拟机（群集节点外部）、服务同步、内置调试选项

- **代理** – Envoy、内置代理、适用于 Windows 工作负载的 l4 可插入代理

- **流量管理** – 路由、拆分、解析

- **策略** – 意图、ACL

- **安全** – 授权、身份验证、加密、基于 SPIFFE 的标识、外部证书颁发机构（保管库）、证书管理和轮换

- **可观察性** – 指标、UI 仪表板、Prometheus、Grafana


### <a name="scenarios"></a>方案

Consul 非常适合以下方案，建议采用：

- 扩展现有的 Consul 连接的工作负荷

- 围绕证书管理制定的符合性要求

- 多群集服务网格

- 基于 VM 的工作负荷，将会包括在服务网格中



## <a name="next-steps"></a>后续步骤

以下文档介绍如何在 Azure Kubernetes 服务 (AKS) 上安装 Consul：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes 服务 (AKS) 中安装 Consul][consul-install]

此外，还可以通过以下文章进一步了解 Consul 的功能和体系结构：

- [Consul 入门教程][consul-getting-started]
- [Consul 功能][consul-features]
- [Consul 体系结构][consul-architecture]
- [Consul - Connect 工作原理][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
