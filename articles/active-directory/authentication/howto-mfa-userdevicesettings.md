---
title: 管理员管理用户和设备 - Azure MFA - Azure Active Directory
description: 管理员可以如何更改用户设置，例如，强制用户再次完成验证过程。
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04d4848a00fd645bcf23342f27fe820ccf034a8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66298839"
---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>管理云中 Azure 多重身份验证的用户设置

作为管理员，可以管理以下用户和设备设置：

* 要求用户再次提供联系方式
* 删除应用密码
* 在所有信任的设备上要求 MFA

## <a name="require-users-to-provide-contact-methods-again"></a>要求用户再次提供联系方式

此项设置强制用户再次完成注册过程。 如果用户为应用设置了密码，则非浏览器应用将继续工作。  也可以通过选择“删除选定用户生成的所有现有应用密码”  删除用户的应用密码。

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>如何要求用户再次提供联系方法

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择“Azure Active Directory” > “用户” > “所有用户”    。
3. 在右侧，选择工具栏上的“多重身份验证”  。 多重身份验证页面将打开。
4. 选中要管理的用户或用户旁的框。 在右侧显示的快速步骤选项列表。
5. 选择“管理用户设置”  。
6. 选中“要求选定用户重新提供的联系方式”框  。
   ![要求用户再次提供联系方法](./media/howto-mfa-userdevicesettings/reproofup.png)
7. 单击“保存”  。
8. 单击“关闭”  。

组织可以使用 PowerShell 中使用以下作为指南来清除完成这些步骤`StrongAuthenticationMethods`属性：

```PowerShell
$Upn = "theuser@domain.com"
$noMfaConfig = @()
Set-MsolUser -UserPrincipalName $Upn -StrongAuthenticationMethods $noMfaConfig
```

## <a name="delete-users-existing-app-passwords"></a>删除用户现有的应用密码

此设置会删除用户创建的所有应用密码。 与这些应用密码关联的非浏览器应用将会停止工作，直到创建新应用密码为止。

### <a name="how-to-delete-users-existing-app-passwords"></a>如何删除用户现有的应用密码

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择“Azure Active Directory” > “用户” > “所有用户”    。
3. 在右侧，选择工具栏上的“多重身份验证”  。 多重身份验证页面将打开。
4. 选中要管理的用户或用户旁的框。 在右侧显示的快速步骤选项列表。
5. 选择“管理用户设置”  。
6. 选中“删除选定用户生产的所有现有应用密码”框  。
   ![删除所有现有应用密码](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
7. 单击“保存”  。
8. 单击“关闭”  。

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>还原用户的所有已记住设备的 MFA

Azure 多重身份验证的可配置功能之一是为用户提供将设备标记为受信任的选项。 有关详细信息，请参阅[配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md#remember-multi-factor-authentication)。

用户可在其常规设备上选择配置的天数内不进行双重验证。 如果帐户受到安全威胁，或者丢失了受信任的设备，则需要能够删除受信任状态并需要重新进行双重验证。

选中之后，**在所有已记住设备上的还原多重身份验证**用户所需执行双重验证他们在下次登录的时，即使它们标记为受信任其设备。

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>如何还原用户所有已暂停设备上的 MFA

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择“Azure Active Directory” > “用户” > “所有用户”    。
3. 在右侧，选择工具栏上的“多重身份验证”  。 多重身份验证页面将打开。
4. 选中要管理的用户或用户旁的框。 在右侧显示的快速步骤选项列表。
5. 选择“管理用户设置”  。
6. 选中的复选框**在所有已记住设备上的还原多重身份验证**
   ![在所有已记住设备上的还原多重身份验证](./media/howto-mfa-userdevicesettings/rememberdevices.png)
7. 单击“保存”  。
8. 单击“关闭”  。

## <a name="next-steps"></a>后续步骤

- 获取有关如何[配置 Azure 多重身份验证设置](howto-mfa-mfasettings.md)的详细信息
- 如果用户需要帮助，可让其参阅[双重验证用户指南](../user-help/multi-factor-authentication-end-user.md)
