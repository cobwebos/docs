---
title: Azure 应用程序网关前端 IP 地址配置
description: 本文介绍如何配置 Azure 应用程序网关前端 IP 地址。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: d6cfac7f0fb3939e57ce64f552556138ce9feacd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89652854"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>应用程序网关前端 IP 地址配置

可将应用程序网关配置为使用公共 IP 地址和/或专用 IP 地址。 托管需要由客户端在 Internet 中通过面向 Internet 的虚拟 IP (VIP) 访问的后端时，必须使用公共 IP 地址。

## <a name="public-and-private-ip-address-support"></a>公共和专用 IP 地址支持

应用程序网关 V2 目前不支持专用 IP 模式。 它支持以下组合：

* 专用 IP 地址和公共 IP 地址
* 仅限公共 IP 地址

有关详细信息，请参阅[有关应用程序网关的常见问题解答](application-gateway-faq.md#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address)。


不向 Internet 公开的内部终结点不需要公共 IP 地址。 该终结点称为内部负载均衡器 (ILB) 终结点或专用前端 IP。 应用程序网关 ILB 适合用于不向 Internet 公开的内部业务线应用程序。 对于位于不向 Internet 公开的安全边界内的多层级应用程序中的服务和层级，ILB 也很有用，但需要启用轮循机制负载分配、会话粘性或 TLS 终止。

仅支持一个公共 IP 地址或一个专用 IP 地址。 在创建应用程序网关时选择前端 IP。

- 对于公共 IP 地址，可以在应用程序网关所在的位置创建新的公共 IP 地址或使用现有的公共 IP。 有关详细信息，请参阅[静态与动态公共 IP 地址](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address)。

- 对于专用 IP 地址，可以在创建应用程序网关的子网中指定一个专用 IP 地址。 如果不显式指定专用 IP 地址，则系统会在子网中自动选择一个任意 IP 地址。 以后无法更改选定的 IP 地址类型（静态或动态）。 有关详细信息，请参阅[创建包含内部负载均衡器的应用程序网关](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)。

某个前端 IP 地址将关联到检查前端 IP 上的传入请求的侦听器。

## <a name="next-steps"></a>后续步骤

- [了解侦听器配置](configuration-listeners.md)