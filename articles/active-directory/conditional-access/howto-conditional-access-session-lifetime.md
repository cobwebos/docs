---
title: 使用 Azure Active Directory 条件访问配置身份验证会话管理
description: 自定义 Azure AD authentication 会话配置, 包括用户登录频率和浏览器会话暂留。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05c93c9fe2b34ae3b87c44608cc5c5c8947ecc73
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68499849"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>使用条件访问配置身份验证会话管理

在复杂的部署中, 组织可能需要限制身份验证会话。 某些情况可能包括:

* 从非托管设备或共享设备访问资源
* 从外部网络访问敏感信息
* 高影响用户
* 关键业务应用程序

条件性访问控制允许您创建针对组织中特定用例的策略, 而不会影响所有用户。

在深入了解有关如何配置策略的详细信息之前, 让我们先查看默认配置。

## <a name="user-sign-in-frequency"></a>用户登录频率

登录频率定义在尝试访问资源时要求用户重新登录之前的时间段。

用户登录频率的 Azure Active Directory (Azure AD) 默认配置为90天的滚动窗口。 要求用户提供凭据通常是一项明智的事情, 但它可以 backfire: 训练有素的用户输入其凭据, 而不想让他们无意中将其提供给恶意凭据提示。

在实际情况下, 可能会发出警报, 要求用户重新登录90天, 实际上任何违反 IT 策略的情况都将撤消会话。 一些示例包括 (但不限于) 密码更改、incompliant 设备或帐户禁用。 还可以[使用 PowerShell 显式撤销用户会话](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0)。 Azure AD 默认配置会出现 "不要求用户在其会话的安全状况未更改的情况下提供其凭据"。

"登录频率" 设置适用于已根据标准实现了 OAUTH2 或 OIDC 协议的应用。 大多数适用于 Windows、Mac 和 Mobile 的 Microsoft 本机应用都符合此设置。

## <a name="persistence-of-browsing-sessions"></a>浏览会话的持久性

持久性浏览器会话允许用户在关闭并重新打开其浏览器窗口后保持登录。

浏览器会话暂留 Azure AD 默认值允许个人设备上的用户通过显示 "保持登录状态" 来选择是否持久保存会话 身份验证成功后的提示。 如果使用 AD FS 单一登录设置 [文中的指南在 AD FS 中配置了浏览器持久性](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
) , 我们将遵守该策略并同时保留 Azure AD 会话。 你还可以配置租户中的用户是否能看到 "保持登录？" 通过使用[自定义 Azure AD 登录页](../fundamentals/customize-branding.md)一文中的指南, 在 Azure 门户中更改 "公司品牌" 窗格中的相应设置来提示。

## <a name="configuring-authentication-session-controls"></a>配置身份验证会话控件

条件性访问是 Azure AD Premium 功能, 需要高级许可证。 如果要了解有关条件性访问的详细信息, 请参阅[什么是 Azure Active Directory 中的条件性访问？](overview.md#license-requirements)

> [!WARNING]
> 如果使用的是公共预览版中的[可配置令牌生存期](../develop/active-directory-configurable-token-lifetimes.md)功能, 请注意, 我们不支持为同一用户或应用组合创建两种不同的策略: 一个使用此功能, 另一个具有可配置的令牌生存期功能。 Microsoft 计划在11月1日停用可配置的令牌生存期功能, 并将其替换为条件访问身份验证会话管理功能。  

### <a name="policy-1-sign-in-frequency-control"></a>策略 1：登录频率控制

1. 创建新策略
1. 选择客户的环境所需的所有条件, 包括目标云应用。

   > [!NOTE]
   > 建议为密钥 Microsoft Office 应用 (如 Exchange Online 和 SharePoint Online) 设置相同的身份验证提示频率, 以获得最佳用户体验。

1. 中转到 "**访问控制** > "**会话**, 并单击 "**登录频率**"
1. 在第一个文本框中输入所需的 "天" 和 "小时" 值
1. 从下拉列表中选择 "**小时**" 或 "**天**" 值
1. 保存策略

![为登录频率配置的条件访问策略](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

在 Azure AD 注册的 Windows 设备上登录到设备被视为提示。 例如, 如果已将登录频率配置为24小时 (对于 Office 应用), 则 Azure AD 注册的 Windows 设备上的用户将通过登录设备满足登录频率策略, 并在打开 Office 应用时不会再次提示。

如果为在同一浏览器会话中运行的不同 web 应用配置了不同的登录频率, 最严格的策略将应用于这两个应用, 因为在同一浏览器会话中运行的所有应用共享一个会话令牌。

### <a name="policy-2-persistent-browser-session"></a>策略 2：持久性浏览器会话

1. 创建新策略
1. 选择所有所需的条件。

   > [!NOTE]
   > 请注意, 此控件要求选择 "所有云应用" 作为条件。 浏览器会话持久性由身份验证会话令牌控制。 浏览器会话中的所有选项卡共享一个会话令牌, 因此它们都必须共享持久性状态。

1. 中转到 "**访问控制** > "**会话**, 然后单击 "**持久浏览器会话**"
1. 从下拉列表中选择一个值
1. 保存策略

![为持久性浏览器配置的条件访问策略](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Azure AD 条件访问中的持久性浏览器会话配置将覆盖 "保持登录？" 如果已配置这两个策略, 则在同一用户的 Azure 门户中的 "公司品牌" 窗格中设置。

## <a name="validation"></a>验证

使用 "假设" 工具, 根据策略的配置方式, 将用户的登录名模拟到目标应用程序以及其他条件。 身份验证会话管理控件将在工具的结果中显示。

![What If 工具结果的条件性访问](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>策略部署

若要确保你的策略按预期工作，建议的最佳做法是在将其推广到生产环境之前对其进行测试。 理想情况下，使用一个测试租户来验证新策略是否按预期方式工作。 有关详细信息, 请参阅[Azure Active Directory 中条件性访问的最佳做法](best-practices.md)。

## <a name="next-steps"></a>后续步骤

* 如果你想要了解如何配置条件性访问策略, 请参阅[使用 Azure Active Directory 条件性访问的特定应用的 MFA](app-based-mfa.md)。
* 如果已准备好为环境配置条件访问策略, 请参阅[Azure Active Directory 中条件性访问的最佳做法](best-practices.md)。
