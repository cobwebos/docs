---
title: Istio 概述
description: 获取 Istio 的概述
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77593894"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>概述

[Istio][istio]是一个功能齐全、customisable 且可扩展的服务网格。

## <a name="architecture"></a>体系结构

Istio 提供了由基于[Envoy][envoy-proxy]的分支组成的数据平面。 这些智能代理控制进出你的网状应用和工作负荷的所有网络流量。

控制平面通过以下[组件][what-is-istio]管理配置、策略和遥测：

- **混音**器-强制实施访问控制和使用策略。 从推送到[Prometheus][prometheus]的代理收集遥测数据。

- **试点**-为代理提供服务发现和流量管理策略/配置。

- **Citadel** -提供允许服务之间的 mTLS 的标识和安全功能。

- **条样**-抽象并为组件提供配置。

下面的体系结构关系图演示了数据平面和控制平面内的各种组件之间的交互方式。


![Istio 组件和体系结构概述。](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>选择条件

评估工作负荷的 Istio 时，请务必了解和考虑以下方面：

- [设计目标](#design-goals)
- [功能](#capabilities)
- [方案](#scenarios)


### <a name="design-goals"></a>设计目标

以下设计目标[指导][design-goals]Istio 项目：

- **最大程度地提高透明度**-允许采用最少的工作量，充分利用系统的价值。

- **扩展性**-必须能够增长并适应不断变化的需求。

- 可**移植性**-在不同类型的环境中轻松运行-云，本地运行。

- **策略一致性**-跨各种资源在策略定义中的一致性。


### <a name="capabilities"></a>功能

Istio 提供以下功能集：

- **网格**-网关（多群集），虚拟机（网格扩展）

- **流量管理**–路由、拆分、超时、断路断路、重试、入口、出口

- **策略**–访问控制、速率限制、配额、自定义策略适配器

- **安全**-身份验证（jwt）、授权、加密（mTLS）、外部 CA （HashiCorp 保管库）

- **可观察性**–金牌指标、镜像、跟踪、自定义适配器、prometheus、grafana

### <a name="scenarios"></a>方案

Istio 非常适合用于以下方案：

- 需要可扩展性和丰富的功能集

- 用于包括基于 VM 的工作负载的网格扩展

- 多群集服务网格

## <a name="next-steps"></a>后续步骤

以下文档介绍了如何在 Azure Kubernetes 服务（AKS）上安装 Istio：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes Service （AKS）中安装 Istio][istio-install]

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
