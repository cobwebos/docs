---
title: 将安全密钥设置为验证方法 - Azure AD
description: 如何设置安全信息（预览）页面以验证您的身份，以使用快速在线身份验证 （FIDO2） 安全密钥作为验证方法。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: ee868ded0dc3a46f2ec20d0141aa2576cbf71f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77062330"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>将安全密钥设置为验证方法

您可以在组织中使用安全密钥作为无密码登录方法。 安全密钥是一种物理设备，它使用唯一 PIN 登录您的工作或学校帐户。 由于安全密钥要求您拥有物理设备和仅您知道的内容，因此它被视为比用户名和密码更强的验证方法。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> 如果看不到安全密钥选项，则您的组织可能不允许使用此选项进行验证。 在这种情况下，您需要选择其他方法或联系组织的帮助台以获得更多帮助。

## <a name="security-verification-versus-password-reset-authentication"></a>安全验证与密码重置身份验证

安全信息方法既用于双因素安全验证，又用于密码重置。 但是，并非所有方法都可以用于这二者。

| 方法 | 用于 |
| ------ | -------- |
| Authenticator 应用 | 双重验证和密码重置身份验证。 |
| 短信 | 双重验证和密码重置身份验证。 |
| 电话呼叫 | 双重验证和密码重置身份验证。 |
| 安全密钥 | 双重验证和密码重置身份验证。 |
| 电子邮件帐户 | 仅密码重置身份验证。 需选择另一种方法进行双重验证。 |
| 安全提问 | 仅密码重置身份验证。 需选择另一种方法进行双重验证。 |

## <a name="what-is-a-security-key"></a>什么是安全密钥？

