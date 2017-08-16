---
title: "Azure 服务总线身份验证和授权 | Microsoft Docs"
description: "使用共享访问签名 (SAS) 身份验证向服务总线验证应用程序。"
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 18bad0ed-1cee-4a5c-a377-facc4785c8c9
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 28fb41499c919e5006f1be7daa97610c2a0583af
ms.contentlocale: zh-cn
ms.lasthandoff: 08/10/2017

---
# <a name="service-bus-authentication-and-authorization"></a>服务总线身份验证和授权

应用程序可以使用共享访问签名 (SAS) 身份验证对 Azure 服务总线进行身份验证。 通过共享访问签名身份验证，应用程序能够使用在命名空间或在关联了特定权限的实体上配置的访问密钥向服务总线进行身份验证。 然后可以使用此密钥生成共享访问签名令牌，客户端可用它向服务总线进行身份验证。

> [!IMPORTANT]
> 应使用 SAS 而非 Azure Active Directory 访问控制（又被称为访问控制服务或 ACS），因为 ACS 将被弃用。 SAS 为服务总线提供了一种简单、灵活且易于使用的身份验证方案。 当应用程序不需要管理授权“用户”这一概念时，可以使用 SAS。 有关详细信息，请参阅[此博客文章](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)。

## <a name="shared-access-signature-authentication"></a>共享访问签名身份验证

通过 [SAS 身份验证](service-bus-sas.md)可向具有特定权限的用户授予对服务总线资源的访问权限。 服务总线中的 SAS 身份验证涉及配置具有服务总线资源相关权限的加密密钥。 客户端随后即可通过提供 SAS 令牌获取该资源的访问权限，该令牌由要访问的资源 URI 和签有已配置密钥的过期时间组成。

可以在服务总线命名空间上配置用于 SAS 的密钥。 该密钥将应用到该命名空间中的所有消息传送实体。 可在服务总线队列和主题上配置密钥。 [Azure 中继](../service-bus-relay/relay-authentication-and-authorization.md)也支持 SAS。

若要使用 SAS，可以为命名空间、队列或主题配置 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 对象。 此规则由以下元素组成：

* 标识此规则的 KeyName。
* PrimaryKey，用于对 SAS 令牌进行签名/验证的加密密钥。
* SecondaryKey，用于对 SAS 令牌进行签名/验证的加密密钥。
* Rights，表示授予的侦听、发送或管理权限的集合。

通过在命名空间级别配置的授权规则，可以向具有使用相应密钥签名的令牌的客户端授予命名空间中所有实体的访问权限。 在服务总线命名空间、队列或主题上最多可配置 12 个此类规则。 默认情况下，首次预配时，将为每个命名空间配置具有所有权限的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)。

若要访问某个实体，客户端需要使用特定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 生成的 SAS 令牌。 SAS 令牌是通过使用资源字符串的 HMAC-SHA256 生成的，该字符串由要授予对其访问权限的资源 URI 和授权规则相关加密密钥的过期时间组成。

Azure.NET SDK 2.0 版和更高版本支持服务总线的 SAS 身份验证。 SAS 支持 [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)。 允许将连接字符串作为参数的所有 API 都支持 SAS 连接字符串。

## <a name="next-steps"></a>后续步骤

- 有关 SAS 的详细信息，请继续阅读[使用共享访问签名进行服务总线身份验证](service-bus-sas.md)。
- [更改为启用 ACS 的命名空间。](https://blogs.msdn.microsoft.com/servicebus/2017/06/01/upcoming-changes-to-acs-enabled-namespaces/)
- 有关 Azure 中继身份验证和授权的相关信息，请参阅 [Azure 中继身份验证和授权](../service-bus-relay/relay-authentication-and-authorization.md)。 


