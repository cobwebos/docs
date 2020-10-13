---
title: 条件访问策略中的会话控制 - Azure Active Directory
description: Azure AD 条件访问策略中的会话控制是什么
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a0089d246169ad4215075662500794e7143940e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601803"
---
# <a name="conditional-access-session"></a>条件访问：会话

在条件访问策略中，管理员可以利用会话控制在特定的云应用程序中启用受限体验。

![包含授权控制且需要多重身份验证的条件访问策略](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>应用程序强制实施的限制

组织可以使用此控制要求 Azure AD 将设备信息传递给所选云应用。 设备信息使云应用能够知道连接是从兼容设备还是已加入域设备发起的。 此控制仅支持将 SharePoint Online 和 Exchange Online 作为选定的云应用。 选择后，云应用会使用设备信息为用户提供有限或完整的体验，具体取决于设备状态。

有关使用和配置应用强制实施的限制的详细信息，请参阅以下文章：

- [使用 SharePoint Online 启用受限访问](/sharepoint/control-access-from-unmanaged-devices)
- [使用 Exchange Online 启用受限访问](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>条件访问应用程序控制

条件访问应用控制使用反向代理体系结构，并以独特的方式与 Azure AD 条件访问相集成。 使用 Azure AD 条件访问可以根据某些条件在组织的应用中强制实施访问控制。 条件定义了要向谁（用户或用户组）、什么（哪些云应用）和哪里（哪些位置和网络）应用条件访问策略。 确定条件后，可将用户路由到 [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security)，在其中，可以通过应用访问和会话控制，使用条件访问应用控制来保护数据。

借助条件访问应用控制，可以根据访问和会话策略实时监视与控制用户应用访问和会话。 在 Cloud App Security 门户中使用访问和会话策略，以进一步具体化筛选器，并设置要对用户执行的操作。 使用访问和会话策略可以：

- 防止数据透露：例如，可以在非托管设备上阻止敏感文档的下载、剪切、复制和打印。
- 在下载时提供保护：如果不阻止敏感文档的下载，可以要求为文档添加标签并通过 Azure 信息保护进行保护。 此操作可确保文档受到保护，并在有潜在风险的会话中限制用户访问。
- 阻止上传不带标签的文件：在其他人上传、分发和使用敏感文件之前，必须确保文件带有适当的标签并受保护。 在用户对内容进行分类之前，可以确保阻止上传包含敏感内容的不带标签的文件。
- 监视用户会话的合规性：风险用户在登录到应用时会受到监视，并从会话内部记录其操作。 可以调查和分析用户的行为，以了解将来应在何处、在何种条件下应用会话策略。
- 阻止访问：可根据多种风险因素，以不同的粒度阻止特定应用和用户的访问。 例如，如果应用和用户使用客户端证书作为设备管理形式，则可将其阻止。
- 阻止自定义活动：某些应用的独特使用场景会带来风险，例如，在 Microsoft Teams 或 Slack 等应用中发送包含敏感内容的消息。 在此类场景中，可以扫描消息中的敏感内容并实时阻止。

有关详细信息，请参阅[为特色应用部署条件访问应用控制](/cloud-app-security/proxy-deployment-aad)一文。

## <a name="sign-in-frequency"></a>登录频率

登录频率定义在用户尝试访问资源时，要求用户重新登录之前所要经过的时限。

登录频率设置适用于已根据标准实现了 OAUTH2 或 OIDC 协议的应用。 大多数适用于 Windows、Mac 和 Mobile 的 Microsoft 本机应用（包括以下 Web 应用程序）都符合该设置。

- Word、Excel、PowerPoint Online
- OneNote Online
- Office.com
- Microsoft 365 管理门户
- Exchange Online
- SharePoint 和 OneDrive
- Teams Web 客户端
- Dynamics CRM Online
- Azure 门户

有关详细信息，请参阅文章 [使用条件访问配置身份验证会话管理](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)。

## <a name="persistent-browser-session"></a>持久性浏览器会话

持久性浏览器会话可让用户在关闭再重新打开其浏览器窗口后保持登录状态。

有关详细信息，请参阅文章 [使用条件访问配置身份验证会话管理](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)。

## <a name="next-steps"></a>后续步骤

- [条件访问常见策略](concept-conditional-access-policy-common.md)

- [“仅报表”模式](concept-conditional-access-report-only.md)
