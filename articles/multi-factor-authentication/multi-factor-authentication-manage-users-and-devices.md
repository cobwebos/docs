---
title: "Azure Multi-Factor Authentication 报告"
description: "本文介绍如何更改用户设置，例如，强制用户再次完成验证过程。"
documentationcenter: 
services: multi-factor-authentication
author: kgremban
manager: femila
editor: curtand
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 80eb9d882fa6b057623e0a5559baf111542359da


---
# <a name="managing-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>管理云中 Azure Multi-Factor Authentication 的用户设置
作为管理员，你可以管理以下用户和设备设置。  

* [要求选定用户重新提供联系方式](#require-selected-users-to-provide-contact-methods-again)
* [删除用户现有的应用密码](#delete-users-existing-app-passwords)
* [还原用户所有已暂停设备上的 MFA](#restore-mfa-on-all-suspended-devices-for-a-user)

如果计算机或设备丢失、被盗或你需要删除用户访问权限，则此项设置将很有用。

## <a name="require-selected-users-to-provide-contact-methods-again"></a>要求选定的用户再次提供联系方法
此项设置将强制用户在登录时再次完成注册过程。 请注意，如果用户拥有应用密码，则非浏览器应用将继续工作。  也可以通过选择“删除选定用户生成的所有现有应用密码”删除用户的应用密码。

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>如何要求用户再次提供联系方法
1. 登录到 Azure 经典门户。
2. 在左侧单击“Active Directory”。
3. 在“目录”下，单击被要求再次提供联系方法的用户对应的目录。
4. 在顶部单击“用户”。
5. 在页面底部，单击“管理 Multi-Factor Auth”。 此时将打开“Multi-Factor Authentication”页。
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
5. 在页面底部，单击“管理 Multi-Factor Auth”。 此时将打开“Multi-Factor Authentication”页。
6. 找到要管理的用户，并勾选其名称旁边的框。 你可能需要在顶部切换视图。
7. 右侧将显示“管理用户设置”链接。 请单击此按钮。
8. 选中“删除选定用户生成的所有现有应用密码”。
   ![删除应用密码](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. 单击“保存”。
10. 单击“关闭”。

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>还原用户的所有已记住设备的 MFA
管理员可以还原用户设备和浏览器的 Multi-Factor Authentication。 执行此操作时，将从用户的所有设备和浏览器中删除记住的 MFA，并且用户在下次登录时需要使用 MFA。

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>如何还原用户所有已暂停设备上的 MFA
1. 登录到 Azure 经典门户。
2. 在左侧单击“Active Directory”。
3. 在“目录”下，单击要为其还原 MFA 的用户对应的目录。
4. 在顶部单击“用户”。
5. 在页面底部，单击“管理 Multi-Factor Auth”。 此时将打开“Multi-Factor Authentication”页。
6. 找到要管理的用户，并勾选其名称旁边的框。 你可能需要在顶部切换视图。
7. 右侧将显示“管理用户设置”链接。 请单击此按钮。
8. 选中“还原所有已记住设备的多重身份验证”****
   ![删除应用密码](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. 单击“保存”。
10. 单击“关闭”。




<!--HONumber=Nov16_HO3-->


