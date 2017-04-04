---
title: "保护 Azure AD 中的密码以及重置被智能密码锁定阻止的密码 | Microsoft Docs"
description: "介绍什么是 Azure AD 租户，以及如何通过 Azure Active Directory 管理 Azure。"
services: active-directory
documentationcenter: 
author: markvi
writer: v-lorisc
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 8e625a346c9495d436a99fcf9eadf8ffeffcfdff
ms.lasthandoff: 03/28/2017


---
# <a name="secure-passwords--in-azure-ad-and-reset-passwords-that-get-blocked-by-smart-password-lockout"></a>保护 Azure AD 中的密码以及重置被智能密码锁定阻止的密码
本文介绍用户或管理员在保护 Azure Active Directory (Azure AD) 和 Microsoft 帐户服务帐户时可遵循的最佳做法。 

 >[!NOTE]
 >Azure AD 管理员可通过单击目录名称来重置用户密码。 在 [Azure 管理门户](https://manage.windowsazure.com)中选择“用户”页，单击用户名，然后单击“重置密码”。 
 >

Azure AD 整合了以下常用功能用于保护密码：
 *    密码长度要求
 *    密码“复杂性”要求
 *    常规和定期密码过期 

有关密码管理功能的信息，请参阅[在 Azure Active Directory 中管理密码](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-manage-passwords)。 

## <a name="azure-ad-password-protection"></a>Azure AD 密码保护
Azure AD 和 Microsoft 帐户系统使用行业验证的方法来确保安全保护用户和管理员的密码。 

本部分介绍 Azure AD 如何使用以下方法保护密码：
 *    动态阻止的密码
 *    智能密码锁定

有关基于当前研究的密码管理的信息，请参阅[密码指南](http://aka.ms/passwordguidance)白皮书。 

### <a name="dynamically-banned-passwords"></a>动态阻止的密码
Azure AD 和 Microsoft 帐户系统通过动态阻止所有常用密码来保护密码。 Azure ID 标识保护团队将定期分析阻止的密码列表，防止用户选择常用的密码。 此服务适用于 Azure AD 和 Microsoft 帐户服务客户。 

创建密码时，管理员必须鼓励用户选择包含字母、数字和字符的唯一组合的不常见密码短语。 这样，用户的密码基本上不可能会遭到破解。 

**破解列表**

Azure AD 一直在努力，力求比网络罪犯先行一步。 我们采取的措施之一是防止用户创建当前攻击列表中的密码。

Azure AD Identity Protection 团队会持续分析常用的密码。 网络罪犯也使用类似的策略发起攻击，例如，构建一个[彩虹表](https://en.wikipedia.org/wiki/Rainbow_table)来破解密码哈希。 

Microsoft 会持续分析[数据破解](https://www.privacyrights.org/data-breaches)技巧以动态更新受禁密码列表，确保容易破解的密码在给 Azure AD 客户带来真正的威胁之前阻止这些密码。 有关我们目前在安全方面所做努力的详细信息，请参阅 [Microsoft 安全智能报告](https://www.microsoft.com/security/sir/default.aspx)。 

### <a name="smart-password-lockout"></a>智能密码锁定

当 Azure AD 检测到潜在的网络罪犯尝试侵入用户密码时，我们将使用智能密码锁定来锁定该用户帐户。 Azure AD 可以确定与特定登录会话关联的风险。 

我们使用最新的安全数据向网络威胁应用锁定语义。 这样，当网络罪犯侵入网络中的用户密码时，用户不会被锁定。

如果用户被锁定在 Azure AD 外部，其屏幕如下所示：

  ![锁定在 Azure AD 外部](./media/active-directory-secure-passwords/locked-out-azuread.png)
  
对于其他 Microsoft 帐户，其屏幕如下所示：

  ![Microsoft 帐户被锁定](./media/active-directory-secure-passwords/locked-out-ms-accounts.png)

有关 Azure Active Directory 中密码管理的信息，请参阅[密码管理的工作原理](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-how-it-works)。

  >![NOTE] 如果你是 Azure AD 管理员，可以使用 [Windows Hello](https://www.microsoft.com/en-us/windows/windows-hello) 来完全避免用户创建传统密码。
  >

## <a name="next-steps"></a>后续步骤
[如何更新自己的密码](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-update-your-own-password)<br>
[Azure 标识管理基础知识](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>
[如何使用密码管理报告获取操作见解](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-passwords-get-insights#view-password-reset-activity)



