---
title: "Azure 服务总线身份验证和授权 | Microsoft Docs"
description: "共享访问签名 (SAS) 身份验证概述。"
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
ms.date: 03/21/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 4eaae725c62f66de1b50fd2c7094f3e6e89281be
ms.lasthandoff: 04/27/2017


---
# <a name="service-bus-authentication-and-authorization"></a>服务总线身份验证和授权
可通过两种方式向 Azure 服务总线验证应用程序，即使用共享访问签名 (SAS) 身份验证，或通过 Azure Active Directory 访问控制（也称为访问控制服务或 ACS）进行身份验证。 通过共享访问签名身份验证，应用程序能够使用在命名空间或在关联了特定权限的实体上配置的访问密钥向服务总线进行身份验证。 然后可以使用此密钥生成共享访问签名令牌，客户端可用它向服务总线进行身份验证。

> [!IMPORTANT]
> 建议使用 SAS 而不是 ACS，因为它为服务总线提供了一种简单、灵活且易于使用的身份验证方案。 当应用程序不需要管理授权“用户”这一概念时，可以使用 SAS。 

## <a name="shared-access-signature-authentication"></a>共享访问签名身份验证
通过 [SAS 身份验证](service-bus-sas.md)可向具有特定权限的用户授予对服务总线资源的访问权限。 服务总线中的 SAS 身份验证涉及配置具有服务总线资源相关权限的加密密钥。 客户端随后即可通过提供 SAS 令牌获取该资源的访问权限，该令牌由要访问的资源 URI 和签有已配置密钥的过期时间组成。

可以在服务总线命名空间上配置用于 SAS 的密钥。 该密钥将应用到该命名空间中的所有消息传送实体。 可在服务总线队列和主题上配置密钥。 [Azure 中继](../service-bus-relay/relay-authentication-and-authorization.md)也支持 SAS。

若要使用 SAS，可在由以下项构成的命名空间、队列或主题上配置 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 对象：

* 标识此规则的 KeyName。
* PrimaryKey，用于对 SAS 令牌进行签名/验证的加密密钥。
* SecondaryKey，用于对 SAS 令牌进行签名/验证的加密密钥。
* Rights，表示授予的侦听、发送或管理权限的集合。

通过在命名空间级别配置的授权规则，可以向具有使用相应密钥签名的令牌的客户端授予命名空间中所有实体的访问权限。 在服务总线命名空间、队列或主题上最多可配置 12 个此类规则。 默认情况下，首次预配时，将为每个命名空间配置具有所有权限的 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)。

若要访问某个实体，客户端需要使用特定 [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) 生成的 SAS 令牌。 SAS 令牌是通过使用资源字符串的 HMAC-SHA256 生成的，该字符串由要授予对其访问权限的资源 URI 和授权规则相关加密密钥的过期时间组成。

Azure.NET SDK 2.0 版和更高版本支持服务总线的 SAS 身份验证。 SAS 支持 [SharedAccessAuthorizationRule](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule)。 允许将连接字符串作为参数的所有 API 都支持 SAS 连接字符串。

## <a name="acs-authentication"></a>ACS 身份验证
使用 ACS 的服务总线身份验证通过随附的“-sb”ACS 命名空间进行管理。 如果要为服务总线命名空间创建随附 ACS 命名空间，则不能使用 Azure 经典门户创建服务总线命名空间，而必须使用 [New-AzureSBNamespace](/powershell/module/azure/new-azuresbnamespace?view=azuresmps-3.7.0) PowerShell cmdlet 来创建命名空间。 例如：

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

若要避免创建 ACS 命名空间，请发出以下命令：

```powershell
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

例如，如果创建名为 **contoso.servicebus.windows.net** 的服务总线命名空间，则将自动预配名为 **contoso-sb.accesscontrol.windows.net** 的随附 ACS 命名空间。 对于在 2014 年 8 月之前创建的所有命名空间，还将创建一个随附的 ACS 命名空间。

默认情况下，此随附 ACS 命名空间中设置了默认服务标识“owner”，具有全部权限。 使用 ACS，可以通过配置相应信任关系来获取对任何服务总线实体的精细化控制。 可以配置附加服务标识，用于管理对服务总线实体的访问权限。

客户端通过提供其凭据，从具有相应声明的 ACS 请求 SWT 令牌，从而访问某个实体。 随后必须将 SWT 令牌作为请求的一部分发送到服务总线，以便启用客户端身份验证，从而访问该实体。

Azure.NET SDK 2.0 版和更高版本支持服务总线的 ACS 身份验证。 此身份验证支持 [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider)。 允许将连接字符串作为参数的所有 API 都支持 ACS 连接字符串。

## <a name="next-steps"></a>后续步骤
有关 SAS 的详细信息，请继续阅读[使用共享访问签名进行服务总线身份验证](service-bus-sas.md)。

有关 Azure 中继身份验证和授权的相关信息，请参阅 [Azure 中继身份验证和授权](../service-bus-relay/relay-authentication-and-authorization.md)。 


