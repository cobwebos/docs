---
title: 应用程序管理：最佳实践和建议 |Microsoft Docs
description: 了解有关在 Azure Active Directory 中管理应用程序的最佳做法和建议。 了解如何使用自动预配并使用应用程序代理发布本地应用。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/13/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6534efb6fcd07ee3b9f3979cabf2feb77496a8b8
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085286"
---
# <a name="application-management-best-practices"></a>应用程序管理最佳做法
本文包含有关使用自动预配在 Azure Active Directory （Azure AD）中管理应用程序、使用应用程序代理发布本地应用的建议和最佳实践。

## <a name="cloud-app-and-single-sign-on-recommendations"></a>Cloud app and 单一登录建议
| 建议 | 注释 |
| --- | --- |
| 检查应用的 Azure AD 应用程序库  | Azure AD 有一个库，其中包含数千个使用企业单一登录（SSO）启用的预先集成的应用程序。 有关特定于应用的安装指南，请参阅[SaaS 应用教程的列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)。  | 
| 使用基于 SAML 的联合 SSO  | 当应用程序支持该应用程序时，将基于 SAML 的联合 SSO 与 Azure AD 而不是基于密码的 SSO 和 ADFS 结合使用。  | 
| 使用 SHA-256 进行证书签名  | 默认情况下，Azure AD 使用 SHA-256 算法为 SAML 响应签名。 使用 SHA-256，除非应用程序要求使用 SHA-1 （请参阅[证书签名选项](certificate-signing-options.md)和[应用程序登录问题](application-sign-in-problem-application-error.md)）。  | 
| 需要用户分配  | 默认情况下，用户可以访问你的企业应用程序，而无需将其分配给他们。 但是，如果应用程序公开角色或者希望应用程序显示在用户的访问面板上，则需要用户分配。 （请参阅[开发人员集成应用程序指南](developer-guidance-for-integrating-applications.md)。）  | 
| 将我的应用访问面板部署到用户 | `https://myapps.microsoft.com` 的[访问面板](end-user-experiences.md)是一个基于 web 的门户，它为用户提供分配给基于云的应用程序的单一入口点。 添加了组管理和自助密码重置等附加功能后，用户可以在访问面板中找到它们。 请参阅[计划访问面板部署](access-panel-deployment-plan.md)。
| 使用组分配  | 如果订阅中包含组，则将组分配给应用程序，以便可以将正在进行的访问管理委派给组所有者。 （请参阅[开发人员集成应用程序指南](developer-guidance-for-integrating-applications.md)。）   | 
| 建立管理证书的过程 | 签名证书的最长生存期为三年。 若要防止或最大程度地减少因证书过期而导致的故障，请使用角色和电子邮件通讯组列表，以确保密切监视与证书相关的更改通知。 |

## <a name="provisioning-recommendations"></a>预配建议
| 建议 | 注释 |
| --- | --- |
| 使用教程通过云应用设置预配 | 请查看[SaaS 应用教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)，以获取有关为要添加的库应用配置预配的分步指导。 |
| 使用预配日志（预览版）监视状态 | [预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)提供了预配服务执行的所有操作的详细信息，包括各个用户的状态。 |
| 将通讯组分配给预配通知电子邮件 | 若要增加预配服务发送的关键警报的可见性，请将通讯组分配给通知电子邮件设置。 |


## <a name="application-proxy-recommendations"></a>应用程序代理建议
| 建议 | 注释 |
| --- | --- |
| 使用应用程序代理远程访问内部资源 | 建议将应用程序代理用于向远程用户提供对内部资源的访问权限，而不需要使用 VPN 或反向代理。 它不用于从企业网络访问资源，因为它可能会增加延迟。
| 使用自定义域 | 为你的应用程序设置自定义域（请参阅[配置自](application-proxy-configure-custom-domain.md)定义域），以便用户和应用程序之间的 url 可以从你的网络内部或外部运行。 你还可以控制你的品牌并自定义你的 Url。  使用自定义域名时，计划从非 Microsoft 可信证书颁发机构获取公共证书。 Azure 应用程序 Proxy 支持标准、（[通配符](application-proxy-wildcard.md)）或基于 SAN 的证书。 （请参阅[应用程序代理计划](application-proxy-deployment-plan.md)。） |
| 在部署应用程序代理之前同步用户 | 在部署应用程序代理之前，请从本地目录同步用户标识，或直接在 Azure AD 中创建用户标识。 标识同步允许 Azure AD 在向用户授予应用程序代理发布的应用程序的访问权限之前对用户进行预身份验证。 它还提供了执行单一登录（SSO）所需的用户标识符信息。 （请参阅[应用程序代理计划](application-proxy-deployment-plan.md)。） |
| 遵循我们的提示以实现高可用性和负载平衡 | 若要了解通信如何在用户、应用程序代理连接器和后端应用服务器之间流动，以及如何获取优化性能和负载均衡的技巧，请参阅[应用程序代理连接器和应用程序的高可用性和负载平衡](application-proxy-high-availability-load-balancing.md)。 |
| 使用多个连接器 | 使用两个或更多应用程序代理连接器以获得更高的复原能力、可用性和规模（请参阅[应用程序代理连接器](application-proxy-connectors.md)）。 创建连接器组，并确保每个连接器组至少有两个连接器（三个连接器是最佳的）。 |
| 找到靠近应用程序服务器的连接器服务器，并确保它们位于同一个域中 | 若要优化性能，请以物理方式将连接器服务器定位到应用程序服务器附近（请参阅[网络拓扑注意事项](application-proxy-network-topology.md)）。 此外，连接器服务器和 web 应用程序服务器应属于同一个 Active Directory 域，或者它们应该跨越信任域。 具有集成 Windows 身份验证（IWA）和 Kerberos 约束委派（KCD）的 SSO 需要此配置。 如果服务器在不同的域中，则需要为 SSO 使用基于资源的委派（请参阅[KCD 以便使用应用程序代理进行单一登录](application-proxy-configure-single-sign-on-with-kcd.md)）。 |
| 为连接器启用自动更新 | 为连接器启用自动更新，以获取最新功能和 bug 修复。 Microsoft 为最新连接器版本和之前的版本提供直接支持。 （请参阅[应用程序代理发布版本历史记录](application-proxy-release-version-history.md)。） |
| 跳过本地代理 | 为了便于维护，请将连接器配置为绕过本地代理，使其直接连接到 Azure 服务。 （请参阅[应用程序代理连接器和代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。） |
| 使用 Web 应用程序代理上的 Azure AD 应用程序代理 | 对于大多数本地方案，请使用 Azure AD 应用程序代理。 Web 应用程序代理仅适用于需要 AD FS 代理服务器和无法在 Azure Active Directory 中使用自定义域的情况。 （请参阅[应用程序代理迁移](application-proxy-migration.md)。） |
