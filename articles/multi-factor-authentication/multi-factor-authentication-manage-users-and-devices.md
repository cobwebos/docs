---
title: "管理员管理用户和设备 - Azure MFA | Microsoft Docs"
description: "本文介绍如何更改用户设置，例如，强制用户再次完成验证过程。"
documentationcenter: 
services: multi-factor-authentication
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: aac3b922-7cc1-428c-9044-273579aa7b5a
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: joflore
ms.reviewer: yossib
ms.custom: it-pro
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: e9b8504d4a59cf0fae69a4e975d6f834028066d5
ms.contentlocale: zh-cn
ms.lasthandoff: 06/30/2017

---
# <a name="manage-user-settings-with-azure-multi-factor-authentication-in-the-cloud"></a>管理云中 Azure 多重身份验证的用户设置
作为管理员，可以管理以下用户和设备设置：

* 要求用户再次提供联系方式
* 删除应用密码
* 在所有信任的设备上要求 MFA 

## <a name="require-users-to-provide-contact-methods-again"></a>要求用户再次提供联系方式
此项设置强制用户再次完成注册过程。 如果用户为应用设置了密码，则非浏览器应用将继续工作。  也可以通过选择“删除选定用户生成的所有现有应用密码”删除用户的应用密码。

### <a name="how-to-require-users-to-provide-contact-methods-again"></a>如何要求用户再次提供联系方法
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择“Azure Active Directory” > “用户和组” > “所有用户”。
3. 选择“多重身份验证”。 多重身份验证页面将打开。 
4. 选中要管理的用户或用户旁的框。 右侧将显示快速步骤选项列表。 
5. 选择“管理用户设置”。
6. 选中“要求选定用户重新提供的联系方式”框。
   ![提供联系方式](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)
7. 单击“保存”。
8. 单击“关闭”。

## <a name="delete-users-existing-app-passwords"></a>删除用户现有的应用密码
此设置会删除用户创建的所有应用密码。 与这些应用密码关联的非浏览器应用将会停止工作，直到创建新应用密码为止。

### <a name="how-to-delete-users-existing-app-passwords"></a>如何删除用户现有的应用密码
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择“Azure Active Directory” > “用户和组” > “所有用户”。
3. 选择“多重身份验证”。 多重身份验证页面将打开。 
6. 选中要管理的用户或用户旁的框。 右侧将显示快速步骤选项列表。 
7. 选择“管理用户设置”。
8. 选中“删除选定用户生产的所有现有应用密码”框。
   ![删除应用密码](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
9. 单击“保存”。
10. 单击“关闭”。

## <a name="restore-mfa-on-all-remembered-devices-for-a-user"></a>还原用户的所有已记住设备的 MFA
Azure 多重身份验证的可配置功能之一是为用户提供将设备标记为受信任的选项。 有关详细信息，请参阅[配置 Azure 多重身份验证设置](multi-factor-authentication-whats-next.md#remember-multi-factor-authentication-for-devices-that-users-trust)。

用户可在其常规设备上选择配置的天数内不进行双重验证。 如果帐户受到安全威胁，或者丢失了受信任的设备，则需要能够删除受信任状态并需要重新进行双重验证。

“在所有已记住的设备上还原多重身份验证”设置表示，将对用户进行质询，要求他们在下次登录时执行双重验证，而无论他们是否选择将其设备标记为受信任。 

### <a name="how-to-restore-mfa-on-all-suspended-devices-for-a-user"></a>如何还原用户所有已暂停设备上的 MFA
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧，选择“Azure Active Directory” > “用户和组” > “所有用户”。
3. 选择“多重身份验证”。 多重身份验证页面将打开。 
6. 选中要管理的用户或用户旁的框。 右侧将显示快速步骤选项列表。 
7. 选择“管理用户设置”。
8. 选中“还原所有已记住设置的多重身份验证”框
   ![删除应用密码](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
9. 单击“保存”。
10. 单击“关闭”。

## <a name="next-steps"></a>后续步骤

- 获取有关如何[配置 Azure 多重身份验证设置](multi-factor-authentication-whats-next.md)的详细信息

- 如果用户需要帮助，可让其参阅[双重验证用户指南](./end-user/multi-factor-authentication-end-user.md)

