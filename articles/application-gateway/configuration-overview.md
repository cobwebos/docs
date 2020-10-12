---
title: Azure 应用程序网关配置概述
description: 本文介绍如何配置 Azure 应用程序网关的组件
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 0012ac99600c77dce5940387e1da54f29c3f6ab7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652007"
---
# <a name="application-gateway-configuration-overview"></a>应用程序网关配置概述

Azure 应用程序网关由多个组件构成，可根据不同的方案以不同的方式配置这些组件。 本文将会介绍如何配置每个组件。

![应用程序网关组件流程图](./media/configuration-overview/configuration-overview1.png)

此图演示了包含三个侦听器的应用程序。 前两个侦听器是分别用于 `http://acme.com/*` 和 `http://fabrikam.com/*` 的多站点侦听器， 两者在端口 80 上侦听。 第三个侦听器是支持端到端传输层安全性 (TLS) 终止（前称为“安全套接字层 (SSL) 终止”）的基本侦听器。

## <a name="infrastructure"></a>基础结构

应用程序网关基础结构包括虚拟网络、子网、网络安全组和用户定义路由。

有关详细信息，请参阅[应用程序网关基础结构配置](configuration-infrastructure.md)。



## <a name="front-end-ip-address"></a>前端 IP 地址

可将应用程序网关配置为使用公共 IP 地址和/或专用 IP 地址。 托管需要由客户端在 Internet 中通过面向 Internet 的虚拟 IP (VIP) 访问的后端时，必须使用公共 IP。

有关详细信息，请参阅[应用程序网关前端 IP 地址配置](configuration-front-end-ip.md)。

## <a name="listeners"></a>侦听器

侦听器是一个逻辑实体，它可以使用端口、协议、主机和 IP 地址检查传入的连接请求。 配置侦听器时，必须输入与网关上传入请求中的对应值相匹配的值。

有关详细信息，请参阅[应用程序网关侦听器配置](configuration-listeners.md)。

## <a name="request-routing-rules"></a>请求路由规则

使用 Azure 门户创建应用程序网关时，可创建一个默认规则 (*rule1*)。 此规则会将默认侦听器 (*appGatewayHttpListener*) 绑定到默认后端池 (*appGatewayBackendPool*) 和默认后端 HTTP 设置 (*appGatewayBackendHttpSettings*)。 创建网关后，可以编辑该默认规则的设置，或创建新的规则。

有关详细信息，请参阅[应用程序网关请求传递规则](configuration-request-routing-rules.md)。

## <a name="http-settings"></a>HTTP 设置

应用程序网关使用此处指定的配置将流量路由到后端服务器。 创建 HTTP 设置后，必须将其关联到一个或多个请求路由规则。

有关详细信息，请参阅[应用程序网关 HTTP 设置配置](configuration-http-settings.md)。

## <a name="back-end-pool"></a>后端池

可将后端池指向四种类型的后端成员：特定的虚拟机、虚拟机规模集、IP 地址/FQDN 或应用服务。 

创建后端池后，必须将其关联到一个或多个请求路由规则。 此外，必须为应用程序网关上的每个后端池配置运行状况探测。 满足请求路由规则条件时，应用程序网关会将流量转发到相应后端池中正常运行的服务器（是否正常由运行状况探测决定）。

## <a name="health-probes"></a>运行状况探测

应用程序网关默认会监视其后端中所有资源的运行状况。 但是，我们强烈建议为每个后端 HTTP 设置创建一个自定义探测，以便更好地控制运行状况监视。 若要了解如何配置自定义探测，请参阅[自定义运行状况探测设置](application-gateway-probe-overview.md#custom-health-probe-settings)。

> [!NOTE]
> 创建自定义运行状况探测后，需将其关联到后端 HTTP 设置。 只有在将相应的 HTTP 设置通过规则显式关联到某个侦听器之后，自定义探测才会监视后端池的运行状况。

## <a name="next-steps"></a>后续步骤

了解应用程序网关组件后，可以：

- [在 Azure 门户中创建应用程序网关](quick-create-portal.md)
- [使用 PowerShell 创建应用程序网关](quick-create-powershell.md)
- [使用 Azure CLI 创建应用程序网关](quick-create-cli.md)
