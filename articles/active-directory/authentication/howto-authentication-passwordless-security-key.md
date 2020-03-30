---
title: 无密码安全密钥登录（预览） - Azure 活动目录
description: 使用 FIDO2 安全密钥（预览）为 Azure AD 启用无密码安全密钥登录
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: e088d239a91edeff34ecd1a7dc5be7a9f8628da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129149"
---
# <a name="enable-passwordless-security-key-sign-in-preview"></a>启用无密码安全密钥登录（预览）

对于目前使用密码且具有共享 PC 环境的企业，安全密钥为工作人员提供了一种无缝的身份验证方式，而无需输入用户名或密码。 安全密钥提高了工作人员的工作效率，并提高了安全性。

本文档重点介绍启用基于安全密钥的无密码身份验证。 在本文末尾，您将能够使用 FIDO2 安全密钥使用 Azure AD 帐户登录到基于 Web 的应用程序。

|     |
| --- |
| FIDO2 安全密钥是 Azure 活动目录的公共预览功能。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>要求

- [Azure 多重身份验证](howto-mfa-getstarted.md)
- [组合安全信息注册预览](concept-registration-mfa-sspr-combined.md)
- 兼容[的 FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN 需要 Windows 10 版本 1809 或更高版本*

要使用安全密钥登录到 Web 应用和服务，您必须具有支持 WebAuthN 协议的浏览器。 其中包括微软边缘、Chrome、火狐和Safari。

## <a name="prepare-devices-for-preview"></a>准备设备进行预览

使用试用的 Azure AD 加入设备必须运行 Windows 10 版本 1809 或更高版本。 最好的体验是在 Windows 10 版本 1903 或更高版本中。

混合 Azure AD 加入的设备必须运行 Windows 10 内部人员生成 18945 或更新。

## <a name="enable-passwordless-authentication-method"></a>启用无密码身份验证方法

### <a name="enable-the-combined-registration-experience"></a>启用组合注册体验

无密码身份验证方法的注册功能依赖于组合的注册预览。 按照文章"[启用组合安全信息注册（预览）"](howto-registration-mfa-sspr-combined.md)中的步骤启用合并注册预览。

### <a name="enable-fido2-security-key-method"></a>启用 FIDO2 安全密钥方法

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 浏览到**Azure 活动目录** > **安全** > **身份验证方法** > **身份验证方法策略（预览）。**
1. 在**FIDO2 安全密钥**的方法下，选择以下选项：
   1. **启用**- 是 或 否
   1. **目标**- 所有用户或选择用户
1. **保存**配置。

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 安全密钥的用户注册和管理

1. 浏览到[https://myprofile.microsoft.com](https://myprofile.microsoft.com)。
1. 如果尚未登录，请登录。
1. 单击 **"安全信息**"。
   1. 如果用户已注册了至少一个 Azure 多重身份验证方法，则可以立即注册 FIDO2 安全密钥。
   1. 如果他们没有至少注册一个 Azure 多重身份验证方法，则必须添加一个。
1. 通过单击 **"添加"方法**并选择**安全密钥**，添加 FIDO2 安全密钥。
1. 选择**USB 设备**或 NFC**设备**。
1. 准备好您的密钥并选择 **"下一步**"。
1. 将显示一个框，要求用户为您的安全密钥创建/输入 PIN，然后对密钥执行所需的手势，即生物识别或触摸。
1. 用户将返回到组合注册体验，并要求为密钥提供有意义的名称，以便用户可以标识哪个具有多个名称。 单击“下一步”****。
1. 单击 **"完成"** 以完成此过程。

## <a name="sign-in-with-passwordless-credential"></a>使用无密码凭据登录

在下面的示例中，用户已预配其 FIDO2 安全密钥。 用户可以选择在 Windows 10 版本 1809 或更高版本的受支持的浏览器内使用 FIDO2 安全密钥在 Web 上登录。

![安全密钥登录微软边缘](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>故障排除和反馈

如果您想在预览此功能时共享反馈或遇到问题，请使用以下步骤通过 Windows 反馈中心应用共享：

1. 启动**反馈中心**并确保您已登录。
1. 根据以下分类提交反馈：
   - 类别：安全和隐私
   - 子类别： FIDO
1. 要捕获日志，请使用 选项**重新创建问题**

## <a name="known-issues"></a>已知问题

### <a name="security-key-provisioning"></a>安全密钥预配

公共预览版中不提供安全密钥的管理员预配和取消预配。

### <a name="upn-changes"></a>UPN 更改

我们正在努力支持一项功能，该功能允许在混合 Azure AD 联接和 Azure AD 联接设备上更改 UPN。 如果用户的 UPN 发生更改，您无法再修改 FIDO2 安全密钥以考虑更改。 解决方法是重置设备，用户必须重新注册。

## <a name="next-steps"></a>后续步骤

[FIDO2 安全密钥 Windows 10 登录](howto-authentication-passwordless-security-key-windows.md)

[对本地资源启用 FIDO2 身份验证](howto-authentication-passwordless-security-key-on-premises.md)

[了解有关设备注册的信息](../devices/overview.md)

[了解 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
