---
title: Azure 应用服务的安全属性
description: 评估 Azure 应用服务的安全属性进行的检查表
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: d22fca27943be7ac7db8b8edd5882b9fa4ab3ab9
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65607253"
---
# <a name="security-attributes-for-azure-app-service"></a>Azure 应用服务的安全属性

本文介绍 Azure 应用服务中内置的常见安全属性。

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全特性 | Yes/No | 说明 |
|---|---|--|
| （例如服务器端加密、 使用客户托管密钥的服务器端加密和其他加密功能） 的静态加密 | 是 | 网站文件内容存储在 Azure 存储空间，它会自动加密静态内容。 请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。<br><br>提供的客户机密进行静态加密。 静态存储在应用服务配置数据库中时加密机密。<br><br>（可选），本地附加的磁盘可以用作临时存储的网站 （D:\local 和 %TMP%)。 本地附加的磁盘不进行静态加密。 |
| 传输中加密 （如 Vnet 加密和 VNet 加密中的 ExpressRoute 加密）| 是 | 客户可以配置为要求和为入站流量使用 HTTPS 的网站。 请参阅博客文章[如何使 Azure 应用服务仅 HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)。 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户可以选择在 Key Vault 中存储应用程序机密，并在运行时检索它们。 请参阅[使用密钥保管库引用应用服务和 Azure Functions （预览版）](app-service-key-vault-references.md)。|
| 列级加密（Azure 数据服务）| 不适用 | |
| 加密的 API 调用| 是 | 通过进行管理调用来配置应用服务[Azure 资源管理器](../azure-resource-manager/index.yml)通过 HTTPS 调用。 |

## <a name="network-segmentation"></a>网络分段

| 安全特性 | Yes/No | 说明 |
|---|---|--|
| 服务终结点支持| 是 | 当前提供预览版，应用服务。 请参阅[Azure 应用服务访问限制](app-service-ip-restrictions.md)。 |
| vNET 注入支持| 是 | 应用服务环境是应用服务专用于单个客户注入到客户的虚拟网络的专用实现。 请参阅[应用服务环境简介](environment/intro.md)。 |
| 网络隔离和 Firewalling 支持| 是 | 对于应用服务的公共多租户变体，客户可以配置网络 Acl （IP 限制），以允许入站流量锁定。  请参阅[Azure 应用服务访问限制](app-service-ip-restrictions.md)。  应用服务环境部署直接到虚拟网络，并因此可以使用 Nsg 保护。 |
| 支持强制隧道 | 是 | 可以将应用服务环境部署到客户的虚拟网络，配置强制隧道。 客户需要按照中的说明[使用强制隧道配置应用服务环境](environment/forced-tunnel-support.md)。 |

## <a name="detection"></a>检测

| 安全特性 | Yes/No | 说明|
|---|---|--|
| Azure 监视支持 （日志分析、 应用程序见解等）| 是 | 应用服务与 Application Insights 集成支持 Application Insights （完整的.NET Framework、.NET Core、 Java 和 Node.JS） 的语言。  请参阅[监视 Azure 应用服务性能](../azure-monitor/app/azure-web-apps.md)。 应用服务还会应用程序指标发送到 Azure Monitor。 请参阅[在 Azure 应用服务中监视应用](web-sites-monitor.md)。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全特性 | Yes/No | 说明|
|---|---|--|
| Authentication| 是 | 客户可以生成自动将与集成的应用服务上的应用程序[Azure Active Directory (Azure AD)](../active-directory/index.md)以及其他 OAuth 兼容的标识提供程序; 请参阅[身份验证和授权Azure 应用服务](overview-authentication-authorization.md)。 管理应用服务资产的访问，所有访问都受 Azure AD 进行身份验证主体和 Azure 资源管理器 RBAC 角色的组合。 |
| 授权| 是 | 管理应用服务资产的访问，所有访问都受 Azure AD 进行身份验证主体和 Azure 资源管理器 RBAC 角色的组合。  |


## <a name="audit-trail"></a>审核线索

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 控制/管理计划日志记录和审核| 是 | 通过应用服务的对象上执行的所有管理操作都发生[Azure 资源管理器](../azure-resource-manager/index.yml)。 这些操作的历史日志可在门户中，通过 CLI;请参阅[Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)并[az monitor 活动日志](/cli/azure/monitor/activity-log)。 |
| 数据平面日志记录和审核 | 否 | 应用服务的数据平面是包含客户的部署的网站内容的远程文件共享。  没有远程文件共享的任何审核。 |

## <a name="configuration-management"></a>配置管理

| 安全特性 | Yes/No | 说明|
|---|---|--|
| 配置管理支持 （版本控制的配置，等等）。| 是 | 对于管理操作，应用服务配置的状态可以导出为 Azure 资源管理器模板和版本控制随着时间的推移。 对于运行时操作，客户可以维护应用程序使用应用服务部署槽功能的多个不同的实时版本。 | 

