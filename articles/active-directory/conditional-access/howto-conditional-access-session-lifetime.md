---
title: 配置身份验证会话管理-Azure Active Directory
description: 自定义 Azure AD 身份验证会话配置，包括用户登录频率和浏览器会话持久性。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 797475bfe0f1ec077ad39c6fce1f0facdf679802
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2019
ms.locfileid: "74483460"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>使用条件访问配置身份验证会话管理

在复杂部署中，组织可能需要限制身份验证会话。 部分场景包括：

* 从非托管设备或共享设备访问资源
* 从外部网络访问敏感信息
* 对用户影响很大的操作
* 业务关键型应用程序

使用条件访问控制可以创建相应的策略来满足组织中的具体用例，且不会影响到所有用户。

在深入了解如何配置策略之前，让我们了解默认的配置。

## <a name="user-sign-in-frequency"></a>用户登录频率

登录频率定义在用户尝试访问资源时，要求用户重新登录之前所要经过的时限。

Azure Active Directory (Azure AD) 的默认用户登录频率配置为 90 天滚动时限。 经常要求用户提供凭据看似很明智，但有时适得其反：平时不加思索输入凭据的用户可能会意外中收到恶意的凭据提示。

如果不要求用户重新登录，则可能会发出警报，实际上任何违反 IT 策略的情况都将吊销会话。 部分示例包括（但不限于）密码更改、设备不合规或禁用帐户。 也可以[使用 PowerShell 显式吊销用户会话](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 Azure AD 的默认配置是“如果用户会话的安全状况未发生变化，则不要求用户提供其凭据”。

登录频率设置适用于已根据标准实现了 OAUTH2 或 OIDC 协议的应用。 大多数适用于 Windows、Mac 和 Mobile 的 Microsoft 本机应用，包括以下 web 应用程序的设置。

- Word、Excel、PowerPoint Online
- OneNote Online
- Office.com
- O365 管理门户
- Exchange Online
- SharePoint 和 OneDrive
- 团队 web 客户端
- Dynamics CRM Online
- Azure 门户

## <a name="persistence-of-browsing-sessions"></a>浏览会话的持久性

持久性浏览器会话可让用户在关闭再重新打开其浏览器窗口后保持登录状态。

成功完成身份验证后，浏览器会话的 Azure AD 默认配置会显示“是否保持登录状态?”提示，让个人设备上的用户选择 是否要保留会话。 如果使用[AD FS 单一登录设置一](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
)文中的指南在 AD FS 中配置了浏览器持久性，我们将遵守该策略并同时保留 Azure AD 会话。 还可以参考 [自定义 Azure AD 登录页](../fundamentals/customize-branding.md)一文中的指导在 Azure 门户上的公司品牌窗格中更改相应的设置，来配置是否要向租户中的用户显示“是否保持登录状态?”提示。

## <a name="configuring-authentication-session-controls"></a>配置身份验证会话控制

条件访问是一项 Azure AD Premium 功能，要求安装高级版许可证。 若要详细了解条件访问，请参阅 [Azure Active Directory 中的条件访问是什么？](overview.md#license-requirements)

> [!WARNING]
> 如果使用的是公共预览版中的[可配置令牌生存期](../develop/active-directory-configurable-token-lifetimes.md)功能，请注意，我们不支持为同一用户或应用组合创建两种不同的策略：一个使用此功能，另一个具有可配置的令牌生存期功能。 Microsoft 计划在 2020 5 月1日停用可配置的令牌生存期功能，并将其替换为条件访问身份验证会话管理功能。  

### <a name="policy-1-sign-in-frequency-control"></a>策略1：登录频率控制

1. 创建新策略
1. 选择客户环境所需的所有条件，包括目标云应用。

   > [!NOTE]
   > 建议为 Exchange Online 和 SharePoint Online 等重要 Microsoft Office 应用设置相同的身份验证提示频率，以获得最佳用户体验。

1. 转到“访问控制” **“会话”，然后单击“登录频率”**  > 
1. 在第一个文本框中输入所需的天数和小时值
1. 从下拉列表中选择“小时”或“天”的值
1. 保存策略

![在条件访问策略中配置的登录频率](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

在已注册到 Azure AD 的 Windows 设备上，设备登录被视为一种提示。 例如，如果将 Office 应用的“登录频率”配置为 24 小时，则已注册到 Azure AD 的 Windows 设备上的用户符合“登录频率”策略，他们可以登录到设备，在打开 Office 应用时不再会看到提示。

如果为同一浏览器会话中运行的不同 Web 应用配置了不同的登录频率，最严格的策略将应用到这两个应用，因为在同一浏览器会话中运行的所有应用共享一个会话令牌。

### <a name="policy-2-persistent-browser-session"></a>策略2：持久性浏览器会话

1. 创建新策略
1. 选择所有所需的条件。

   > [!NOTE]
   > 请注意，此控制措施要求选择“所有云应用”作为条件。 浏览器会话持久性由身份验证会话令牌控制。 浏览器会话中的所有标签页共享一个会话令牌，因此它们都必须共享持久性状态。

1. 转到“访问控制” **“会话”，然后单击“持久性浏览器会话”**  > 
1. 从下拉列表中选择一个值
1. 保存策略

![在条件访问策略中配置的持久性浏览器](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD 条件访问中的持久性浏览器会话配置将覆盖 在 Azure 门户上的公司品牌窗格中为同一用户配置的“是否保持登录状态?”设置（如果同时配置了这两个策略）。

## <a name="validation"></a>验证

使用 What-If 工具根据策略的配置方式，来模拟用户登录目标应用程序及其他条件。 身份验证会话管理控制措施将显示在工具的结果中。

![条件访问的 What If 工具结果](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>策略部署

若要确保你的策略按预期工作，建议的最佳做法是在将其推广到生产环境之前对其进行测试。 理想情况下，使用一个测试租户来验证新策略是否按预期方式工作。 有关详细信息，请参阅 [Azure Active Directory 中的条件访问的最佳做法](best-practices.md)一文。

## <a name="next-steps"></a>后续步骤

* 若要了解如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](app-based-mfa.md) 一文。
* 如果已准备好针对环境配置条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](best-practices.md)一文。