目前，我们使用[快速在线识别 （FIDO） （FIDO）](https://fidoalliance.org/fido2/) （FIDO2） 无密码身份验证协议支持多个安全密钥设计和提供程序。 这些密钥允许您登录到工作或学校帐户，以便在受支持的设备和 Web 浏览器上访问组织的基于云的资源。

如果您的管理员或您的组织需要安全密钥用于您的工作或学校帐户，则他们将会为您提供安全密钥。 您可以使用不同类型的安全密钥，例如插入设备的 USB 密钥或点击 NFC 读卡器的 NFC 密钥。 您可以从制造商的文档中了解有关安全密钥的详细信息，包括安全密钥的类型。

> [!Note]
> 如果您无法使用 FIDO2 安全密钥，则可以使用其他无密码验证方法，如 Microsoft 身份验证器应用或 Windows Hello。 有关 Microsoft 身份验证器应用的详细信息，请参阅什么是[Microsoft 身份验证器应用？](user-help-auth-app-overview.md) 有关 Windows Hello 的详细信息，请参阅[Windows Hello 概述](https://www.microsoft.com/windows/windows-hello)。

## <a name="before-you-begin"></a>开始之前

在能够注册安全密钥之前，必须正确：

- 管理员已打开此功能，供组织内使用。

- 您正在运行 Windows 10 2019 更新的设备上，并使用受支持的浏览器。

- 您有管理员或组织批准的物理安全密钥。 您的安全密钥必须同时符合 FIDO2 和 Microsoft 要求。 如果您对安全密钥及其是否兼容有任何疑问，请与组织的帮助台联系。

## <a name="register-your-security-key"></a>注册安全密钥

您必须创建安全密钥并给它一个唯一的 PIN，然后才能使用该密钥登录到您的工作或学校帐户。 您的帐户中最多可能注册了 10 个密钥。 

1. 转到"**我的个人资料"** 页面https://myprofile.microsoft.com，如果您尚未登录。"

2. 选择 **"安全信息**"，选择 **"添加方法**"，然后从 **"添加方法**下拉列表"中选择 **"安全密钥**"。

    ![添加方法框，选择安全键](media/security-info/security-info-security-key-add-method.png)

3. 选择 **"添加**"，然后选择您拥有的安全密钥类型，即**USB 设备**或 NFC**设备**。

    ![选择您是否具有 USB 或 NFC 类型的安全密钥](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 如果您不确定您拥有哪种类型的安全密钥，请参阅制造商的文档。 如果您不确定制造商，请与组织的帮助台联系以获得帮助。

4. 使安全密钥在物理上可用，然后在 **"安全密钥"** 框中，选择 **"下一步**"。

    ![安全密钥启动注册框](media/security-info/security-info-security-key-start-setup.png)

    一个新框似乎可以帮助您完成设置新的登录方法。

5. 在 **"设置新登录方法**"框中，选择 **"下一步**"，然后选择：

    - 如果您的安全密钥是 USB 设备，请将安全密钥插入设备的 USB 端口。

    - 如果您的安全密钥是 NFC 设备，请点按您的安全密钥到您的读者。

6. 在**Windows 安全**框中键入唯一的安全密钥 PIN，然后选择 **"确定**"。

    您将返回到 **"设置新登录方法"** 框。

7. 选择“下一步”。

8. 返回到 **"安全信息**"页，键入稍后新安全密钥将识别的名称，然后选择 **"下一步**"。

    ![安全信息页面，命名安全密钥](media/security-info/security-info-security-key-name.png)

    您的安全密钥已注册，可供您使用登录您的工作或学校帐户。

9. 选择 **"完成"** 以关闭**安全密钥**框。

    **安全信息**页面将更新您的安全密钥信息。

    ![安全信息页面，显示所有已注册方法](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>从安全信息中删除安全密钥

如果放置错误或不再想要使用安全密钥，则可以从安全信息中删除该密钥。 虽然这阻止安全密钥与工作或学校帐户一起使用，但安全密钥将继续存储您的数据和凭据信息。 要从安全密钥本身中删除您的数据和凭据信息，必须按照本文的["重置 Microsoft 兼容安全密钥](#reset-your-security-key)"部分中的说明进行操作。

1. 从安全密钥中选择 **"删除**"链接以将其删除。

2. 从 **"删除安全密钥"** 框中选择 **"确定**"。

    您的安全密钥已被删除，您将无法再使用它登录到您的工作或学校帐户。

>[!Important]
>如果错误地删除了安全密钥，则可以使用本文"[如何注册安全密钥](#register-your-security-key)"部分中的说明再次注册安全密钥。

## <a name="manage-your-security-key-settings-from-windows-settings"></a>从 Windows 设置管理安全密钥设置

您可以通过 Windows 设置应用管理安全密钥**设置**，包括重置安全密钥和创建新的安全密钥 PIN。

### <a name="reset-your-security-key"></a>重置安全密钥

如果要删除存储在物理安全密钥上的所有帐户信息，则必须将密钥返回到其出厂默认值。 重置安全密钥会从密钥中删除所有内容，从而允许您重新开始。

>[!IMPORTANT]
>重置安全密钥会删除从密钥到出厂默认值的所有内容。
>
> **将清除所有数据和凭据。**

#### <a name="to-reset-your-security-key"></a>重置安全密钥

1. 打开 Windows 设置应用，选择 **"帐户**"，选择 **"登录"选项**，选择 **"安全密钥**"，然后选择"**管理**"。

2. 将安全密钥插入 USB 端口或点击 NFC 读卡器以验证您的身份。

3. 根据您的特定安全密钥制造商，按照屏幕上的说明进行操作。 如果屏幕说明中未列出您的密钥制造商，请参阅制造商网站了解更多信息。

4. 选择 **"关闭**"以关闭 **"管理"** 屏幕。

### <a name="create-a-new-security-key-pin"></a>创建新的安全密钥 PIN

您可以为您的安全密钥创建新的安全密钥 PIN。

#### <a name="to-create-a-new-security-key-pin"></a>创建新的安全密钥 PIN

1. 打开 Windows 设置应用，选择 **"帐户**"，选择 **"登录"选项**，选择 **"安全密钥**"，然后选择"**管理**"。

2. 将安全密钥插入 USB 端口或点击 NFC 读卡器以验证您的身份。
3. 选择"从**安全密钥 PIN**区域**添加**"，键入并确认新的安全密钥 PIN，然后选择 **"确定**"。

     安全密钥使用新的安全密钥 PIN 更新，以便与您的工作或学校帐户一起使用。 如果您决定再次更改 PIN，则可以选择 **"更改**"按钮。
4. 选择 **"关闭**"以关闭 **"管理"** 屏幕。

## <a name="additional-security-info-methods"></a>其他安全信息方法

要注册安全密钥，必须至少注册一个附加的安全验证方法。 有关详细信息，请参阅[概述部分](security-info-add-update-methods-overview.md)。 

## <a name="next-steps"></a>后续步骤

- 有关无密码验证方法的详细信息，请阅读 Microsoft[的 Azure AD 开始公开预览 FIDO2 安全密钥，启用无密码登录](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins)博客，或阅读[什么是 Microsoft 身份验证器应用？](user-help-auth-app-overview.md) [Windows Hello overview](https://www.microsoft.com/windows/windows-hello)

- 有关符合 Microsoft[的安全密钥](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)的详细信息。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](active-directory-passwords-update-your-own-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。
