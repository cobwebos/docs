---
title: 安全控件
description: 查找安全控制清单，用于评估组织的 Azure App Service。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671455"
---
# <a name="security-controls-for-azure-app-service"></a>Azure App Service 的安全控制

本文介绍 Azure App Service 中内置的安全控件。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 说明 | 文档
|---|---|--|
| 服务终结点支持| 是 | 适用于应用服务。| [Azure App Service 访问限制](app-service-ip-restrictions.md)
| VNet 注入支持| 是 | 应用服务环境是专用于注入到客户虚拟网络的单个客户的应用服务的专用实现。 | [应用服务环境简介](environment/intro.md)
| 网络隔离和防火墙支持| 是 | 对于应用服务的公共多租户变体，客户可以将网络 Acl （IP 限制）配置为锁定允许的入站流量。  应用服务环境直接部署到虚拟网络中，因此可以通过 Nsg 进行保护。 | [Azure App Service 访问限制](app-service-ip-restrictions.md)
| 强制隧道支持| 是 | 应用服务环境可部署到客户的虚拟网络中，其中配置了强制隧道。 | [使用强制隧道配置应用服务环境](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>监视 & 日志记录

| 安全控制 | Yes/No | 说明 | 文档
|---|---|--|
| Azure 监视支持（Log analytics、App insights 等）| 是 | 应用服务与支持 Application Insights （完整 .NET Framework、.NET Core、Java 和 NODE.JS）的语言的 Application Insights 集成。  请参阅[监视 Azure App Service 性能](../azure-monitor/app/azure-web-apps.md)。 应用服务还会将应用程序指标发送到 Azure Monitor。 | [监视 Azure 应用服务中的应用](web-sites-monitor.md)
| 控制和管理平面日志记录和审核| 是 | 对应用服务对象执行的所有管理操作都通过[Azure 资源管理器](../azure-resource-manager/index.yml)进行。 这些操作的历史日志可在门户中和通过 CLI 获得。 | [Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)， [az monitor 活动日志](/cli/azure/monitor/activity-log)
| 数据平面日志记录和审核 | No | 应用服务的数据平面是包含客户部署的网站内容的远程文件共享。  没有远程文件共享的审核。 |

## <a name="identity"></a>身份标识

| 安全控制 | Yes/No | 说明 |  文档
|---|---|--|
| Authentication| 是 | 客户可以在应用服务上构建应用程序，这些应用程序可自动与[Azure Active Directory （Azure AD）](../active-directory/index.yml)以及其他 OAuth 兼容标识提供程序集成，以便对应用服务资产进行管理访问，所有访问都由 Azure AD 身份验证的主体和 AZURE 资源管理器 RBAC 角色的组合控制。 | [Azure 应用服务中的身份验证和授权](overview-authentication-authorization.md)
| 授权| 是 | 对于对应用服务资产的管理访问权限，所有访问都由 Azure AD 身份验证的主体和 Azure 资源管理器 RBAC 角色的组合来控制。  | [Azure 应用服务中的身份验证和授权](overview-authentication-authorization.md)

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 说明 | 文档
|---|---|--|
| 服务器端加密： Microsoft 托管密钥 | 是 | 网站文件内容存储在 Azure 存储中，后者会自动加密静态内容。 <br><br>客户提供的机密静态加密。 密钥存储在应用服务配置数据库中时，静态加密。<br><br>网站（D:\local 和% TMP%）可以选择性地将本地附加的磁盘用作临时存储。 本地附加的磁盘未静态加密。 | [静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)
| 服务器端加密：客户托管的密钥（BYOK） | 是 | 客户可以选择将应用程序机密存储在 Key Vault 中，并在运行时检索它们。 | [使用应用服务和 Azure Functions 的 Key Vault 参考（预览版）](app-service-key-vault-references.md)
| 列级加密（Azure 数据服务）| N/A | |
| 传输中的加密（如 ExpressRoute 加密、VNet 加密和 VNet-VNet 加密）| 是 | 客户可以将网站配置为需要和使用 HTTPS 进行入站流量。  | [如何仅 AZURE APP SERVICE HTTPS](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/) （博客文章）
| 加密的 API 调用| 是 | 通过 HTTPS 上的[Azure 资源管理器](../azure-resource-manager/index.yml)调用来配置应用服务的管理调用。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 说明 | 文档
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 对于管理操作，可以将应用服务配置的状态导出为 Azure 资源管理器模板，并随时间进行版本控制。 对于运行时操作，客户可以使用应用服务部署槽功能来维护应用程序的多个不同的实时版本。 | 

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。
