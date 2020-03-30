---
title: 用于 Azure AD 应用程序代理的 PowerShell 示例
description: 使用这些适用于 Azure AD 应用程序代理的 PowerShell 示例来获取有关目录中的应用程序代理应用和连接器的信息，将用户和组分配给应用，以及获取证书信息。
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
ms.openlocfilehash: 476bdfdd2c0b81d86900cee18024cc261ee80a07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481256"
---
# <a name="azure-ad-powershell-examples-for-azure-ad-application-proxy"></a>Azure AD 电源外壳示例，用于 Azure AD 应用程序代理

下表包括指向 Azure AD 应用程序代理的 PowerShell 脚本示例的链接。 这些示例需要[用于图形模块的 AzureAD V2 PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或[用于图形模块预览版本的 AzureAD V2 PowerShell，](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)除非另有说明。


有关这些示例中使用的 cmdlet 的详细信息，请参阅[应用程序代理应用程序管理和](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)[应用程序代理连接器管理](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)。

| | |
|---|---|
|**应用程序代理应用**||
| [列出所有应用程序代理应用的基本信息](scripts/powershell-get-all-app-proxy-apps-basic.md) | 列出有关目录中所有应用程序代理应用的基本信息（AppId、显示名称、ObjId）。 |
| [列出所有应用程序代理应用的扩展信息](scripts/powershell-get-all-app-proxy-apps-extended.md) | 列出有关目录中所有应用程序代理应用的扩展信息（AppId、显示名称、外部 Url、内部 Url、外部身份验证类型）。  |
| [按连接器组列出所有应用程序代理应用](scripts/powershell-get-all-app-proxy-apps-by-connector-group.md) | 列出有关目录中所有应用程序代理应用以及应用分配给哪个连接器的信息。 |
| [使用令牌生存期策略获取所有应用程序代理应用](scripts/powershell-get-all-app-proxy-apps-with-policy.md) | 列出目录中包含令牌生存期策略的所有应用程序代理应用及其详细信息。 此示例需要[AzureAD V2 PowerShell 用于图形模块预览版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)。 |
|**连接器组**||
| [获取目录中的所有连接器组和连接器](scripts/powershell-get-all-connectors.md) | 列出目录中的所有连接器组和连接器。 |
| [将分配给连接器组的所有应用移动到其他连接器组](scripts/powershell-move-all-apps-to-connector-group.md) | 将当前分配给连接器组的所有应用程序移动到其他连接器组。 |
|**分配的用户和组**||
| [显示分配给应用程序代理应用程序的用户和组](scripts/powershell-display-users-group-of-app.md) | 列出分配给特定应用程序代理应用程序的用户和组。 |
| [将用户分配给应用程序](scripts/powershell-assign-user-to-app.md) | 将特定用户分配给应用程序。 |
| [将组分配给应用程序](scripts/powershell-assign-group-to-app.md) | 将特定组分配给应用程序。 |
|**外部 URL 配置**||
| [使用默认域获取所有应用程序代理应用 （.msappproxy.net）](scripts/powershell-get-all-default-domain-apps.md)  | 使用默认域 （.msappproxy.net） 列出所有应用程序代理应用程序。 |
| [使用通配符发布获取所有应用程序代理应用](scripts/powershell-get-all-wildcard-apps.md) | 使用通配符发布列出所有应用程序代理应用。 |
|**自定义域配置**||
| [使用自定义域和证书信息获取所有应用程序代理应用](scripts/powershell-get-all-custom-domains-and-certs.md) | 列出使用自定义域的所有应用程序代理应用以及与自定义域关联的证书信息。 |
| [获取发布的所有 Azure AD 代理应用程序应用程序，无需上载证书](scripts/powershell-get-all-custom-domain-no-cert.md) | 列出使用自定义域但未上载有效 TLS/SSL 证书的所有应用程序代理应用。 |
| [使用相同证书发布所有 Azure AD 代理应用程序应用程序](scripts/powershell-get-custom-domain-identical-cert.md) | 列出使用相同证书发布的所有 Azure AD 代理应用程序。 |
| [获取使用相同证书发布的所有 Azure AD 代理应用程序应用程序并替换它](scripts/powershell-get-custom-domain-replace-cert.md) | 对于使用相同证书发布的 Azure AD 代理应用程序应用，允许您批量替换证书。 |
