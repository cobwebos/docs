---
title: Azure AD 应用程序代理的 PowerShell 示例
description: 使用以下 PowerShell 示例 Azure AD 应用程序代理获取目录中应用程序代理应用和连接器的相关信息、将用户和组分配到应用，以及获取证书信息。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 25097f8b72c7c8dde742701ce576c1b0e2fbabd2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480939"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD 应用程序代理 Azure AD PowerShell 示例

下表包含指向 Azure AD 应用程序代理的 PowerShell 脚本示例的链接。 除非另有说明，否则这些示例需要[AzureAD V2 powershell For graph 模块](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或[AzureAD V2 powershell for graph 模块预览版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)。


有关这些示例中使用的 cmdlet 的详细信息，请参阅[应用程序代理应用程序管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)和[应用程序代理连接器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)。

| | |
|---|---|
|**应用程序代理应用**||
| [列出所有应用程序代理应用程序的基本信息](scripts/powershell-get-all-app-proxy-apps-basic.md) | 列出目录中所有应用程序代理应用的基本信息（AppId、DisplayName、ObjId）。 |
| [列出所有应用程序代理应用的扩展信息](scripts/powershell-get-all-app-proxy-apps-extended.md) | 列出目录中的所有应用程序代理应用的扩展信息（AppId、DisplayName、ExternalUrl、InternalUrl、ExternalAuthenticationType）。  |
| [按连接器组列出所有应用程序代理应用](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | 列出目录中的所有应用程序代理应用的相关信息，以及将应用分配到的连接器组。 |
| [获取具有令牌生存期策略的所有应用程序代理应用](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | 列出目录中具有令牌生存期策略及其详细信息的所有应用程序代理应用。 此示例需要[AzureAD V2 PowerShell For Graph 模块预览版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)。 |
|**连接器组**||
| [获取目录中的所有连接器组和连接器](scripts/powershell-get-all-connectors.md) | 列出目录中的所有连接器组和连接器。 |
| [将分配给连接器组的所有应用移动到另一个连接器组](scripts/powershell-move-all-apps-to-connector-group.md) | 将当前分配给连接器组的所有应用程序移到另一个连接器组。 |
|**已分配用户和组**||
| [显示分配给应用程序代理应用程序的用户和组](scripts/powershell-display-users-group-of-app.md) | 列出分配给特定应用程序代理应用程序的用户和组。 |
| [将用户分配到应用程序](scripts/powershell-assign-user-to-app.md) | 将特定用户分配到应用程序。 |
| [向应用程序分配组](scripts/powershell-assign-group-to-app.md) | 将特定组分配给应用程序。 |
|**外部 URL 配置**||
| [使用默认域（. msappproxy.net）获取所有应用程序代理应用](scripts/powershell-get-all-default-domain-apps.md)  | 列出所有使用默认域（. msappproxy.net）的应用程序代理应用程序。 |
| [使用通配符发布获取所有应用程序代理应用](scripts/powershell-get-all-wildcard-apps.md) | 使用通配符发布列出所有应用程序代理应用。 |
|**自定义域配置**||
| [使用自定义域和证书信息获取所有应用程序代理应用](scripts/powershell-get-all-custom-domains-and-certs.md) | 列出使用自定义域的所有应用程序代理应用和与自定义域关联的证书信息。 |
| [获取已发布但未上传任何证书的所有 Azure AD 代理应用程序](scripts/powershell-get-all-custom-domain-no-cert.md) | 列出所有使用自定义域但尚未上传有效 SSL 证书的应用程序代理应用。 |
| [获取用相同证书发布的所有 Azure AD 代理应用程序应用](scripts/powershell-get-custom-domain-identical-cert.md) | 列出所有用相同证书发布的 Azure AD 代理应用程序应用。 |
| [获取用相同证书发布的所有 Azure AD 代理应用程序，并将其替换](scripts/powershell-get-custom-domain-replace-cert.md) | 对于使用相同证书发布的 Azure AD 代理应用程序应用程序，允许你批量替换证书。 |
