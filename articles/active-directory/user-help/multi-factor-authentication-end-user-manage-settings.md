---
title: 管理双重验证设置 - Azure AD | Microsoft Docs
description: 管理 Azure 多重身份验证的使用方式包括更改联系信息或配置设备。
services: multi-factor-authentication
keywords: 多重身份验证客户端, 身份验证问题, 相关性 ID
documentationcenter: ''
author: eross-msft
manager: mtillman
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: lizross
ms.custom: end-user
ms.openlocfilehash: ff48cb2cee5387f5dd5e0b89966a11fb76379337
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2018
ms.locfileid: "39057599"
---
# <a name="manage-your-settings-for-two-step-verification"></a>管理双重验证设置
本文解答了有关如何更新双重验证或多重身份验证设置的问题。 如果在登录帐户时遇到问题，请参阅[使用双重验证时遇到问题](multi-factor-authentication-end-user-troubleshoot.md)获取疑难解答帮助。

## <a name="where-to-find-the-settings-page"></a>设置页位置
根据公司设置多重身份验证的方式，可以在多个位置更改电话号码等设置。

如果公司支持人员发出特定 URL 或管理双重验证的步骤，请按照这些说明操作。 否则，可按照以下适用于所有用户的说明操作。 如果执行以下步骤，但看到的选项不同，则表示公司或学校已自定义门户。 通过管理员获取 Azure 多重身份验证门户的链接。

**转到“其他安全性验证”页**

- 转到 https://aka.ms/MFASetup。

    ![验证](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

如果单击该链接对你无效，还可以按照以下步骤访问“其他安全性验证”页：

1. 登录到 [https://myapps.microsoft.com](https://myapps.microsoft.com)  

2. 在右上方选择帐户名称，并选择“配置文件”。

3. 选择“其他安全性验证”。  

    ![Myapps](./media/multi-factor-authentication-end-user-manage-settings/myapps1.png)

4. “其他安全性验证”页将加载设置。

    ![验证](./media/multi-factor-authentication-end-user-manage-settings/proofup.png)

## <a name="i-want-to-change-my-phone-number-or-add-a-secondary-number"></a>我要更改电话号码，或添加辅助号码
必须配置辅助身份验证电话号码。  由于主要电话号码和移动应用可能在同一部手机上，因此，当手机丢失或被盗时，只能通过辅助电话号码访问帐户。

> [!NOTE]
> 如果无权使用主电话号码，并需要获得进入帐户的帮助，请参阅[使用双重验证时遇到问题](multi-factor-authentication-end-user-troubleshoot.md)一文以获得更多帮助。  

**若要更改主电话号码，请执行以下步骤：**  

1. 在“其他安全性验证”页上，选择当前电话号码的文本框，并使用新电话号码编辑。  
2. 选择“保存”。  
3. 如果此电话号码是用于首选验证选项的号码，必须在保存之前验证新号码。  

**若要添加辅助电话号码，请执行以下操作：**  

1. 在“其他安全性验证”页上，单击“备用身份验证电话”旁的复选框。  
2. 在文本框中输入辅助电话号码。  
3. 选择“保存”，完成更改。  

## <a name="require-two-step-verification-again-on-a-device-youve-marked-as-trusted"></a>需要在已标记为受信任的设备上重新进行双重验证

在浏览器中执行双重验证时，根据组织设置，可能会有一个复选框显示“**X** 天不再询问”。 如果选中了此复选框，并且丢失了设备或认为帐户受到安全威胁，则应将双重验证还原到所有设备。

1. 在“其他安全验证”页中，选择“在以前受信任的设备上还原多重身份验证”。
2. 下次登录任何设备时，会提示执行双重验证。

## <a name="how-do-i-clean-up-microsoft-authenticator-from-my-old-device-and-move-to-a-new-one"></a>如何从旧设备清除 Microsoft Authenticator 并将其迁移到新设备？
从设备上卸载应用或者重置设备时，不会在后端删除激活信息。 有关详细信息，请参阅 [Microsoft Authenticator](microsoft-authenticator-app-how-to.md)。

## <a name="next-steps"></a>后续步骤
* 在[使用双重验证时遇到问题](multi-factor-authentication-end-user-troubleshoot.md)中获得疑难解答提示和帮助
* 为不支持双重验证的任何应用设置[应用密码](multi-factor-authentication-end-user-app-passwords.md)。
