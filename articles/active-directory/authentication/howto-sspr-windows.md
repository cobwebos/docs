---
title: 适用于 Windows 设备的自助式密码重置 - Azure Active Directory
description: 了解如何在 Windows 登录屏幕上启用 Azure Active Directory 自助式密码重置。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/17/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: f1cd40b8df0251aee7692df24e9bc3f7186c155d
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966502"
---
# <a name="enable-azure-active-directory-self-service-password-reset-at-the-windows-sign-in-screen"></a>在 Windows 登录屏幕上启用 Azure Active Directory 自助式密码重置

自助式密码重置 (SSPR) 使 Azure Active Directory (Azure AD) 中的用户能够更改或重置其密码，而不需要管理员或支持人员的干预。 通常，用户会在另一台设备上打开 Web 浏览器，以访问 [SSPR 门户](https://aka.ms/sspr)。 若要在运行 Windows 7、8、8.1、10 的计算机上改善体验，可以允许用户在 Windows 登录屏幕上重置其密码。

![Windows 7 和 10 登录屏幕的示例，其中显示了 SSPR 链接](./media/howto-sspr-windows/windows-reset-password.png)

> [!IMPORTANT]
> 本教程演示管理员如何为企业中的 Windows 设备启用 SSPR。
>
> 如果 IT 团队尚未启用从你的 Windows 设备使用 SSPR 的功能，或者你在登录过程中遇到问题，请联系支持人员以获得更多帮助。

## <a name="general-limitations"></a>一般限制

以下限制适用于在 Windows 登录屏幕上使用 SSPR：

- 目前不支持从远程桌面或从 Hyper-V 增强式会话进行密码重置。
- 已知某些第三方凭据提供程序会导致此功能出现问题。
- 已知通过修改 [EnableLUA 注册表项](/openspecs/windows_protocols/ms-gpsb/958053ae-5397-4f96-977f-b7700ee461ec)禁用 UAC 会导致问题。
- 此功能不支持部署了 802.1x 网络身份验证的网络和“在用户登录前立即执行”选项。 对于部署了 802.1x 网络身份验证的网络，建议使用计算机身份验证来启用此功能。
- 已加入混合 Azure AD 的计算机必须能够通过网络连接到域控制器才能使用新密码以及更新缓存的凭据。 这意味着，设备必须位于组织的内部网络或 VPN 中，并且必须能够通过网络访问本地域控制器。
- 如果使用映像，请确保在运行 sysprep 之前先为内置 Administrator 清除 Web 缓存，然后再执行 CopyProfile 步骤。 有关此步骤的详细信息，请参阅支持文章[使用自定义默认用户配置文件时性能较差](https://support.microsoft.com/help/4056823/performance-issue-with-custom-default-user-profile)。
- 已知以下设置会干扰在 Windows 10 设备上使用和重置密码的功能：
    - 如果 v1909 之前 Windows 10 版本中的策略需要 Ctrl + Alt + Del，则 **重置密码** 将不起作用。
    - 如果锁屏通知已关闭，则“重置密码”将无效。
    - HideFastUserSwitching 设置为“已启用”或 1
    - DontDisplayLastUserName 设置为“已启用”或 1
    - NoLockScreen 设置为“已启用”或 1
    - 在设备上设置了 EnableLostMode
    - 将 Explorer.exe 替换为自定义 shell
- 组合使用下面三个特定的设置可能会导致此功能失效。
    - 交互式登录：不要求 CTRL+ALT+DEL = Disabled
    - *DisableLockScreenAppNotifications* = 1 或“已启用”
    - Windows SKU 不是家庭或专业版

## <a name="windows-10-password-reset"></a>Windows 10 密码重置

若要配置 Windows 10 设备以便在登录屏幕上启用 SSPR，请查看以下先决条件和配置步骤。

### <a name="windows-10-prerequisites"></a>Windows 10 先决条件

- 管理员[必须通过 Azure 门户启用 Azure AD 自助式密码重置](tutorial-enable-sspr.md)。
- 使用此功能之前，用户必须在 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 上注册 SSPR
    - 并非仅与在 Windows 登录屏幕上使用 SSPR 相关，所有用户都必须先提供身份验证联系信息，然后才能重置其密码。
- 网络代理要求：
    - 使用端口 443 连接到 `passwordreset.microsoftonline.com` 和 `ajax.aspnetcdn.com`
    - Windows 10 设备仅支持计算机级别的代理配置。
- 至少运行 Windows 10 2018 年 4 月更新版 (v1803)，且设备必须符合下述条件之一：
    - 已加入 Azure AD
    - 已加入混合 Azure AD

### <a name="enable-for-windows-10-using-intune"></a>使用 Intune 为 Windows 10 启用

使用 Intune 部署配置更改以从登录屏幕启用 SSPR 是最灵活的方法。 Intune 允许将配置更改部署到你定义的特定计算机组。 此方法要求在 Intune 中登记设备。

#### <a name="create-a-device-configuration-policy-in-intune"></a>在 Intune 中创建设备配置策略

1. 登录到 [Azure 门户](https://portal.azure.com) 并选择 **Intune**。
1. 转到 "**设备配置**  >  " "**配置文件**"，然后选择 " **+ 创建配置**文件"，创建新的设备配置文件
   - 对于 **平台** ，请选择 *Windows 10 和更高版本*
   - 对于 "**配置文件类型**"，选择 "*自定义*"
1. 选择 " **创建**"，然后为配置文件提供一个有意义的名称，例如 *Windows 10 登录屏幕 SSPR*

    （可选）提供配置文件的有意义的说明，然后选择 " **下一步**"。
1. 在 " *配置设置*" 下，选择 " **添加** " 并提供以下 oma-uri 设置来启用 "重置密码" 链接：
      - 提供一个有意义的名称来说明该设置的作用，如 *ADD SSPR link*。
      - 根据需要提供对设置的有意义的说明。
      - **OMA-URI** 设置为 `./Vendor/MSFT/Policy/Config/Authentication/AllowAadPasswordReset`
      - **数据类型**设置为**整数**
      - **值**设置为 **1**

    依次选择 " **添加**"、" **下一步**"。
1. 可以将策略分配给特定的用户、设备或组。 根据需要为环境分配配置文件，理想情况下为设备的测试组，然后选择 " **下一步**"。

    有关详细信息，请参阅 [在 Microsoft Intune 中分配用户和设备配置文件](/mem/intune/configuration/device-profile-assign)。

1. 根据需要为环境配置适用性规则，例如， *如果 OS 版本为 Windows 10 企业版，则分配配置文件*，然后选择 " **下一步**"。
1. 查看配置文件，然后选择 " **创建**"。

### <a name="enable-for-windows-10-using-the-registry"></a>为使用注册表的 Windows 10 启用此功能

若要使用注册表项在登录屏幕上启用 SSPR，请完成以下步骤：

1. 使用管理凭据登录到 Windows 电脑。
1. 按 Windows + R 打开“运行”对话框，然后以管理员身份运行 regedit
1. 设置以下注册表项：

    ```cmd
    HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\AzureADAccount
       "AllowPasswordReset"=dword:00000001
    ```

#### <a name="troubleshooting-windows-10-password-reset"></a>排查 Windows 10 密码重置问题

如果在 Windows 登录屏幕上使用 SSPR 时遇到问题，Azure AD 审核日志会包含有关发生密码重置的 IP 地址和 *ClientType* 的信息，如以下示例输出所示：

![Azure AD 审核日志中的 Windows 7 密码重置示例](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

当用户在 Windows 10 设备的登录屏幕中重置其密码时，系统会创建名为 `defaultuser1` 的权限较低的临时帐户。 使用此帐户可以确保密码重置过程的安全。

此帐户本身有一个随机生成的密码，该密码在进行设备登录时不显示，在用户重置其密码后会自动删除。 可能存在多个 `defaultuser` 配置文件，不过可以放心地忽略它们。

## <a name="windows-7-8-and-81-password-reset"></a>Windows 7、8 和 8.1 密码重置

若要配置 Windows 7、8 或 8.1 设备以便在登录屏幕上启用 SSPR，请查看以下先决条件和配置步骤。

### <a name="windows-7-8-and-81-prerequisites"></a>Windows 7、8 和 8.1 先决条件

- 管理员[必须通过 Azure 门户启用 Azure AD 自助式密码重置](tutorial-enable-sspr.md)。
- 使用此功能之前，用户必须在 [https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) 上注册 SSPR
    - 并非仅与在 Windows 登录屏幕上使用 SSPR 相关，所有用户都必须先提供身份验证联系信息，然后才能重置其密码。
- 网络代理要求：
    - 使用端口 443 连接到 `passwordreset.microsoftonline.com`
- 修补的 Windows 7 或 Windows 8.1 操作系统。
- 根据[传输层安全性 (TLS) 注册表设置](/windows-server/security/tls/tls-registry-settings#tls-12)中的指导启用的 TLS 1.2。
- 如果在计算机上启用了多个第三方凭据提供程序，用户会在登录屏幕上看到多个用户配置文件。

> [!WARNING]
> 必须启用 TLS 1.2，而不能仅仅设置为自动协商。

### <a name="install"></a>安装

对于 Windows 7、8 和 8.1，必须在计算机上安装一个小组件，才能在登录屏幕上启用 SSPR。 若要安装此 SSPR 组件，请完成以下步骤：

1. 下载要启用的 Windows 版本的相应安装程序。

    Microsoft 下载中心 ([https://aka.ms/sspraddin](https://aka.ms/sspraddin)) 提供了该软件安装程序
1. 登录到要在其中进行安装的计算机，然后运行安装程序。
1. 安装完成后，强烈建议重新启动。
1. 重启后，在登录屏幕中选择一个用户，然后选择“忘记了密码?” 启动密码重置工作流。
1. 遵循屏幕上的步骤完成重置密码的工作流。

![在 Windows 7 中单击“忘记了密码?”后的 SSPR 流](media/howto-sspr-windows/windows-7-sspr.png)

#### <a name="silent-installation"></a>无提示安装

可以使用以下命令，在无提示的情况下安装或卸载 SSPR 组件：

- 若要进行无提示安装，请使用命令“msiexec /i SsprWindowsLogon.PROD.msi /qn”
- 若要进行无提示卸载，请使用命令“msiexec /x SsprWindowsLogon.PROD.msi /qn”

#### <a name="troubleshooting-windows-7-8-and-81-password-reset"></a>排查 Windows 7、8 和 8.1 密码重置问题

如果在 Windows 登录屏幕上使用 SSPR 时遇到问题，则计算机和 Azure AD 中均会记录事件。 Azure AD 事件包括有关发生密码重置的 IP 地址和 ClientType 的信息，如以下示例输出所示：

![Azure AD 审核日志中的 Windows 7 密码重置示例](media/howto-sspr-windows/windows-7-sspr-azure-ad-audit-log.png)

如果需要记录更多的日志，可以更改计算机上的注册表项，以启用详细日志记录。 使用以下注册表项值启用详细日志记录（仅用于故障排除目的）：

```cmd
HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Authentication\Credential Providers\{86D2F0AC-2171-46CF-9998-4E33B3D7FD4F}
```

- 若要启用详细日志记录，请创建 `REG_DWORD: "EnableLogging"` 并将其设置为 1。
- 若要禁用详细日志记录，请将 `REG_DWORD: "EnableLogging"` 更改为 0。

## <a name="what-do-users-see"></a>用户看到什么

为 Windows 设备配置 SSPR 以后，对用户来说有什么变化？ 用户如何知道可以在登录屏幕上重置其密码？ 以下示例屏幕截图显示了可供用户使用 SSPR 重置其密码的更多选项：

![Windows 7 和 10 登录屏幕的示例，其中显示了 SSPR 链接](./media/howto-sspr-windows/windows-reset-password.png)

用户在尝试登录时，可以看到“重置密码”或“忘记了密码”链接，该链接用于在登录屏幕上打开自助式密码重置体验。  此功能允许用户重置其密码，不需使用其他设备来访问 Web 浏览器。

可以在[重置工作或学校密码](../user-help/active-directory-passwords-update-your-own-password.md)中找到为用户提供的有关使用此功能的详细信息

## <a name="next-steps"></a>后续步骤

若要简化用户注册体验，可以[预填充用于 SSPR 的用户身份验证联系信息](howto-sspr-authenticationdata.md)。