---
title: 启用 Azure AD （预览版）的无密码安全密钥登录-Azure Active Directory
description: 使用 FIDO2 安全密钥（预览版）启用无密码安全密钥登录 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66102a92ce279b594d61c8b2d484c89a8532057a
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73172002"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>启用无密码安全密钥登录（预览版）

对于目前使用密码并具有共享 PC 环境的企业，安全密钥为辅助角色提供了无需输入用户名或密码即可进行身份验证的无缝方式。 安全密钥可提高工作人员的工作效率，并获得更好的安全性。

本文档重点介绍如何启用基于安全密钥的无密码身份验证。 本文末尾，你将能够使用 FIDO2 安全密钥通过 Azure AD 帐户登录到基于 web 的应用程序。

|     |
| --- |
| FIDO2 安全密钥是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>要求

- [Azure 多重身份验证](howto-mfa-getstarted.md)
- [组合安全信息注册预览](concept-registration-mfa-sspr-combined.md)
- 兼容的[FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN 需要 Windows 10 版本1809或更高版本 * *

若要使用安全密钥登录到 web 应用和服务，您必须拥有支持 WebAuthN 协议的浏览器。 其中包括 Microsoft Edge、Chrome、Firefox 和 Safari。

## <a name="prepare-devices-for-preview"></a>为预览版准备设备

要试验的设备必须运行 Windows 10 版本1809或更高版本。 最佳体验是在 Windows 10 版本1903或更高版本上。

## <a name="enable-passwordless-authentication-method"></a>启用无密码 authentication 方法

### <a name="enable-the-combined-registration-experience"></a>启用组合注册体验

无密码身份验证方法的注册功能依赖于组合注册预览。 按照[启用组合安全信息注册（预览版）](howto-registration-mfa-sspr-combined.md)一文中的步骤，启用组合注册预览。

### <a name="enable-fido2-security-key-method"></a>启用 FIDO2 安全密钥方法

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Azure Active Directory** > **安全** > **身份验证**方法 > **身份验证方法策略（预览）** 。
1. 在 "方法**FIDO2 安全密钥**" 下，选择以下选项：
   1. **启用**-是或否
   1. **目标**-所有用户或选择用户
1. **保存**配置。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 安全密钥的用户注册和管理

1. 浏览到 [https://myprofile.microsoft.com](https://myprofile.microsoft.com)。
1. 如果尚未登录，请登录。
1. 单击 "**安全信息**"。
   1. 如果用户已注册至少一个 Azure 多重身份验证方法，则可以立即注册 FIDO2 安全密钥。
   1. 如果它们没有注册至少一个 Azure 多重身份验证方法，则它们必须添加一个。
1. 通过单击 "**添加方法**" 并选择 "**安全密钥**" 来添加 FIDO2 安全密钥。
1. 选择 " **USB 设备**" 或 " **NFC 设备**"。
1. 准备好密钥，然后选择 "**下一步**"。
1. 将显示一个框，要求用户为你的安全密钥创建/输入 PIN，然后为密钥（生物识别或触摸）执行所需的笔势。
1. 用户将返回到合并的注册体验，并要求为密钥提供有意义的名称，以便用户可以识别有多个密钥的名称。 单击“下一步”。
1. 单击 "**完成**" 以完成该过程。

## <a name="sign-in-with-passwordless-credential"></a>用无密码凭据登录

在下面的示例中，用户已设置了其 FIDO2 安全密钥。 用户可以选择使用 Windows 10 1809 版或更高版本上受支持的浏览器内的 FIDO2 安全密钥登录到 web。

![安全密钥登录 Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>故障排除和反馈

若要在预览此功能时共享反馈或遇到问题，请通过 Windows 反馈中心应用进行共享。

1. 启动**反馈中心**并确保已登录。
1. 按照以下分类提交反馈：
   1. 类别：安全和隐私
   1. 子类别： FIDO
1. 若要捕获日志，请使用选项：**重新创建问题**

## <a name="known-issues"></a>已知问题

### <a name="security-key-provisioning"></a>安全密钥预配

在公共预览版中，管理员预配和取消预配安全密钥不可用。

### <a name="upn-changes"></a>UPN 更改

我们正在努力支持一项功能，该功能允许在混合 Azure AD 联接和 Azure AD 加入的设备上进行 UPN 更改。 如果用户的 UPN 发生更改，你将无法再修改 FIDO2 安全密钥来考虑此更改。 解决方法是重置设备，用户必须重新注册。

## <a name="next-steps"></a>后续步骤

[FIDO2 安全密钥 Windows 10 登录](howto-authentication-passwordless-security-key-windows.md)

[对本地资源启用 FIDO2 authentication](howto-authentication-passwordless-security-key-on-premises.md)

[了解有关设备注册的详细信息](../devices/overview.md)

[了解有关 Azure 多重身份验证的详细信息](../authentication/howto-mfa-getstarted.md)
