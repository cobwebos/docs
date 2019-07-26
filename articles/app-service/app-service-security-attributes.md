---
title: Azure 应用服务的安全属性
description: 用于评估 Azure 应用服务的安全属性的清单
services: app-service
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: app-service
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbaldwin
ms.openlocfilehash: 299262610c027529749840720f46d6dc97a07b21
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442270"
---
# <a name="security-attributes-for-azure-app-service"></a>Azure 应用服务的安全属性

本文介绍 Azure App Service 中内置的安全属性。

[!INCLUDE [Security attributes header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>预防

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 静态加密（例如服务器端加密、带客户托管密钥的服务器端加密，以及其他加密功能） | 是 | 网站文件内容存储在 Azure 存储中，后者自动对内容进行静态加密。 请参阅[静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)。<br><br>客户提供的机密会进行静态加密。 机密在存储到应用服务配置数据库中时会进行静态加密。<br><br>本地附加的磁盘可以由网站选择性地用作临时存储 (D:\local and %TMP%)。 本地附加的磁盘不进行静态加密。 |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | 客户可以将网站配置为要求将 HTTPS 用于入站流量。 请参阅博客文章[如何仅创建 Azure App Service 的 HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)。 |
| 加密密钥处理（CMK、BYOK 等）| 是 | 客户可以选择将应用程序机密存储在 Key Vault 中，在运行时检索它们。 请参阅[使用应用服务和 Azure Functions 的 Key Vault 引用（预览版）](app-service-key-vault-references.md)。|
| 列级加密（Azure 数据服务）| 不可用 | |
| 加密的 API 调用| 是 | 可以通过基于 HTTPS 的 [Azure 资源管理器](../azure-resource-manager/index.yml)调用进行管理调用，以便配置应用服务。 |

## <a name="network-segmentation"></a>网络分段

| 安全属性 | 是/否 | 说明 |
|---|---|--|
| 服务终结点支持| 是 | 目前提供适用于应用服务的预览版。 请参阅 [Azure 应用服务访问限制](app-service-ip-restrictions.md)。 |
| VNet 注入支持| 是 | 应用服务环境是专用于注入到客户虚拟网络的单个客户的应用服务的专用实现。 请参阅[应用服务环境简介](environment/intro.md)。 |
| 网络隔离和防火墙支持| 是 | 对于应用服务的公共多租户变体，客户可以配置网络 ACL（IP 限制），锁定允许的入站流量。  请参阅 [Azure 应用服务访问限制](app-service-ip-restrictions.md)。  应用服务环境直接部署到虚拟网络中，因此可以通过 NSG 来确保安全。 |
| 强制隧道支持| 是 | 应用服务环境可部署到客户的虚拟网络中, 其中配置了强制隧道。 客户需要按照[将应用服务环境配置为强制隧道](environment/forced-tunnel-support.md)中的说明进行操作。 |

## <a name="detection"></a>检测

| 安全属性 | 是/否 | 说明|
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 应用服务与支持 Application Insights (完整 .NET Framework、.NET Core、Java 和 NODE.JS) 的语言的 Application Insights 集成。  请参阅[监视 Azure App Service 性能](../azure-monitor/app/azure-web-apps.md)。 应用服务还会将应用程序指标发送到 Azure Monitor。 请参阅[在 Azure App Service 中监视应用](web-sites-monitor.md)。 |

## <a name="identity-and-access-management"></a>标识和访问管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 身份验证| 是 | 客户可以构建基于应用服务的应用程序，这些应用程序自动集成 [Azure Active Directory (Azure AD)](../active-directory/index.yml) 以及其他与 OAuth 兼容的标识提供者；请参阅 [Azure 应用服务中的身份验证和授权](overview-authentication-authorization.md)。 对应用服务资产进行管理访问时，所有访问都可以通过组合使用经 Azure AD 验证的主体和 Azure 资源管理器 RBAC 角色进行控制。 |
| Authorization| 是 | 对应用服务资产进行管理访问时，所有访问都可以通过组合使用经 Azure AD 验证的主体和 Azure 资源管理器 RBAC 角色进行控制。  |


## <a name="audit-trail"></a>审核线索

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 控制和管理平面日志记录和审核| 是 | 在应用服务对象上执行的所有管理操作都通过 [Azure 资源管理器](../azure-resource-manager/index.yml)来完成。 可以通过门户和 CLI 获取这些操作的历史日志；请参阅 [Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)和 [az monitor activity-log](/cli/azure/monitor/activity-log)。 |
| 数据平面日志记录和审核 | 否 | 应用服务的数据平面是一个远程文件共享，其中包含客户的已部署网站内容。  不对远程文件共享进行审核。 |

## <a name="configuration-management"></a>配置管理

| 安全属性 | 是/否 | 说明|
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 进行管理操作时，可以将应用服务配置的状态导出为 Azure 资源管理器模板，并在一段时间内进行版本控制。 进行运行时操作时，客户可以使用应用服务的部署槽功能保留某个应用程序的多个不同的实时版本。 | 

