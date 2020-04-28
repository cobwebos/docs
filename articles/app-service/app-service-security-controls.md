---
title: 安全控件
description: 查找用于评估组织 Azure 应用服务的安全控制的清单。
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2586821c4c48f809efb5408c3cdae5e42e3b3fcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "74671455"
---
# <a name="security-controls-for-azure-app-service"></a>Azure 应用服务的安全控制

本文介绍 Azure 应用服务中内置的安全控制。

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>网络

| 安全控制 | Yes/No | 注释 | 文档
|---|---|--|
| 服务终结点支持| 是 | 适用于应用服务。| [Azure 应用服务访问限制](app-service-ip-restrictions.md)
| VNet 注入支持| 是 | 应用服务环境是对专用于单个客户的应用服务的专用实现，已注入到客户的虚拟网络中。 | [应用服务环境简介](environment/intro.md)
| 网络隔离和防火墙支持| 是 | 对于应用服务的公共多租户变体，客户可以配置网络 ACL（IP 限制），锁定允许的入站流量。  应用服务环境直接部署到虚拟网络中，因此可以通过 NSG 来确保安全。 | [Azure 应用服务访问限制](app-service-ip-restrictions.md)
| 强制隧道支持| 是 | 应用服务环境可以部署到客户的虚拟网络中，其中已配置强制隧道。 | [使用强制隧道配置应用服务环境](environment/forced-tunnel-support.md)

## <a name="monitoring--logging"></a>监视和日志记录

| 安全控制 | Yes/No | 注释 | 文档
|---|---|--|
| Azure 监视支持（Log Analytics、App Insights 等）| 是 | 应用服务通过支持 Application Insights 的语言（完整版 .NET Framework、.NET Core、Java 和 Node.js）与 Application Insights 集成。  请参阅[监视 Azure 应用服务性能](../azure-monitor/app/azure-web-apps.md)。 应用服务还会将应用程序指标发送到 Azure Monitor。 | [监视 Azure 应用服务中的应用](web-sites-monitor.md)
| 控制和管理平面日志记录和审核| 是 | 在应用服务对象上执行的所有管理操作都通过 [Azure 资源管理器](../azure-resource-manager/index.yml)来完成。 可以通过门户和 CLI 获取这些操作的历史日志。 | [Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md#microsoftweb)、[az monitor activity-log](/cli/azure/monitor/activity-log)
| 数据平面日志记录和审核 | 否 | 应用服务的数据平面是一个远程文件共享，其中包含客户的已部署网站内容。  不对远程文件共享进行审核。 |

## <a name="identity"></a>标识

| 安全控制 | Yes/No | 注释 |  文档
|---|---|--|
| 身份验证| 是 | 客户可以构建基于应用服务的应用程序，这些应用程序自动集成 [Azure Active Directory (Azure AD)](../active-directory/index.yml) 以及其他与 OAuth 兼容的标识提供者 对应用服务资产进行管理访问时，所有访问都是通过组合使用 Azure AD 身份验证主体和 Azure 资源管理器 RBAC 角色来控制的。 | [Azure 应用服务中的身份验证和授权](overview-authentication-authorization.md)
| 授权| 是 | 对应用服务资产进行管理访问时，所有访问都可以通过组合使用经 Azure AD 验证的主体和 Azure 资源管理器 RBAC 角色进行控制。  | [Azure 应用服务中的身份验证和授权](overview-authentication-authorization.md)

## <a name="data-protection"></a>数据保护

| 安全控制 | Yes/No | 注释 | 文档
|---|---|--|
| 服务器端静态加密：Microsoft 管理的密钥 | 是 | 网站文件内容存储在 Azure 存储中，后者自动对内容进行静态加密。 <br><br>客户提供的机密会进行静态加密。 机密在存储到应用服务配置数据库中时会进行静态加密。<br><br>本地附加的磁盘可以由网站选择性地用作临时存储 (D:\local and %TMP%)。 本地附加的磁盘不进行静态加密。 | [静态数据的 Azure 存储加密](../storage/common/storage-service-encryption.md)
| 服务器端静态加密：客户管理的密钥 (BYOK) | 是 | 客户可以选择将应用程序机密存储在 Key Vault 中，在运行时检索它们。 | [使用应用服务和 Azure Functions 的 Key Vault 引用（预览版）](app-service-key-vault-references.md)
| 列级加密（Azure 数据服务）| 空值 | |
| 传输中加密（例如 ExpressRoute 加密、VNet 中加密，以及 VNet-VNet 加密）| 是 | 客户可以将网站配置为要求将 HTTPS 用于入站流量。  | [How to make an Azure App Service HTTPS only](https://blogs.msdn.microsoft.com/benjaminperkins/2017/11/30/how-to-make-an-azure-app-service-https-only/)（如何将 Azure 应用服务设置为“仅限 HTTPS”）（博客文章）
| 加密的 API 调用| 是 | 可以通过基于 HTTPS 的 [Azure 资源管理器](../azure-resource-manager/index.yml)调用进行管理调用，以便配置应用服务。 |

## <a name="configuration-management"></a>配置管理

| 安全控制 | Yes/No | 注释 | 文档
|---|---|--|
| 配置管理支持（配置的版本控制等）| 是 | 进行管理操作时，可以将应用服务配置的状态导出为 Azure 资源管理器模板，并在一段时间内进行版本控制。 进行运行时操作时，客户可以使用应用服务的部署槽功能保留某个应用程序的多个不同的实时版本。 | 

## <a name="next-steps"></a>后续步骤

- 详细了解[Azure 服务中的内置安全控件](../security/fundamentals/security-controls.md)。
