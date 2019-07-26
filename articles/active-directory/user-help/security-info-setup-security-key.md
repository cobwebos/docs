---
title: 设置安全信息以使用安全密钥 (预览)-Azure Active Directory |Microsoft Docs
description: 如何设置安全信息以使用快速标识联机 (FIDO2) 安全密钥验证身份。
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: librown
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d2f06b054e433c0320019548c56539d102beaad
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68386494"
---
# <a name="set-up-security-info-to-use-a-security-key-preview"></a>设置安全信息以使用安全密钥 (预览)

你可以使用安全密钥作为你的组织中的无密码登录方法。 安全密钥是一种物理设备, 使用唯一的 PIN 登录到工作或学校帐户。 由于安全密钥要求你具有物理设备, 并且只有你知道的内容, 因此它被视为比用户名和密码更强的身份验证方法。

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-preview-notice-security-key.md)]

>[!Important]
>此内容适用于用户。 如果你是管理员，可以在 [Azure Active Directory 文档](https://docs.microsoft.com/azure/active-directory)中查找有关如何设置和管理 Azure Active Directory (Azure AD) 环境的详细信息。

## <a name="what-is-a-security-key"></a>什么是安全密钥？

目前, 我们使用[快速标识 Online (FIDO)](https://fidoalliance.org/fido2/) (FIDO2) 无密码身份验证方法支持多个安全密钥设计和提供程序。 此方法允许你登录到你的工作或学校帐户一次, 以便访问你的组织的所有基于云的资源和支持的浏览器。

如果你的管理员或组织需要为你的工作或学校帐户提供安全密钥, 则会向你提供该密钥。 你可以使用不同类型的安全密钥, 例如, 你插入到设备的 USB 密钥或你在 NFC 读卡器上点击的 NFC 密钥。 你可以从制造商的文档中了解有关安全密钥的详细信息, 包括其类型。

> [!Note]
> 如果你无法使用 FIDO2 安全密钥, 则可以使用其他无密码身份验证方法, 例如 Microsoft Authenticator 应用或 Windows Hello。 有关 Microsoft Authenticator 应用的详细信息, 请参阅[什么是 Microsoft Authenticator 应用？](https://docs.microsoft.com/azure/active-directory/user-help/user-help-auth-app-overview)。 有关 Windows Hello 的详细信息, 请参阅[Windows hello 概述](https://www.microsoft.com/windows/windows-hello)。

## <a name="before-you-begin"></a>开始之前

在开始注册安全密钥之前, 请仔细检查以下内容:

- 你的管理员已启用此功能, 以便在你的组织中使用。

- 你处于至少运行 Windows 10 版本1903和 Microsoft Edge 浏览器的设备上。

- 你已收到管理员或你的组织的物理安全密钥。 安全密钥必须同时为 FIDO2 和 Microsoft 兼容。 如果你对安全密钥有任何疑问以及它是否兼容, 请与你的组织的技术支持部门联系。

## <a name="register-your-security-key"></a>注册安全密钥

必须先准备好安全密钥才能使用 Windows 和唯一的 PIN, 然后才能使用密钥登录到工作帐户或学校帐户。

1. 请在上 https://myprofile.microsoft.com 的 "**我的配置文件**" 页, 并登录 (如果尚未这样做)。

2. 选择 "**安全信息**", 选择 "**添加方法**", 然后从 "**添加方法**" 下拉列表中选择 "**安全密钥**"。

    ![已选择安全密钥的添加方法框](media/security-info/security-info-security-key-add-method.png)

3. 选择 "**添加**", 然后选择你拥有的安全密钥的类型, 即 " **USB 设备**" 或 " **NFC 设备**"。

    ![选择是使用 USB 还是 NFC 类型的安全密钥](media/security-info/security-info-security-key-choose-type.png)

    > [!Note]
    > 如果你不确定你拥有哪种类型的安全密钥, 请参阅制造商的文档。 如果你不确定制造商的情况, 请与你的组织的支持人员联系以获得帮助。

4. 在 "**安全密钥**" 框中, 选择 "**下一步**", 以物理方式使用安全密钥。

    ![安全密钥开始注册框](media/security-info/security-info-security-key-start-setup.png)

    此时将显示一个新的框, 帮助你逐步设置新的登录方法。

5. 在 "**设置新登录方法**" 框中, 选择 "**下一步**", 然后执行以下操作:

    - 如果安全密钥是 USB 设备, 请将您的安全密钥插入到设备的 USB 端口中。

    - 如果安全密钥是 NFC 设备, 请点击你的安全密钥。

6. 在 " **Windows 安全性**" 框中键入唯一的安全密钥 PIN, 然后选择 **"确定"** 。

    你将返回到 "**设置新登录方法**" 框。

7. 选择“**下一步**”。

8. 返回到 "**安全信息**" 页, 为新的安全密钥键入稍后将识别的名称, 然后选择 "**下一步**"。

    ![安全信息页, 命名安全密钥](media/security-info/security-info-security-key-name.png)

    你的安全密钥已注册并可供你用来登录到你的工作或学校帐户。

9. 选择 "**完成**" 以关闭 "**安全密钥**" 框。

    "**安全信息**" 页面将更新为安全密钥信息。

    !["安全信息" 页, 其中显示了所有已注册方法](media/security-info/security-info-security-key-configured.png)

## <a name="delete-a-security-key-from-your-security-info"></a>从安全信息中删除安全密钥

如果不再想要使用安全密钥, 可以从安全信息中删除该密钥。 尽管这会阻止安全密钥与你的工作或学校帐户一起使用, 但安全密钥会继续存储你的数据和凭据信息。 若要从安全密钥本身删除数据和凭据信息, 你必须按照本文[重置 Microsoft 兼容的安全密钥](#reset-your-security-key)部分中的说明进行操作。

1. 选择要删除的安全密钥中的 "**删除**" 链接。

2. 从 "**删除安全密钥**" 框中选择 **"确定"** 。

    你的安全密钥会被删除, 你将无法再使用它来登录到你的工作或学校帐户。

>[!Important]
>如果错误地删除了此安全密钥, 则必须使用本文[如何注册安全密钥](#register-your-security-key)部分中的说明再次注册该安全密钥。

## <a name="manage-your-security-key-settings-from-windows-settings"></a>在 Windows 设置中管理安全密钥设置

你可以从**Windows 设置**应用程序管理安全密钥设置, 包括重置安全密钥和创建新的安全密钥 PIN。

### <a name="reset-your-security-key"></a>重置安全密钥

如果要删除存储在物理安全密钥上的所有帐户信息, 则必须将该密钥恢复为其出厂默认值。 重置安全密钥会删除密钥中的所有内容, 使你可以重新开始。

>[!IMPORTANT]
>重置安全密钥会删除密钥中的所有内容, 并将其重置为出厂默认值。
>
> **将清除所有数据和凭据。**

#### <a name="to-reset-your-security-key"></a>重置安全密钥

1. 打开 Windows "设置" 应用, 选择 "**帐户**", 选择 "**登录选项**", 选择 "**安全密钥**", 然后选择 "**管理**"。

2. 将安全密钥插入 USB 端口, 或点击 NFC 读卡器来验证身份。

3. 根据特定的安全密钥制造商, 按照屏幕上的说明进行操作。 如果你的关键制造商未在屏幕上的说明中列出, 请参阅制造商的网站以获取详细信息。

4. 选择 "**关闭**" 以关闭 "**管理**" 屏幕。

### <a name="create-a-new-security-key-pin"></a>创建新的安全密钥 PIN

你可以为安全密钥创建新的安全密钥 PIN。

#### <a name="to-create-a-new-security-key-pin"></a>创建新的安全密钥 PIN

1. 打开 Windows "设置" 应用, 选择 "**帐户**", 选择 "**登录选项**", 选择 "**安全密钥**", 然后选择 "**管理**"。

2. 将安全密钥插入 USB 端口, 或点击 NFC 读卡器来验证你的身份。5
3. 从 "**安全密钥**" "pin" 区域中选择 "**添加**", 键入并确认新的安全密钥 Pin, 然后选择 **"确定"** 。

    使用新的安全密钥 PIN 更新安全密钥, 以便与工作或学校帐户一起使用。 如果你决定再次更改你的 PIN, 则可以选择 "**更改**" 按钮。6
4. 选择 "**关闭**" 以关闭 "**管理**" 屏幕。

## <a name="additional-security-info-methods"></a>其他安全信息方法

基于你想要执行的操作，还可以选择组织如何联系你以验证你的身份的其他选项。 选项包括：

- **Authenticator 应用。** 下载并使用验证器应用，获取双重验证或密码重置的审批通知或随机生成的审核代码。 有关如何设置和使用 Microsoft Authenticator 应用的分步说明，请参阅[设置安全信息以使用验证器应用](security-info-setup-auth-app.md)。

- **移动设备短信。** 输入移动设备号码，获取将用于双重验证或密码重置的短信或代码。 有关如何通过短信 (SMS) 验证身份的分步说明，请参阅[设置安全信息以使用短信 (SMS)](security-info-setup-text-msg.md)。

- **移动设备或工作电话。** 输入移动设备号码，获取将用于双重验证或密码重置的电话。 有关如何使用电话号码验证身份的分步说明，请参阅[设置安全信息以使用电话](security-info-setup-phone-number.md)。

- **电子邮件地址。** 输入工作或学校电子邮件地址，获取用于密码重置的电子邮件。 此选项不可用于双重验证。 有关如何设置电子邮件的分步说明，请参阅[设置安全信息以使用电子邮件](security-info-setup-email.md)。

- **安全性问题。** 回答管理员为组织创建的一些安全性问题。 此选项仅适用于密码重置，不可用于双重验证。 有关如何设置安全问题的分步说明，请参阅[设置安全信息以使用安全性问题](security-info-setup-questions.md)一文。

## <a name="next-steps"></a>后续步骤

- 有关无密码 authentication 方法的详细信息, 请阅读[Microsoft 的 Azure AD 开始 FIDO2 安全密钥的公共预览版, 启用无密码登录](https://www.onmsft.com/news/microsofts-azure-ad-begins-public-preview-of-fido2-security-keys-enabling-passwordless-logins)博客, 或阅读[什么是 Microsoft Authenticator 应用？](https://docs.microsoft.com/azure/active-directory/user-help8user-help-auth-app-overview)和[Windows Hello 概述](https://www.microsoft.com/windows/windows-hello)文章。

- 有关[与 Microsoft 兼容的安全密钥](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/microsoft-compatible-security-key)的详细信息。

- 如果丢失或忘记密码，请通过[密码重置门户](https://passwordreset.microsoftonline.com/)或遵循[重置工作或学校帐户密码](user-help-reset-password.md)一文中的步骤重置密码。

- 在[无法登录 Microsoft 帐户](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)一文中获取登录问题的故障排除提示和帮助。
