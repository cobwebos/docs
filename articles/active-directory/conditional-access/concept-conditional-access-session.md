---
title: 条件访问策略中的会话控件 - Azure 活动目录
description: Azure AD 条件访问策略中的会话控件是什么
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
ms.openlocfilehash: e99b9b87f939d614679fdecf24c9d36d99bf2938
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671877"
---
# <a name="conditional-access-session"></a>条件访问：会话

在条件访问策略中，管理员可以使用会话控件在特定云应用程序中启用有限的体验。

![具有需要多重身份验证的授予控制的条件访问策略](./media/concept-conditional-access-session/conditional-access-session.png)

## <a name="application-enforced-restrictions"></a>应用程序强制限制

组织可以使用此控件要求 Azure AD 将设备信息传递到选定的云应用。 设备信息使云应用能够知道连接是从兼容设备还是已加入域设备发起的。 此控制仅支持将 SharePoint Online 和 Exchange Online 作为选定的云应用。 选择后，云应用会使用设备信息为用户提供有限或完整的体验，具体取决于设备状态。

有关应用强制限制的使用和配置的详细信息，请参阅以下文章：

- [使用 SharePoint Online 启用受限访问](/sharepoint/control-access-from-unmanaged-devices)
- [使用 Exchange Online 启用受限访问](https://aka.ms/owalimitedaccess)

## <a name="conditional-access-application-control"></a>条件访问应用程序控制

条件访问应用控件使用反向代理体系结构，并且与 Azure AD 条件访问唯一集成。 使用 Azure AD 条件访问可以根据某些条件在组织的应用中强制实施访问控制。 条件定义了要向谁（用户或用户组）、什么（哪些云应用）和哪里（哪些位置和网络）应用条件访问策略。 确定条件后，可以将用户路由到[Microsoft 云应用安全，](/cloud-app-security/what-is-cloud-app-security)通过应用访问和会话控件，您可以使用条件访问应用控制保护数据。

借助条件访问应用控制，可根据访问和会话策略实时监视并控制用户应用访问和会话。 在 Cloud App Security 门户中使用访问和会话策略，以进一步优化筛选器并设置要对用户执行的操作。 使用访问和会话策略，可以：

- 防止数据渗漏：您可以阻止在非托管设备上下载、剪切、复制和打印敏感文档。
- 保护下载：您可以要求使用 Azure 信息保护标记和保护文档，而不是阻止敏感文档的下载。 此操作可确保文档受到保护，并在存在潜在风险的会话中限制用户访问。
- 防止上载未标记的文件：在敏感文件被他人上载、分发和使用之前，请务必确保该文件具有正确的标签和保护。 您可以确保在用户对内容进行分类之前，阻止上载包含敏感内容的未标记文件。
- 监视用户会话的合规性：风险用户在登录到应用时受到监视，并且其操作从会话中记录。 你可调查和分析用户行为，了解将来应用会话策略的位置和条件。
- 阻止访问：您可以根据多种风险因素，精细地阻止特定应用和用户的访问。 例如，如果它们使用客户端证书作为设备管理的一种形式，则可以阻止它们。
- 阻止自定义活动：某些应用具有具有风险的独特方案，例如，在 Microsoft Teams 或 Slack 等应用中发送包含敏感内容的消息。 在此类方案中，您可以扫描消息以寻找敏感内容并实时阻止它们。

有关详细信息，请参阅为[特色应用部署条件访问应用控件](/cloud-app-security/proxy-deployment-aad)的文章。

## <a name="sign-in-frequency-preview"></a>登录频率（预览）

登录频率定义在用户尝试访问资源时，要求用户重新登录之前所要经过的时限。

登录频率设置适用于已根据标准实现了 OAUTH2 或 OIDC 协议的应用。 大多数适用于 Windows、Mac 和 Mobile 的 Microsoft 本机应用（包括以下 Web 应用程序）都符合该设置。

- Word、Excel、PowerPoint Online
- OneNote Online
- Office.com
- O365 管理门户
- Exchange Online
- SharePoint 和 OneDrive
- Teams Web 客户端
- Dynamics CRM Online
- Azure 门户

有关详细信息，请参阅文章["使用条件访问配置身份验证会话管理](howto-conditional-access-session-lifetime.md#user-sign-in-frequency)"。

## <a name="persistent-browser-session-preview"></a>持久浏览器会话（预览）

持久性浏览器会话可让用户在关闭再重新打开其浏览器窗口后保持登录状态。

有关详细信息，请参阅文章["使用条件访问配置身份验证会话管理](howto-conditional-access-session-lifetime.md#persistence-of-browsing-sessions)"。

## <a name="next-steps"></a>后续步骤

- [条件访问常见策略](concept-conditional-access-policy-common.md)

- [“仅报表”模式](concept-conditional-access-report-only.md)
