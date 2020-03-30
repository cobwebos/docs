---
title: 应用程序管理：最佳实践和建议 |微软文档
description: 了解在 Azure 活动目录中管理应用程序的最佳做法和建议。 了解如何使用自动预配和发布具有应用程序代理的本地应用。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "74085286"
---
# <a name="application-management-best-practices"></a>应用程序管理最佳实践
本文包含使用自动预配以及使用应用程序代理发布本地应用在 Azure 活动目录 （Azure AD） 中管理应用程序的建议和最佳实践。

## <a name="cloud-app-and-single-sign-on-recommendations"></a>云应用和单一登录建议
| 建议 | 注释 |
| --- | --- |
| 检查 Azure AD 应用程序库中的应用  | Azure AD 具有包含数千个通过企业单一登录 （SSO） 启用的预集成应用程序。 有关特定于应用的设置指南，请参阅[SaaS 应用教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)。  | 
| 使用基于 SAML 的联合 SSO  | 当应用程序支持它时，使用基于 Azure AD 的联合基于 SAML 的 SSO，而不是基于密码的 SSO 和 ADFS。  | 
| 使用 SHA-256 进行证书签名  | 默认情况下，Azure AD 使用 SHA-256 算法对 SAML 响应进行签名。 使用 SHA-256，除非应用程序需要 SHA-1（请参阅[证书签名选项](certificate-signing-options.md)和[应用程序登录问题](application-sign-in-problem-application-error.md)。  | 
| 需要用户分配  | 默认情况下，用户可以访问企业应用程序，而无需分配给它们。 但是，如果应用程序公开角色，或者希望应用程序显示在用户的访问面板上，则需要用户分配。 （请参阅[集成应用程序的开发人员指南](developer-guidance-for-integrating-applications.md)。  | 
| 将"我的应用"访问面板部署到用户 | `https://myapps.microsoft.com`访问[面板](end-user-experiences.md)是一个基于 Web 的门户，为用户提供了为其分配的基于云的应用程序提供单个入口点。 随着添加组管理和自助服务密码重置等其他功能，用户可以在访问面板中找到它们。 请参阅[规划访问面板部署](access-panel-deployment-plan.md)。
| 使用组分配  | 如果包含在订阅中，请将组分配给应用程序，以便可以将正在进行的访问管理委派给组所有者。 （请参阅[集成应用程序的开发人员指南](developer-guidance-for-integrating-applications.md)。   | 
| 建立证书管理流程 | 签名证书的最大生存期为三年。 为了防止或尽量减少由于证书过期而导致的中断，请使用角色和电子邮件通讯组列表来确保密切监视与证书相关的更改通知。 |

## <a name="provisioning-recommendations"></a>预配建议
| 建议 | 注释 |
| --- | --- |
| 使用教程设置云应用的预配 | 有关为要添加的库应用配置的分步指南，请查看[SaaS 应用教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)。 |
| 使用预配日志（预览）监视状态 | [预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)提供有关预配服务执行的所有操作的详细信息，包括单个用户的状态。 |
| 将通讯组分配给预配通知电子邮件 | 要提高预配服务发送的关键警报的可见性，请将通讯组分配给通知电子邮件设置。 |


## <a name="application-proxy-recommendations"></a>应用程序代理建议
| 建议 | 注释 |
| --- | --- |
| 使用应用程序代理远程访问内部资源 | 建议应用程序代理允许远程用户访问内部资源，从而取代对 VPN 或反向代理的需求。 它不适合从公司网络内访问资源，因为它可能会增加延迟。
| 使用自定义域 | 为应用程序设置自定义域（请参阅[配置自定义域](application-proxy-configure-custom-domain.md)），以便用户和应用程序之间的 URL 将从网络内部或外部工作。 您还可以控制品牌并自定义 URL。  使用自定义域名时，计划从非 Microsoft 受信任的证书颁发机构获取公共证书。 Azure 应用程序代理支持标准证书 （[通配符](application-proxy-wildcard.md)） 或基于 SAN 的证书。 （请参阅[应用程序代理规划](application-proxy-deployment-plan.md)。） |
| 在部署应用程序代理之前同步用户 | 在部署应用程序代理之前，请同步本地目录中的用户标识，或直接在 Azure AD 中创建它们。 身份同步允许 Azure AD 在授予用户对应用代理发布的应用程序的访问权限之前对用户进行预身份验证。 它还提供必要的用户标识符信息来执行单一登录 （SSO）。 （请参阅[应用程序代理规划](application-proxy-deployment-plan.md)。） |
| 遵循高可用性和负载平衡的提示 | 要了解流量如何在用户、应用程序代理连接器和后端应用服务器之间流动，并获取优化性能和负载平衡的提示，请参阅[应用程序代理连接器和应用程序的高可用性和负载平衡](application-proxy-high-availability-load-balancing.md)。 |
| 使用多个连接器 | 使用两个或多个应用程序代理连接器提高弹性、可用性和扩展率（请参阅[应用程序代理连接器](application-proxy-connectors.md)）。 创建连接器组并确保每个连接器组至少有两个连接器（三个连接器是最佳的）。 |
| 查找靠近应用程序服务器的连接器服务器，并确保它们位于同一域中 | 要优化性能，请物理定位靠近应用程序服务器的连接器服务器（请参阅[网络拓扑注意事项](application-proxy-network-topology.md)）。 此外，连接器服务器和 Web 应用程序服务器应属于同一活动目录域，或者它们应跨越信任域。 具有集成 Windows 身份验证 （IWA） 和 Kerberos 约束委派 （KCD） 的 SSO 需要此配置。 如果服务器位于不同的域中，则需要使用基于资源的 SSO 委派（请参阅[KCD 对应用程序代理的单一登录](application-proxy-configure-single-sign-on-with-kcd.md)。 |
| 为连接器启用自动更新 | 为连接器启用自动更新，以进行最新功能和错误修复。 微软提供最新的连接器版本和之前的一个版本的直接支持。 （请参阅[应用程序代理版本历史记录](application-proxy-release-version-history.md)。 |
| 绕过本地代理 | 为了便于维护，请将连接器配置为绕过本地代理，以便它直接连接到 Azure 服务。 （请参阅[应用程序代理连接器和代理服务器](application-proxy-configure-connectors-with-proxy-servers.md)。 |
| 通过 Web 应用程序代理使用 Azure AD 应用程序代理 | 对于大多数本地方案，请使用 Azure AD 应用程序代理。 只有在需要 AD FS 代理服务器且无法在 Azure 活动目录中使用自定义域的情况下，才首选 Web 应用程序代理。 （请参阅[应用程序代理迁移](application-proxy-migration.md)。 |
