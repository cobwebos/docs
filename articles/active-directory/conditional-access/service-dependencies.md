---
title: Azure Active Directory 条件访问中的服务依赖项是什么？ | Microsoft Docs
description: 了解如何在 Azure Active Directory 条件访问中使用条件来触发策略。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7c7f2abda282d0219dd8787a9f6a2b6c1cda15df
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2019
ms.locfileid: "71257912"
---
# <a name="what-are-service-dependencies-in-azure-active-directory-conditional-access"></a>Azure Active Directory 条件访问中的服务依赖项是什么？ 

使用条件访问策略时，可以指定网站和服务的访问要求。 例如，访问要求可以包括要求进行多重身份验证 (MFA)，或者要求使用[托管设备](require-managed-devices.md)。 

直接访问站点或服务时，通常容易评估相关策略的影响。 例如，如果策略要求对配置的 SharePoint Online 进行 MFA，则每次登录到 SharePoint Web 门户时，都会强制执行 MFA。 但是，评估某项策略的影响并非始终是直截了当的，因为某些云应用依赖于其他云应用。 例如，可以通过 Microsoft Teams 访问 SharePoint Online 中的资源。 因此，你在访问当前方案中的 Microsoft Teams 时，也会受 SharePoint MFA 策略的约束。   

## <a name="policy-enforcement"></a>策略实施 

如果配置了服务依赖项，则可使用早期绑定或后期绑定强制来应用此策略。 

- **早期绑定策略强制**意味着用户必须在访问调用应用之前满足依赖的服务策略。 例如，在登录 MS Teams 之前，用户必须满足 SharePoint 策略要求。 
- **后期绑定策略强制**发生在用户登录到调用应用以后。 强制会延迟到调用应用请求时（下游服务的令牌）。 示例包括 MS Teams 访问 Planner，以及 Office.com 访问 SharePoint。 

下图演示了 MS Teams 服务依赖关系。 Planner 的实线箭头表示早期绑定强制，虚线箭头表示后期绑定强制。 

![MS Teams 服务依赖关系](./media/service-dependencies/01.png)

最佳做法是，尽可能跨相关的应用和服务设置常用策略。 安全态势一致可以为你提供最佳用户体验。 例如，跨 Exchange Online、SharePoint Online、Microsoft Teams 和 Skype for Business 设置常用策略可以显著减少意外提示数（为下游服务应用不同策略时，可能会出现这些提示）。 

下表列出了客户端应用必须满足的其他服务依赖项  

| 客户端应用         | 下游服务                          | 强制 |
| :--                 | :--                                         | ---         | 
| Azure Data Lake     | Microsoft Azure 管理（门户和 API） | 早期绑定 |
| Microsoft Classroom | Exchange                                    | 早期绑定 |
|                     | SharePoint                                  | 早期绑定 |
| Microsoft Teams     | Exchange                                    | 早期绑定 |
|                     | MS Planner                                  | 后期绑定  |
|                     | SharePoint                                  | 早期绑定 |
|                     | Skype for Business Online                   | 早期绑定 |
| Office 门户       | Exchange                                    | 后期绑定  |
|                     | SharePoint                                  | 后期绑定  |
| Outlook 组      | Exchange                                    | 早期绑定 |
|                     | SharePoint                                  | 早期绑定 |
| PowerApps           | Microsoft Azure 管理（门户和 API） | 早期绑定 |
|                     | Microsoft Azure Active Directory              | 早期绑定 |
| 项目             | Dynamics CRM                                | 早期绑定 |
| Skype for Business  | Exchange                                    | 早期绑定 |
| Visual Studio       | Microsoft Azure 管理（门户和 API） | 早期绑定 |
| Microsoft Forms     | Exchange                                    | 早期绑定 |
|                     | SharePoint                                  | 早期绑定 |
| 微软待办     | Exchange                                    | 早期绑定 |

## <a name="next-steps"></a>后续步骤

若要了解如何在环境中实现条件访问，请参阅[在 Azure Active Directory 中规划条件访问部署](plan-conditional-access.md)。
