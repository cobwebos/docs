---
title: Azure Active Directory 条件访问中的基线保护 | Microsoft Docs
description: 了解基线保护如何确保在环境中启用至少为基线级别的安全性。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 25ae4db2cd4f2a2cea74c428a272c6868acaa5c5
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248910"
---
# <a name="what-is-baseline-protection"></a>何为基线保护？  

在上一年中，标识攻击增加了 300%。 为了保护环境免受不断增长的攻击的影响，Azure Active Directory (Azure AD) 引入了一项称为基线保护的新功能。 基线保护是一组预定义的条件访问策略。 这些策略的目的是确保环境中启用至少为基线级别的安全性。 

预览期间，如果想要激活它们，需要启用基线策略。 正式发布之后，默认情况下将启用这些策略。 

第一个基线保护策略需要对特权帐户进行 MFA。 获得特权帐户控制权的攻击者可以执行巨大的破坏，因此先行保护这些帐户至关重要。 下列特权角色位于此策略的范围内： 

- 全局管理员  

- SharePoint 管理员  

- Exchange 管理员  

- 条件访问管理员  

- 安全管理员  


![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/01.png)

## <a name="how-to-get-started"></a>如何入门 

启用基线策略：  

1. 以全局管理员、安全管理员或条件访问管理员的身份登录 [Azure 门户](https://portal.azure.com)。

2. 在 **Azure 门户**的左侧导航栏中，单击“Azure Active Directory”。

    ![Azure Active Directory](./media/active-directory-conditional-access-baseline-protection/02.png)

3. 在“Azure Active Directory”页的“管理”部分，单击“条件性访问”。

    ![条件性访问](./media/active-directory-conditional-access-baseline-protection/03.png)

4. 在策略列表中，单击“基线策略: 需对管理员进行 MFA (预览版)”。 

5. 若要启用策略，请单击“立即使用策略”。

6. 单击“ **保存**”。 
 

基线策略可提供排除用户和组的选项。 你可能需要排除一个[紧急情况下的访问管理帐户](active-directory-admin-manage-emergency-access-accounts.md)以确保你未锁定在租户之外。
  
 

## <a name="what-you-should-know"></a>要点 

基线策略中包含的目录角色是特权最高的 Azure AD 角色。 根据反馈，可能会在将来添加其他角色。 

如果脚本中有具有管理员特权的帐户，则应改为使用[托管服务标识 (MSI)](managed-service-identity/overview.md) 或[服务主体（有证书）](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authenticate-service-principal)。 作为临时的解决方法，可以从基线策略中排除特定的用户帐户。 

策略可应用于传统的身份验证流，如 POP、IMAP、较旧的 Office 桌面客户端。 

## <a name="next-steps"></a>后续步骤

若要了解如何配置条件性访问策略，请参阅 [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md)（Azure Active Directory 中的条件性访问入门）。

如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](active-directory-conditional-access-best-practices.md)。 
