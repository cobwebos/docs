---
title: 设置安全密钥作为验证方法 - Azure AD
description: 如何设置“安全信息”（预览版）页，以使用快速在线身份认证 (FIDO2) 安全密钥作为验证方法来验证身份。
services: active-directory
author: curtand
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/18/2019
ms.author: curtand
ms.openlocfilehash: c056e439deac71417ff14dcfc3f2c3c95db41946
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88797660"
---
# <a name="set-up-a-security-key-as-your-verification-method"></a>设置安全密钥作为验证方法

可以使用安全密钥作为组织中的无密码登录方法。 安全密钥是一种物理设备，与唯一的 PIN 一起用于登录到工作或学校帐户。 由于安全密钥要求你具有物理设备和一些只有你知道的内容，因此它被视为比用户名和密码更强大的验证方式。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Note]
> 如果看不到安全密钥选项，则可能是因为组织不允许你使用此选项进行验证。 在这种情况下，需选择另一种方法，或者与组织的支持人员联系以获取进一步帮助。

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

目前，我们使用[快速在线身份认证 (FIDO)](https://fidoalliance.org/fido2/) (FIDO2) 无密码身份验证协议支持多种安全密钥设计和提供商。 借助这些密钥，你可以在受支持的设备和 Web 浏览器上登录工作或学校帐户，以访问组织的基于云的资源。

如果管理员或组织要求工作或学校帐户使用安全密钥，则他们会向你提供该密钥。 可供使用的安全密钥有各种类型，例如可插入设备的 USB 密钥或可在 NFC 读取器上轻触的 NFC 密钥。 从制造商的文档中可以了解有关安全密钥的详细信息，包括安全密钥的类型。

> [!Note]
> 如果无法使用 FIDO2 安全密钥，则可以使用其他无密码验证方法，如 Microsoft Authenticator 应用或 Windows Hello。 有关 Microsoft Authenticator 应用的详细信息，请参阅[什么是 Microsoft Authenticator 应用？](user-help-auth-app-overview.md)。 有关 Windows Hello 的详细信息，请参阅 [Windows Hello 概述](https://www.microsoft.com/windows/windows-hello)。

## <a name="before-you-begin"></a>开始之前

在能够注册安全密钥之前，必须满足以下条件：

- 管理员已启用此功能，供组织内使用。

- 所用设备运行 Windows 10（2019 年 5 月更新），并使用受支持的浏览器。

- 具有管理员或组织批准的物理安全密钥。 安全密钥必须同时兼容 FIDO2 和 Microsoft。 如果对安全密钥及其是否兼容有任何疑问，请与组织的支持人员联系。

## <a name="register-your-security-key"></a>注册安全密钥

必须创建安全密钥并为它指定一个唯一的 PIN，然后才能使用该密钥登录到工作或学校帐户。 最多向帐户注册 10 个密钥。 

1. 请转到“我的个人资料”页 (https://myaccount.microsoft.com )，然后登录（如果尚未登录）。

2. 依次选择“安全信息”和“添加方法”，然后从“添加方法”下拉列表中选择“安全密钥”   。

    ![使用所选的安全密钥添加方法框](media/security-info/security-info-security-key-add-method.png)

3. 选择“添加”，然后选择拥有的安全密钥类型：“USB 设备”或“NFC 设备”  。

    ![选择是使用 USB 还是 NFC 类型的安全密钥](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 如果不确定拥有的是哪种类型的安全密钥，请参阅制造商的文档。 如果不确定制造商，请与组织的支持人员联系以获得帮助。

4. 准备好物理安全密钥，然后在“安全密钥”框中选择“下一页” 。

    ![安全密钥开始注册框](media/security-info/security-info-security-key-start-setup.png)

    系统会显示一个新框，用于帮助引导你设置新的登录方法。

5. 在“设置新登录方法”框中，选择“下一页”，然后 ：

    - 如果安全密钥是 USB 设备，请将安全密钥插入设备的 USB 端口。

    - 如果安全密钥是 NFC 设备，请将安全密钥轻触读取器。

6. 将唯一的安全密钥 PIN 键入“Windows 安全”框，然后选择“确定” 。

    你将返回“设置新登录方法”框。

7. 选择“**下一页**”。

8. 返回到“安全信息”页，键入一个你稍后会认作新安全密钥的名称，然后选择“下一页” 。

    ![“安全信息”页，为安全密钥命名](media/security-info/security-info-security-key-name.png)

    安全密钥已注册，可供你用于登录工作或学校帐户。

9. 选择“完成”以关闭“安全密钥”框 。

    “安全信息”页连同安全密钥信息一并更新。

    ![“安全信息”页，显示所有已注册的方法](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>从安全信息中删除安全密钥

如果丢失或不再想要使用安全密钥，则可以从安全信息中删除该密钥。 虽然这会阻止安全密钥用于工作或学校帐户，但安全密钥将继续存储你的数据和凭据信息。 要从安全密钥本身删除数据和凭据信息，必须按照本文[重置与 Microsoft 兼容的安全密钥](#reset-your-security-key)部分的说明进行操作。

1. 从要移除的安全密钥选择“删除”链接。

2. 从“删除安全密钥”框中选择“确定” 。

    安全密钥已被删除，你将无法再使用它登录到工作或学校帐户。

>[!Important]
>如果错误地删除了安全密钥，则可以使用本文[如何注册安全密钥](#register-your-security-key)部分中的说明再次注册。

## <a name="manage-your-security-key-settings-from-windows-settings"></a>从 Windows 设置中管理安全密钥设置

可以通过“Windows 设置”应用管理安全密钥设置，包括重置安全密钥和创建新的安全密钥 PIN。

### <a name="reset-your-security-key"></a>重置安全密钥

如果要删除物理安全密钥上存储的所有帐户信息，则必须将该密钥恢复为出厂默认值。 重置安全密钥会删除密钥中的一切信息，以重新开始使用密钥。

>[!IMPORTANT]
>重置安全密钥会删除密钥中的一切信息，从而将其重置为出厂默认值。
>
> **所有数据和凭据都将被清除。**

#### <a name="to-reset-your-security-key"></a>重置安全密钥

1. 打开 Windows 设置应用，依次选择“帐户”、“登录选项”、“安全密钥”和“管理”   。

2. 将安全密钥插入 USB 端口或轻触 NFC 读取器以验证身份。

3. 按照屏幕上的说明进行操作，操作方式取决于具体的安全密钥制造商。 如果屏幕说明中未列出密钥制造商，请参阅制造商网站获取详细信息。

4. 选择“关闭”以关闭“管理”屏幕 。

### <a name="create-a-new-security-key-pin"></a>新建安全密钥 PIN

可以为安全密钥创建新的安全密钥 PIN。

#### <a name="to-create-a-new-security-key-pin"></a>新建安全密钥 PIN

1. 打开 Windows 设置应用，依次选择“帐户”、“登录选项”、“安全密钥”和“管理”   。

2. 将安全密钥插入 USB 端口或轻触 NFC 读取器以验证身份。
3. 从“安全密钥 PIN”区域选择“添加”，键入并确认新的安全密钥 PIN，然后选择“确认”  。

     安全密钥连同安全密钥 PIN 一同更新，以便用于工作和学校帐户。 如果决定再次更改 PIN，可以选择“更改”按钮。
4. 选择“关闭”以关闭“管理”屏幕 。

## <a name="additional-security-info-methods"></a>其他安全信息方法

要注册安全密钥，必须至少注册一个附加的安全验证方法。 请参阅[“概述”部分](./security-info-setup-auth-app.md)以了解详细信息。 

## <a name="next-steps"></a>后续步骤

- 有关无密码验证方法的详细信息，请参阅 [Microsoft’s Azure AD begins public preview of FIDO2 security keys, enabling passwordless logins](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins)（Microsoft 的 Azure AD 推出 FIDO2 安全密钥的公开预览版，启用无密码登录）博客，或阅读[什么是 Microsoft Authenticator 应用？](user-help-auth-app-overview.md)和 [Windows Hello 概述](https://www.microsoft.com/windows/windows-hello)文章。

- 详细了解[与 Microsoft 兼容的安全密钥](/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](active-directory-passwords-update-your-own-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。