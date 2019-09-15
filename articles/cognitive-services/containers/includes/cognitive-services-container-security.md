---
title: 容器安全性
titleSuffix: Azure Cognitive Services
description: 了解如何保护容器
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/13/2019
ms.author: dapine
ms.openlocfilehash: d8d069dddbce6ab6ddb541db460634ad3f6fa067
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/14/2019
ms.locfileid: "70994965"
---
## <a name="azure-cognitive-services-container-security"></a>Azure 认知服务容器安全性

无论何时开发应用程序，安全性都应该是主要关注点。 安全的重要性是成功的指标。 构建包含认知服务容器的软件解决方案时，请务必了解可用的限制和功能。 有关详细信息，请参阅[Azure 安全性][az-security]。

> [!IMPORTANT]
> 默认情况下，认知服务容器 API*没有安全性*。 这样做的原因是：容器最常见的情况是，容器将作为 pod 的一部分运行，而该 pod 由网桥保护。 但是，可以启用与访问[基于云的认知服务][request-authentication]时所使用的身份验证相同的身份验证。

下图说明了默认方法和不**安全**的方法：

![容器安全性](../media/container-security.svg)

认知服务容器的使用者是一种替代和*安全*的方法，可以使用正面组件扩充容器，使容器终结点专用。 让我们考虑一种方案，其中使用[Istio][istio]作为入口网关。 Istio 支持 HTTPS/SSL 和客户端证书身份验证。 在这种情况下，Istio 前端将公开容器访问权限，并在 Istio 的后面呈现客户端证书。

[Nginx][nginx]是同一类别中的另一种常用选择。 Istio 和 Nginx 都充当服务网格，提供其他功能，包括负载平衡、路由和速率控制等功能。

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../../security/fundamentals/overview.md
