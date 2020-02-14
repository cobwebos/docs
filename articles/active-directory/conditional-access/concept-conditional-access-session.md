---
title: 条件访问策略中的会话控制-Azure Active Directory
description: 什么是 Azure AD 条件访问策略中的会话控件
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
ms.openlocfilehash: 84c794e6fe751bc1c12b90353ef7b285f31a2331
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192137"
---
# <a name="conditional-access-session"></a>条件性访问：会话

在条件性访问策略中，管理员可以利用会话控制来启用特定云应用程序内的有限体验。

![具有要求多重身份验证的授权控制的条件性访问策略](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>应用程序强制实施的限制

组织可以使用此控制来要求 Azure AD 将设备信息传递到所选的云应用。 设备信息使云应用能够知道连接是从兼容设备还是已加入域设备发起的。 此控制仅支持将 SharePoint Online 和 Exchange Online 作为选定的云应用。 选择后，云应用会使用设备信息为用户提供有限或完整的体验，具体取决于设备状态。

有关使用和配置应用强制实施的限制的详细信息，请参阅以下文章：

- [使用 SharePoint Online 启用受限访问](https://docs.microsoft.com/sharepoint/control-access-from-unmanaged-devices)
- [使用 Exchange Online 启用受限访问](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>条件访问应用程序控件

条件访问应用控制使用反向代理体系结构，并且与 Azure AD 条件访问唯一集成。 Azure AD 条件性访问允许你根据某些条件在你的组织的应用上强制实施访问控制。 条件定义将条件访问策略应用到的用户（用户或用户组）和内容（哪个位置和网络）。 确定条件后，可通过应用访问和会话控制将用户路由到[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) ，你可以在其中使用条件访问应用控制来保护数据。

条件访问应用控制使用户应用访问和会话能够根据访问和会话策略实时监视和控制。 在 Cloud App Security 门户中使用访问和会话策略，以进一步优化筛选器并设置要对用户执行的操作。 使用访问和会话策略，可以：

- 阻止数据渗透：可以阻止下载、剪切、复制和打印敏感文档，例如非托管设备。
- 下载时保护：你可以要求文档通过 Azure 信息保护进行标记和保护，而不是阻止对敏感文档的下载。 此操作可确保文档受到保护，并且在可能存在风险的会话中限制用户访问。
- 禁止上传未标记的文件：在上传、分发敏感文件并使用其他文件之前，请务必确保文件具有正确的标签和保护。 可以确保在用户对内容进行分类之前，阻止上载包含敏感内容的未标记文件。
- 监视用户会话的符合性：当有风险的用户登录到应用时，将监视这些用户的操作，并从会话中记录这些用户的操作。 你可以调查和分析用户行为，以了解在什么情况下应在什么情况下应用会话策略。
- 阻止访问：可以根据多个风险因素，以粒度方式阻止特定应用和用户的访问。 例如，如果使用客户端证书作为设备管理形式，则可以阻止它们。
- 阻止自定义活动：某些应用具有有风险的独特方案，例如，在 Microsoft 团队或时差等应用中发送包含敏感内容的消息。 在这种情况下，你可以对敏感内容的消息进行扫描，并实时将其阻止。

有关详细信息，请参阅文章[部署适用于特色应用的条件访问应用控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)。

## <a name="sign-in-frequency-preview"></a>登录频率（预览）

登录频率定义在尝试访问资源时要求用户重新登录之前的时间段。

"登录频率" 设置适用于已根据标准实现了 OAUTH2 或 OIDC 协议的应用。 大多数适用于 Windows、Mac 和 Mobile 的 Microsoft 本机应用，包括以下 web 应用程序的设置。

- Word、Excel、PowerPoint Online
- OneNote Online
- Office.com
- O365 管理门户
- Exchange Online
- SharePoint 和 OneDrive
- 团队 web 客户端
- Dynamics CRM Online
- Azure 门户

有关详细信息，请参阅文章[使用条件访问配置身份验证会话管理](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)。

## <a name="persistent-browser-session-preview"></a>持久性浏览器会话（预览）

持久性浏览器会话允许用户在关闭并重新打开其浏览器窗口后保持登录。

有关详细信息，请参阅文章[使用条件访问配置身份验证会话管理](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)。

## <a name="next-steps"></a>后续步骤

- [条件访问公用策略](concept-conditional-access-policy-common.md)

- [仅限报表模式](concept-conditional-access-report-only.md)
