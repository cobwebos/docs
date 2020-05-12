---
title: Azure 中继身份验证和授权 | Microsoft Docs
description: 本文概述了如何使用 Azure 中继服务进行共享访问签名 (SAS) 身份验证。
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: aac5c973a99b13d5918a0162feb7f1ede443463b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210562"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure 中继身份验证和授权

应用程序可以使用共享访问签名 (SAS) 身份验证对 Azure 中继进行身份验证。 通过 SAS 身份验证，应用程序能够使用在中继命名空间中配置的访问密钥向 Azure 中继服务进行身份验证。 然后可以使用此密钥生成共享访问签名令牌，客户端可用它向中继服务进行身份验证。

## <a name="shared-access-signature-authentication"></a>共享访问签名身份验证

通过 [SAS 身份验证](../service-bus-messaging/service-bus-sas.md)可向具有特定权限的用户授予对 Azure 中继资源的访问权限。 SAS 身份验证涉及配置具有资源相关权限的加密密钥。 客户端随后即可通过提供 SAS 令牌获取该资源的访问权限，该令牌由要访问的资源 URI 和签有已配置密钥的过期时间组成。

可以在中继命名空间上配置用于 SAS 的密钥。 与服务总线消息传送不同，[中继混合连接](relay-hybrid-connections-protocol.md)支持未经授权的发件人或匿名发件人。 可在创建实体时启用它的匿名访问权限，如门户中以下屏幕截图所示：

![][0]

若要使用 SAS，可在由以下项构成的中继命名空间上配置 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 对象：

* 标识此规则的 KeyName  。
* PrimaryKey  ，用于对 SAS 令牌进行签名/验证的加密密钥。
* SecondaryKey  ，用于对 SAS 令牌进行签名/验证的加密密钥。
* Rights  ，表示授予的侦听、发送或管理权限的集合。

在命名空间级别配置的授权规则，可以向具有使用相应密钥签名的令牌的客户端授予命名空间中所有中继连接的访问权限。 在中继命名空间上最多可配置 12 个此类授权规则。 默认情况下，首次预配时，将为每个命名空间配置具有所有权限的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)。

若要访问某个实体，客户端需要使用特定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 生成的 SAS 令牌。 SAS 令牌是通过使用资源字符串的 HMAC-SHA256 生成的，该字符串由要授予对其访问权限的资源 URI 和授权规则相关加密密钥的过期时间组成。

Azure.NET SDK 2.0 版和更高版本中包含 Azure 中继的 SAS 身份验证支持。 SAS 支持 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)。 允许将连接字符串作为参数的所有 API 都支持 SAS 连接字符串。

## <a name="next-steps"></a>后续步骤

- 有关 SAS 的详细信息，请继续阅读[使用共享访问签名进行服务总线身份验证](../service-bus-messaging/service-bus-sas.md)。
- 有关混合连接功能的详细信息，请参阅[Azure 中继混合连接协议指南](relay-hybrid-connections-protocol.md)。
- 有关服务总线消息传送身份验证和授权的相关信息，请参阅[服务总线身份验证和授权](../service-bus-messaging/service-bus-authentication-and-authorization.md)。 

[0]: ./media/relay-authentication-and-authorization/hcanon.png