---
title: Istio 概述
description: 获取 Istio 的概述
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593894"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>概述

[Istio][istio] 是一种功能全面、可自定义且可扩展的服务网格。

## <a name="architecture"></a>体系结构

Istio 提供了由基于 [Envoy][envoy-proxy] 的挎斗组成的数据平面。 这些智能代理控制进出网格应用和工作负荷的所有网络流量。

控制平面通过以下[组件][what-is-istio]管理配置、策略和遥测：

- **Mixer** - 强制实施访问控制和使用策略。 从代理收集推送到 [Prometheus][prometheus] 中的遥测。

- **Pilot** - 为代理提供服务发现和流量管理策略/配置。

- **Citadel** - 提供允许在服务之间使用 mTLS 的标识和安全功能。

- **Galley** - 抽取配置并将其提供给组件。

下面的体系结构关系图演示了数据平面和控制平面内的各种组件如何交互。


![Istio 组件和体系结构概述。](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>选择条件

在为工作负荷评估 Istio 时，请务必了解并考虑以下方面：

- [设计目标](#design-goals)
- [功能](#capabilities)
- [应用场景](#scenarios)


### <a name="design-goals"></a>设计目标

以下设计目标[指导][design-goals] Istio 项目：

- **将透明度最大化** - 允许通过最小量的工作完成采用操作，从系统获取真正的价值。

- **扩展性** - 必须能够扩展并根据不断变化的需求进行调整。

- **可移植性** - 在不同类型的环境中（不管是在云中还是在本地）轻松地运行。

- **策略一致性** - 跨各种资源在策略定义方面保持一致性。


### <a name="capabilities"></a>功能

Istio 提供下述功能集：

- **网格**+ 网关（多群集）、虚拟机（网格扩展）

- **交通管理**– 路由、拆分、超时、断路器、重试、入口、出口

- **策略**= 访问控制、速率限制、配额、自定义策略适配器

- **安全性**+ 身份验证 （jwt）、授权、加密 （mTLS）、外部 CA（HashiCorp 保险库）

- **可观测性**– 黄金指标、镜像、跟踪、自定义适配器、普罗米古斯、格拉法纳

### <a name="scenarios"></a>方案

Istio 非常适合以下方案，建议采用：

- 需要扩展性和丰富的功能集

- 进行网格扩展，使之包含基于 VM 的工作负荷

- 多群集服务网格

## <a name="next-steps"></a>后续步骤

以下文档介绍如何在 Azure Kubernetes 服务 (AKS) 上安装 Istio：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes 服务 (AKS) 中安装 Istio][istio-install]

还可以进一步探索 Istio 概念和其他部署模型：

- [Istio 概念][what-is-istio]
- [Istio 部署模型][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
