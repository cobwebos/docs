---
title: 适用于 Azure AD 应用程序代理的 PowerShell 示例
description: 使用这些 Azure AD 应用程序代理的 PowerShell 示例获取有关目录中应用程序代理应用和连接器的信息、将用户和组分配给应用，以及获取证书信息。
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57efd88b9d194442920496107d4c37ba2baf94d3
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2020
ms.locfileid: "85554987"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD 应用程序代理的 Azure AD PowerShell 示例

下表包含 Azure AD 应用程序代理的 PowerShell 脚本示例的链接。 除非另有说明，否则这些示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)。


有关这些示例中使用的 cmdlet 的详细信息，请参阅[应用程序代理应用程序管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)和[应用程序代理连接器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)。

| 链接 | 说明 |
|---|---|
|应用程序代理应用||
| [列出所有应用程序代理应用的基本信息](scripts/powershell-get-all-app-proxy-apps-basic.md) | 列出有关目录中所有应用程序代理应用的基本信息（AppId、DisplayName、ObjId）。 |
| [列出所有应用程序代理应用的扩展信息](scripts/powershell-get-all-app-proxy-apps-extended.md) | 列出有关目录中所有应用程序代理应用的扩展信息（AppId、DisplayName、ExternalUrl、InternalUrl、ExternalAuthenticationType）。  |
| [按连接器组列出所有应用程序代理应用](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | 列出有关目录中所有应用程序代理应用以及应用被分配到哪个连接器组的信息。 |
| [获取具有令牌生存期策略的所有应用程序代理应用](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | 列出目录中具有令牌生存期策略的所有应用程序代理应用及其详细信息。 此示例需要[适用于 Graph 的 AzureAD V2 PowerShell 模块预览版](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)。 |
|连接器组||
| [获取目录中的所有连接器组和连接器](scripts/powershell-get-all-connectors.md) | 列出目录中的所有连接器组和连接器。 |
| [将分配到某连接器组的所有应用移动到另一个连接器组](scripts/powershell-move-all-apps-to-connector-group.md) | 将当前分配到某连接器组的所有应用程序移动到另一个连接器组。 |
|分配的用户和组||
| [显示分配到应用程序代理应用程序的用户和组](scripts/powershell-display-users-group-of-app.md) | 列出分配到特定应用程序代理应用程序的用户和组。 |
| [将用户分配到应用程序](scripts/powershell-assign-user-to-app.md) | 将特定用户分配到应用程序。 |
| [将组分配到应用程序](scripts/powershell-assign-group-to-app.md) | 将特定组分配到应用程序。 |
|外部 URL 配置||
| [获取所有使用默认域 (.msappproxy.net) 的应用程序代理应用](scripts/powershell-get-all-default-domain-apps.md)  | 列出所有使用默认域 (.msappproxy.net) 的应用程序代理应用程序。 |
| [获取所有使用通配符发布的应用程序代理应用](scripts/powershell-get-all-wildcard-apps.md) | 列出所有使用通配符发布的应用程序代理应用。 |
|自定义域配置||
| [获取所有使用自定义域和证书信息的应用程序代理应用](scripts/powershell-get-all-custom-domains-and-certs.md) | 列出所有使用自定义域和与自定义域关联的证书信息的应用程序代理应用。 |
| [获取已发布但未上传任何证书的所有 Azure AD 代理应用程序应用](scripts/powershell-get-all-custom-domain-no-cert.md) | 列出所有使用自定义域但尚未上传有效的 TLS/SSL 证书的应用程序代理应用。 |
| [获取使用相同的证书发布的所有 Azure AD 代理应用程序应用](scripts/powershell-get-custom-domain-identical-cert.md) | 列出使用相同的证书发布的所有 Azure AD 代理应用程序应用。 |
| [获取使用相同的证书发布的所有 Azure AD 代理应用程序应用，并替换该证书](scripts/powershell-get-custom-domain-replace-cert.md) | 对于使用相同证书发布的 Azure AD 代理应用程序应用，允许批量替换证书。 |
