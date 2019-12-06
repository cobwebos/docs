---
title: 无密码安全密钥登录 Windows-Azure Active Directory
description: 使用 FIDO2 安全密钥（预览版）启用无密码安全密钥登录 Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce2b420c2124c86610058ce2f31cd6d7bf620a97
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2019
ms.locfileid: "74848453"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-preview"></a>启用无密码安全密钥登录到 Windows 10 设备（预览）

本文档重点介绍如何在 Windows 10 设备上启用基于 FIDO2 安全密钥的无密码身份验证。 本文末尾，你将能够使用 FIDO2 安全密钥登录到基于 web 的应用程序，以及使用 Azure AD 帐户登录到 Azure AD 联接的 Windows 10 设备。

|     |
| --- |
| FIDO2 安全密钥是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>要求

- [Azure 多重身份验证](howto-mfa-getstarted.md)
- [组合安全信息注册预览](concept-registration-mfa-sspr-combined.md)
- 兼容的[FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys)
- WebAuthN 需要 Windows 10 版本1809或更高版本
- [Azure AD 联接的设备](../devices/concept-azure-ad-join.md)需要 Windows 10 版本1809或更高版本
- [Microsoft Intune](https://docs.microsoft.com/intune/fundamentals/what-is-intune) （可选）
- 预配包（可选）

### <a name="unsupported-scenarios"></a>不支持的方案

- Windows Server Active Directory 域服务（AD DS） "已加入域（仅限本地）" 部署**不受支持**。
- 使用安全密钥**不支持**RDP、VDI 和 Citrix 方案。
- **不支持**使用安全密钥的 S/MIME。
- **不支持**使用安全密钥的 "运行身份"。
- **不支持**使用安全密钥登录到服务器。
- 如果你未使用安全密钥在联机时登录到你的设备，你将无法使用它来登录或脱机解锁。
- 使用包含多个 Azure AD 帐户的安全密钥登录或解锁 Windows 10 设备。 此方案将利用最后添加到安全密钥的帐户。 WebAuthN 允许用户选择他们想要使用的帐户。

## <a name="prepare-devices-for-preview"></a>为预览版准备设备

要试验的 Azure AD 联接的设备必须运行 Windows 10 版本1809或更高版本。 最佳体验是在 Windows 10 版本1903或更高版本上。

## <a name="enable-security-keys-for-windows-sign-in"></a>启用 Windows 登录的安全密钥

组织可能会选择使用以下一种或多种方法来根据组织的要求，使用安全密钥进行 Windows 登录。

- [使用 Intune 启用](#enable-with-intune)
- [面向 Intune 部署](#targeted-intune-deployment)
- [使用预配包启用](#enable-with-a-provisioning-package)

### <a name="enable-with-intune"></a>使用 Intune 启用

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Microsoft Intune** > **设备注册** > **Windows 注册** > **windows Hello 企业版** > **属性**。
1. 在 "**设置**" 下，将 "**使用安全密钥登录**" 设置为 "**已启用**"。

用于登录的安全密钥配置不依赖于配置 Windows Hello 企业版。

### <a name="targeted-intune-deployment"></a>面向 Intune 部署

若要以特定的设备组为目标以启用凭据提供程序，请通过 Intune 使用以下自定义设置。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 浏览到**Microsoft Intune** > **设备配置**" > **配置**文件 > "**创建配置文件**"。
1. 用以下设置配置新配置文件
   1. 名称： Windows 登录的安全密钥
   1. 说明：在 Windows 登录过程中启用要使用的 FIDO 安全密钥
   1. 平台： Windows 10 及更高版本
   1. 配置文件类型：自定义
   1. 自定义 OMA-URI 设置：
      1. 名称：开启 Windows 登录的 FIDO 安全密钥
      1. OMA-URI：./Device/Vendor/MSFT/PassportForWork/SecurityKey/UseSecurityKeyForSignin
      1. 数据类型：整数
      1. 值：1
1. 此策略可以分配给特定的用户、设备或组。 有关详细信息，请参阅[将用户和设备配置文件分配到 Microsoft Intune](https://docs.microsoft.com/intune/device-profile-assign)一文。

![Intune 自定义设备配置策略创建](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>使用预配包启用

对于不由 Intune 管理的设备，可以安装预配包以启用该功能。 可以从[Microsoft Store](https://www.microsoft.com/en-us/p/windows-configuration-designer/9nblggh4tx22)安装 Windows 配置设计器应用。

1. 启动 Windows 配置设计器。
1. 选择 "**文件**" > "**新建项目**"。
1. 为项目指定一个名称，并记下创建项目的路径。
1. 选择“**下一步**”。
1. 选择 "**预配包**" 作为**选定的项目工作流**，然后选择 "**下一步**"。
1. 选择**要查看和配置的设置**，然后**选择 "** **下一步**"。
1. 选择“完成”。
1. 在新创建的项目中，浏览到 "**运行时设置**" > **WindowsHelloForBusiness** > **SecurityKeys** > **UseSecurityKeyForSignIn**"。
1. 将**UseSecurityKeyForSignIn**设置为**Enabled**。
1. 选择 "**导出** > **预配包**"
1. 在 "**生成**" 窗口的 "**描述预配包**" 中保留默认值，然后选择 "**下一步**"。
1. 在 "**生成**" 窗口的 "**选择设置包的安全详细信息**" 下保留默认值，然后选择 "**下一步**"。
1. 记下或更改**生成**窗口中的路径，**选择要保存预配包的位置**，然后选择 "**下一步**"。
1. 在 "**生成预配包**" 页面上选择 "**生成**"。
1. 将创建的两个文件（ppkg 和 cat）保存到你稍后可以将它们应用到计算机的位置。
1. 按照[应用预配包一](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)文中的指导进行操作，以应用创建的预配包。

> [!NOTE]
> 运行 Windows 10 版本1809的设备还必须启用共享 PC 模式（EnableSharedPCMode）。 有关启用此有趣的信息，请参阅文章[使用 Windows 10 设置共享计算机或来宾 PC](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)。

## <a name="sign-in-to-windows-with-a-fido2-security-key"></a>使用 FIDO2 安全密钥登录 Windows

在下面的示例中，用户 Bala Sandhu 已经使用上一文章中的步骤设置了其 FIDO2 安全密钥，请[启用无密码安全密钥登录](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)。 Bala 可以从 Windows 10 锁屏界面中选择安全密钥凭据提供程序，并插入用于登录 Windows 的安全密钥。

![Windows 10 锁屏界面上的安全密钥登录](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全密钥生物识别、PIN 或重置安全密钥

* Windows 10 版本1903或更高版本
   * 用户可以在其设备上打开**Windows 设置** **帐户** > **安全密钥**
   * 用户可以更改其 PIN、更新生物识别或重置其安全密钥

## <a name="troubleshooting-and-feedback"></a>故障排除和反馈

若要在预览此功能时共享反馈或遇到问题，请通过 Windows 反馈中心应用进行共享。

1. 启动**反馈中心**并确保已登录。
1. 按照以下分类提交反馈：
   1. 类别：安全和隐私
   1. 子类别： FIDO
1. 若要捕获日志，请使用选项：**重新创建问题**

## <a name="frequently-asked-questions"></a>常见问题

### <a name="does-this-work-in-my-on-premises-environment"></a>这是在我的本地环境中吗？

此功能不适用于纯本地 Active Directory 域服务（AD DS）环境。

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>我的组织需要双重身份验证来访问资源，我可以执行哪些操作来支持此要求？

安全密钥分为多种形式。 请联系感兴趣的设备制造商，讨论如何使用 PIN 或生物识别功能来启用其设备，作为第二个因素。

### <a name="can-admins-set-up-security-keys"></a>管理员可以设置安全密钥吗？

我们正在努力提供此功能的公开上市（GA）功能。

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>在哪里可以找到符合条件的安全密钥？

[FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>如果丢失了安全密钥，我该怎么办？

可以通过导航到 "安全信息" 页并删除安全密钥，从 Azure 门户中删除密钥。

## <a name="next-steps"></a>后续步骤

[了解有关设备注册的详细信息](../devices/overview.md)

[了解有关 Azure 多重身份验证的详细信息](../authentication/howto-mfa-getstarted.md)
