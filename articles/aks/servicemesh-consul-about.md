---
title: Consul 概述
description: 获取 Consul 的概述
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594203"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>概述

[Consul][consul]是一个支持多个数据中心的服务网络解决方案，可跨运行时平台连接和保护服务。 [连接][consul-features]是提供服务网格功能的组件。

## <a name="architecture"></a>体系结构

默认情况下，Consul 提供了由基于[Envoy][envoy-proxy]的[分支][consul-sidecar]组成的数据平面。 Consul 有一个可插入的代理体系结构。 这些智能代理控制进出你的网状应用和工作负荷的所有网络流量。

控制平面通过以下[组件][consul-architecture]管理配置和策略：

- **服务器**-在服务器模式下运行的 Consul 代理，用于维护 Consul 群集状态。

- **客户端**-在轻型客户端模式下运行的 Consul 代理。 每个计算节点都必须有一个运行的客户端代理。 工作负荷和 Consul 配置之间的此客户端代理配置和策略。 

下面的体系结构关系图演示了数据平面和控制平面内的各种组件之间的交互方式。

![Consul 组件和体系结构概述。](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>选择条件

评估工作负荷的 Consul 时，请务必了解和考虑以下方面：

- [Consul 原则](#consul-principles)
- [功能](#capabilities)
- [方案](#scenarios)


### <a name="consul-principles"></a>Consul 原则

以下原则[指导][consul-principles]Consul 项目：

- **API 驱动**-编入所有配置和策略。

- 在**任意位置运行和连接**-通过运行时平台连接工作负荷（Kubernetes、Vm、无服务器）。

- **扩展和集成**-跨基础结构安全地连接工作负荷。


### <a name="capabilities"></a>功能

Consul 提供以下功能集：

- **网格**-网关（多数据中心），虚拟机（群集节点不足），服务同步，内置调试选项

- **代理**-Envoy、内置代理、可插入、适用于 Windows 工作负荷的 l4 代理

- **流量管理**–路由、拆分、解析

- **策略**–意向，acl

- **安全性**–授权、身份验证、加密、基于 SPIFFE 的标识、外部 CA （保管库）、证书管理和旋转

- **可观察性**–指标，ui 仪表板，prometheus，grafana


### <a name="scenarios"></a>方案

Consul 非常适合用于以下方案：

- 扩展现有的 Consul 连接工作负荷

- 有关证书管理的符合性要求

- 多群集服务网格

- 要包括在服务网格中的基于 VM 的工作负荷



## <a name="next-steps"></a>后续步骤

以下文档介绍了如何在 Azure Kubernetes 服务（AKS）上安装 Consul：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes Service （AKS）中安装 Consul][consul-install]

还可以进一步探索 Consul 的功能和体系结构：

- [Consul 功能][consul-features]
- [Consul 体系结构][consul-architecture]
- [Consul-连接的工作原理][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
