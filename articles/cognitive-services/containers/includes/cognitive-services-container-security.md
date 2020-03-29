---
title: 容器安全性
titleSuffix: Azure Cognitive Services
description: 了解如何保护容器
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/30/2019
ms.author: dapine
ms.openlocfilehash: 20f78d9269d4b2270293c8746157ba495c694562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80272873"
---
## <a name="azure-cognitive-services-container-security"></a>Azure 认知服务容器安全性

开发应用程序时，安全性是主要关注因素。 安全性重要是因为它是成功的指标。 构建包含认知服务容器的软件解决方案时，必须了解你所受的限制和能够使用的功能。 有关网络安全的详细信息，请参阅[配置 Azure 认知服务虚拟网络][az-security]。

> [!IMPORTANT]
> 默认情况下，认知服务容器 API 上没有安全措施。** 之所以这样设置，是因为大多数情况下容器会作为 Pod 的一部分运行，而 Pod 受网络桥的保护，与外部隔离。 但是，可以启用与访问[基于云的认知服务][request-authentication]时使用的身份验证相同的身份验证。

下图演示了默认的**非安全**方法：

![容器安全性](../media/container-security.svg)

认知服务容器的所有者可以通过一个前置组件来增强容器，让容器终结点保持专用，这是一种替代的安全** 方法。 让我们考虑一个方案，在该方案中，我们使用 [Istio][istio] 作为入口网关。 Istio 支持 HTTPS/TLS 和客户端证书身份验证。 在此方案中，Istio 前端会公开容器访问权限，提供的客户端证书已事先通过 Istio 加入允许列表。

在同一类别中，[Nginx][nginx] 是另一常用的选择。 Istio 和 Nginx 都充当服务网格，并提供其他功能，例如负载均衡、路由和速率控制。

### <a name="container-networking"></a>容器网络

若要提交计费所需的计量信息，则需要认知服务容器。 唯一例外是脱机容器，因为后者遵循不同的计费方法。** 如果无法允许列出认知服务容器依赖的各种网络通道，则容器不能正常运行。

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>允许列出认知服务域和端口

主机应该允许列出**端口 443** 和以下域：

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>禁用深度行数据包检查

> [深度数据包检查](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) 是一种数据处理，它会详细检查通过计算机网络发送的数据，并且通常会对其采取阻止、重新路由或日志记录等相应操作。

在认知服务容器创建的通往 Microsoft 服务器的安全通道上禁用 DPI。 如果不能这样做，则容器无法正常运行。

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
