---
title: 无密码安全密钥登录 Windows - Azure 活动目录
description: 了解如何使用 FIDO2 安全密钥（预览）将无密码安全密钥登录 Azure 活动目录
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/30/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6ef244a887e75a0d8b9bb663d5325a33cd1e89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263902"
---
# <a name="enable-passwordless-security-key-sign-in-to-windows-10-devices-with-azure-active-directory-preview"></a>使用 Azure 活动目录（预览）为 Windows 10 设备启用无密码安全密钥登录

本文档重点介绍对 Windows 10 设备启用基于 FIDO2 安全密钥的无密码身份验证。 在本文末尾，您将能够使用 FIDO2 安全密钥登录到 Azure AD 和混合 Azure AD 与 Azure AD 帐户联接的 Windows 10 设备。

|     |
| --- |
| FIDO2 安全密钥是 Azure 活动目录的公共预览功能。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="requirements"></a>要求

| 设备类型 | 已加入 Azure AD | 已加入混合 Azure AD |
| --- | --- | --- |
| [Azure 多重身份验证](howto-mfa-getstarted.md) | X | X |
| [组合安全信息注册预览](concept-registration-mfa-sspr-combined.md) | X | X |
| 兼容[的 FIDO2 安全密钥](concept-authentication-passwordless.md#fido2-security-keys) | X | X |
| WebAuthN 需要 Windows 10 版本 1809 或更高版本 | X | X |
| [Azure AD 加入的设备](../devices/concept-azure-ad-join.md)需要 Windows 10 版本 1903 或更高版本 | X |   |
| [混合 Azure AD 加入的设备](../devices/concept-azure-ad-join-hybrid.md)需要 Windows 10 内部内部版本 18945 或更高版本 |   | X |
| 完全修补的 Windows 服务器 2016/2019 域控制器。 |   | X |
| [Azure AD 连接](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)版本 1.4.32.0 或更高版本 |   | X |
| [微软Intune（](https://docs.microsoft.com/intune/fundamentals/what-is-intune)可选） | X | X |
| 预配包（可选） | X | X |
| 组策略（可选） |   | X |

### <a name="unsupported-scenarios"></a>不支持的方案

不支持以下方案：

- Windows 服务器活动目录域服务 （AD DS） 域加入（仅限本地设备）部署。
- 使用安全密钥的 RDP、VDI 和 Citrix 方案。
- 使用安全密钥的 S/MIME。
- 使用安全密钥"以样运行"。
- 使用安全密钥登录到服务器。
- 如果联机时未使用安全密钥登录设备，则不能使用它脱机登录或解锁。
- 使用包含多个 Azure AD 帐户的安全密钥登录或解锁 Windows 10 设备。 此方案利用添加到安全密钥的最后一个帐户。 WebAuthN 允许用户选择要使用的帐户。
- 解锁运行 Windows 10 版本 1809 的设备。 为了获得最佳体验，请使用 Windows 10 版本 1903 或更高版本。

## <a name="prepare-devices-for-preview"></a>准备设备进行预览

在功能预览期间试用的 Azure AD 加入设备必须运行 Windows 10 版本 1809 或更高版本。 最好的体验是在 Windows 10 版本 1903 或更高版本中。

混合 Azure AD 加入的设备必须运行 Windows 10 内部人员生成 18945 或更新。

## <a name="enable-security-keys-for-windows-sign-in"></a>为 Windows 登录启用安全密钥

组织可以选择使用以下一种或多种方法，根据组织的要求，启用 Windows 登录的安全密钥：

- [使用 Intune 启用](#enable-with-intune)
- [目标 Intune 部署](#targeted-intune-deployment)
- [使用预配包启用](#enable-with-a-provisioning-package)
- [使用组策略启用（仅限混合 Azure AD 联接设备）](#enable-with-group-policy)

> [!IMPORTANT]
> 具有**混合 Azure AD 加入设备的**组织**还必须**在 Windows 10 FIDO2 安全密钥身份验证工作之前完成本文中的步骤，即在 Windows 10 FIDO2 安全密钥身份验证工作之前[，将 FIDO2 身份验证启用到本地资源](howto-authentication-passwordless-security-key-on-premises.md)。
>
> 具有**Azure AD 加入设备的**组织必须先执行此操作，然后才能使用 FIDO2 安全密钥对其设备对本地资源进行身份验证。

### <a name="enable-with-intune"></a>使用 Intune 启用

要启用使用 Intune 的安全密钥，请完成以下步骤：

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 浏览到**微软Intune** > **设备注册** > **Windows注册** > **Windows你好商业** > **属性**。
1. 在 **"设置"** 下，将 **"使用安全密钥的登录****"设置为"已启用**"。

用于登录的安全密钥的配置不依赖于为企业配置 Windows Hello。

### <a name="targeted-intune-deployment"></a>目标 Intune 部署

要定位特定设备组以启用凭据提供程序，请通过 Intune 使用以下自定义设置：

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 浏览到**微软Intune** > **设备配置文件** > **Profiles** > **创建配置文件**。
1. 使用以下设置配置新配置文件：
   - 名称：Windows 登录的安全密钥
   - 说明：使 FIDO 安全密钥在 Windows 登录期间使用
   - 平台：Windows 10 及更高版本
   - 配置文件类型：自定义
   - 自定义 OMA-URI 设置：
      - 名称：打开 Windows 登录的 FIDO 安全密钥
      - OMA-URI： ./设备/供应商/MSFT/工作/安全密钥/使用安全键
      - 数据类型：整数
      - 值：1
1. 此策略可以分配给特定用户、设备或组。 有关详细信息，请参阅在[Microsoft Intune 中分配用户和设备配置文件](https://docs.microsoft.com/intune/device-profile-assign)。

![Intune 自定义设备配置策略创建](./media/howto-authentication-passwordless-security-key/intune-custom-profile.png)

### <a name="enable-with-a-provisioning-package"></a>使用预配包启用

对于 Intune 未管理的设备，可以安装预配包以启用该功能。 Windows 配置设计器应用可以从 Microsoft[应用商店](https://www.microsoft.com/p/windows-configuration-designer/9nblggh4tx22)安装。 完成以下步骤以创建预配包：

1. 启动 Windows 配置设计器。
1. 选择 **"文件** > **新项目**"。
1. 为项目命名并记下创建项目的路径，然后选择 **"下一步**"。
1. 将*预配包*保留为 **"选定项目工作流**"，然后选择 **"下一步**"。
1. 选择 **"选择要查看和配置哪些设置**"下*的所有 Windows 桌面版本*，然后选择 **"下一步**"。
1. 选择“完成”****。
1. 在新创建的项目中，浏览到**运行时设置** > **WindowsHelloFor 业务** > **安全密钥** > **使用安全密钥ForSignin。**
1. 将 **"使用安全密钥"设置为***已启用*。
1. 选择**导出** > **预配包**
1. 在 **"描述预配包**"下的 **"生成**"窗口中保留默认值，然后选择 **"下一步**"。
1. 在 **"选择预配包的安全详细信息**"下的 **"生成**"窗口中保留默认值，然后选择 **"下一步**"。
1. 记下或更改 **"选择"** 下 **"生成**"窗口中的路径，并选择"**下一步**"。
1. 在 **"生成预配包**"页上选择 **"生成**"。
1. 将创建的两个文件 *（ppkg*和*cat）* 保存到以后可以将它们应用于计算机的位置。
1. 要应用您创建的预配包，请参阅[应用预配包](https://docs.microsoft.com/windows/configuration/provisioning-packages/provisioning-apply-package)。

> [!NOTE]
> 运行 Windows 10 版本 1809 的设备还必须启用共享 PC 模式 （*启用共享PC模式*）。 有关启用此功能的详细信息，请参阅[使用 Windows 10 设置共享电脑或来宾电脑](https://docs.microsoft.com/windows/configuration/set-up-shared-or-guest-pc)。

### <a name="enable-with-group-policy"></a>使用组策略启用

对于**混合 Azure AD 联接设备**，组织可以配置以下组策略设置以启用 FIDO 安全密钥登录。 可在**计算机配置** > **管理模板** > **系统** > **登录** > 下找到该设置**打开安全密钥登录**：

- 将此策略设置为 **"已启用"** 允许用户使用安全密钥登录。
- 将此策略设置为 **"已禁用****"或"未配置"** 会阻止用户使用安全密钥登录。

此组策略设置需要`credentialprovider.admx`组策略模板的更新版本。 此新模板可用于下一版本的 Windows 服务器和 Windows 10 20H1。 此设置可以通过运行这些较新版本 Windows 的设备进行管理，也可以按照支持主题"[如何在 Windows 中创建和管理组策略管理模板的中央存储](https://support.microsoft.com/help/3087759/how-to-create-and-manage-the-central-store-for-group-policy-administra)"中的指南进行集中管理。

## <a name="sign-in-with-fido2-security-key"></a>使用 FIDO2 安全密钥登录

在下面的示例中，一个名为 Bala Sandhu 的用户已经使用上一篇文章中的步骤"[启用无密码安全密钥登录](howto-authentication-passwordless-security-key.md#user-registration-and-management-of-fido2-security-keys)"中预配了其 FIDO2 安全密钥。 对于混合 Azure AD 联接设备，请确保也[启用了无密码安全密钥登录到本地资源](howto-authentication-passwordless-security-key-on-premises.md)。 Bala 可以从 Windows 10 锁屏界面中选择安全密钥凭据提供程序，并将安全密钥插入到 Windows 中签名。

![Windows 10 锁屏界面的安全密钥登录](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-sign-in-lock-screen.png)

### <a name="manage-security-key-biometric-pin-or-reset-security-key"></a>管理安全密钥生物识别、PIN 或重置安全密钥

* Windows 10 版本 1903 或更高版本
   * 用户可以在其设备上打开**Windows 设置**>**帐户** > **安全密钥**
   * 用户可以更改其 PIN、更新生物识别或重置其安全密钥

## <a name="troubleshooting-and-feedback"></a>故障排除和反馈

如果您想在预览此功能时共享反馈或遇到问题，请使用以下步骤通过 Windows 反馈中心应用共享：

1. 启动**反馈中心**并确保您已登录。
1. 根据以下分类提交反馈：
   - 类别：安全和隐私
   - 子类别： FIDO
1. 要捕获日志，请使用 选项**重新创建问题**

## <a name="next-steps"></a>后续步骤

[启用对 Azure AD 和混合 Azure AD 联接设备的本地资源的访问](howto-authentication-passwordless-security-key-on-premises.md)

[了解有关设备注册的信息](../devices/overview.md)

[了解 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)
