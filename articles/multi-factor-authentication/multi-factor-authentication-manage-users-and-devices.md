---
title: "管理员管理用户和设备 - Azure MFA | Microsoft Docs"
description: "本文介绍如何更改用户设置，例如，强制用户再次完成验证过程。"
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: yossib
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 847a8bdcf880b56f587f6759058825fd1965d29e
ms.openlocfilehash: 43ab735b91bf3f3f1e9631067827f2c456dd7b72
ms.lasthandoff: 03/01/2017


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>管理云中 Azure 多重身份验证的用户设置
作为管理员，可以管理以下用户和设备设置：

* 要求选定的用户再次提供联系方法
* 删除用户现有的应用密码
* 还原用户所有已暂停设备上的 MFA

## <a name="require-selected-users-to-provide-contact-methods-again"></a>要求选定的用户再次提供联系方法
此项设置将强制用户在登录时再次完成注册过程。 请注意，如果用户拥有应用密码，则非浏览器应用将继续工作。  也可以通过选择“删除选定用户生成的所有现有应用密码”删除用户的应用密码。

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>如何要求用户再次提供联系方法
1. 登录到 Azure 经典门户。
2. 在左侧单击“Active Directory”。
3. 在“目录”下，单击被要求再次提供联系方法的用户对应的目录。
4. 在顶部单击“用户”。
5. 在页面底部，单击“管理 Multi-Factor Auth”。 此时将打开“多重身份验证”页。
6. 找到要管理的用户，并勾选其名称旁边的框。 你可能需要在顶部切换视图。
7. 右侧将显示“管理用户设置”链接。 请单击此按钮。
8. 选中“要求选定用户重新提供联系方式”。
   ![提供联系方式](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
9. 单击“保存”。
10. 单击“关闭”

## <a name="delete-users-existing-app-passwords"></a>删除用户现有的应用密码
这会删除用户创建的所有应用密码。 与这些应用密码关联的非浏览器应用将会停止工作，直到创建新应用密码为止。

### <a name="how-to-delete-users-existing-app-passwords"></a>如何删除用户现有的应用密码
1. 登录到 Azure 经典门户。
2. 在左侧单击“Active Directory”。
3. 在“目录”下，单击你要删除其应用密码的用户对应的目录。
4. 在顶部单击“用户”。
5. 在页面底部，单击“管理 Multi-Factor Auth”。 此时将打开“多重身份验证”页。
6. 找到要管理的用户，并勾选其名称旁边的框。 你可能需要在顶部切换视图。
7. 右侧将显示“管理用户设置”链接。 请单击此按钮。
8. 选中“删除选定用户生成的所有现有应用密码”。
   ![删除应用密码](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. 单击“保存”。
10. 单击“关闭”。

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>还原用户的所有已记住设备的 MFA
Azure 多重身份验证的可配置功能之一是为用户提供将设备标记为受信任的选项。 有关详细信息，请参阅[配置 Azure 多重身份验证设置](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust)

用户可以选择在其常规设备上可配置的天数内不进行双重验证。 如果帐户受到安全威胁，或者丢失了受信任的设备，则需要能够删除受信任状态并需要重新进行双重验证。

“在所有已记住的设备上还原多重身份验证”设置表示，将对用户进行质询，要求他们在下次登录时执行双重验证，而无论他们是否选择将其设备标记为受信任。 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>如何还原用户所有已暂停设备上的 MFA
1. 登录到 Azure 经典门户。
2. 在左侧单击“Active Directory”。
3. 在“目录”下，单击要为其还原 MFA 的用户对应的目录。
4. 在顶部单击“用户”。
5. 在页面底部，单击“管理 Multi-Factor Auth”。 此时将打开“多重身份验证”页。
6. 找到要管理的用户，并勾选其名称旁边的框。 你可能需要在顶部切换视图。
7. 右侧将显示“管理用户设置”链接。 请单击此按钮。
8. 选中“还原所有已记住设备的多重身份验证”****
   ![删除应用密码](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. 单击“保存”。
10. 单击“关闭”。

