---
title: Linkerd 概述
description: 获取 Linkerd 的概述
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 9d0a65f2cf557120ec5a551494e697c876ad5c59
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530207"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>概述

[Linkerd][linkerd]是一种易于使用且轻型的服务网格。

## <a name="architecture"></a>体系结构

Linkerd 提供由 ultralight [Linkerd][linkerd-proxy]专用 proxy 分支组成的数据平面。 这些智能代理控制进出你的网状应用和工作负荷的所有网络流量。 代理还通过[Prometheus][prometheus]指标终结点公开指标。

控制平面通过以下[组件][linkerd-architecture]管理配置和聚合遥测：

- **控制器**-提供用于驱动 Linkerd CLI 和仪表板的 api。 为代理提供配置。

- **点击**-基于请求和响应建立实时监视。

- **标识**-提供允许服务之间的 mTLS 的标识和安全功能。

- **Web** -提供 Linkerd 仪表板。


下面的体系结构关系图演示了数据平面和控制平面内的各种组件之间的交互方式。


![Linkerd 组件和体系结构概述。](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>选择条件

评估工作负荷的 Linkerd 时，请务必了解和考虑以下方面：

- [设计原则](#design-principles)
- [功能](#capabilities)
- [方案](#scenarios)


### <a name="design-principles"></a>设计原理

以下设计原则[指导][design-principles]Linkerd 项目：

- **简单**易用-必须易于使用和理解。

- **最大程度地减少资源需求**-施加最小的性能和资源成本。

- **只需工作**-不要中断现有的应用程序，也不需要复杂的配置。


### <a name="capabilities"></a>功能

Linkerd 提供以下功能集：

- **网格**–内置调试选项

- **流量管理**-拆分、超时、重试、进入

- **安全性**-加密（mTLS），证书 autorotated 每24小时一次

- **可观察性**–金牌指标、点击、跟踪、服务配置文件和每个路由指标、具有拓扑图的 web 仪表板、prometheus、grafana


### <a name="scenarios"></a>方案

Linkerd 非常适合用于以下方案：

- 简单易用，只使用基本的一组功能要求

- 低延迟、低开销，重点关注可观察性和简单流量管理


## <a name="next-steps"></a>后续步骤

以下文档介绍了如何在 Azure Kubernetes 服务（AKS）上安装 Linkerd：

> [!div class="nextstepaction"]
> [在 Azure Kubernetes Service （AKS）中安装 Linkerd][linkerd-install]

还可以进一步探索 Linkerd 的功能和体系结构：

- [Linkerd 功能][linkerd-features]
- [Linkerd 体系结构][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md