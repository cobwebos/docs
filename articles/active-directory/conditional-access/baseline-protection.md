---
title: 什么是 Azure Active Directory 条件访问中的基线保护？ - 预览版 | Microsoft Docs
description: 了解基线保护如何确保在 Azure Active Directory 环境中启用至少为基线级别的安全性。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/08/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 6ee89aa901c9553d22470a0481909e9cd62d60cf
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2018
ms.locfileid: "49345124"
---
# <a name="what-is-baseline-protection-preview"></a>何为基线保护（预览）？  

在上一年中，标识攻击增加了 300%。 为了保护环境免受不断增长的攻击的影响，Azure Active Directory (Azure AD) 引入了一项称为基线保护的新功能。 基线保护是一组预定义的[条件访问策略](../active-directory-conditional-access-azure-portal.md)。 这些策略的目的是确保在所有版本的 Azure AD 中启用至少为基线级别的安全性。 

本文概述了 Azure Active Directory 中的基线保护。


 
## <a name="require-mfa-for-admins"></a>要求管理员进行 MFA

对特权帐户具有访问权限的用户对你的环境具有不受限制的访问权限。 鉴于这些帐户具有的权利，应当特别小心地对待它们。 增强对特权帐户的保护的一种常用方法是要求在使用这些帐户登录时进行更强的帐户验证。 在 Azure Active Directory 中，可以通过要求进行多重身份验证 (MFA) 来实现更强的帐户验证。  

**要求管理员进行 MFA** 是一项要求以下目录角色进行 MFA 的基线策略： 

- 全局管理员  

- SharePoint 管理员  

- Exchange 管理员  

- 条件访问管理员  

- 安全管理员  


![Azure Active Directory](./media/baseline-protection/01.png)

此基线策略提供了用来排除用户和组的选项。 你可能需要排除一个[紧急情况下的访问管理帐户](../users-groups-roles/directory-emergency-access.md)以确保你未锁定在租户之外。


## <a name="enable-a-baseline-policy"></a>启用基线策略 

在基线策略处于预览版时，它们不会默认激活。 如果要激活策略，需要手动将其启用。 如果在预览阶段显式启用了基线策略，则当此功能正式发布时，这些策略将保持活动状态。 由于计划了行为更改，因此，除了激活和停用之外，你还有第三个选项来设置策略的状态：**将来自动启用策略**。 通过选择此选项，你可以在预览期间使这些策略处于禁用状态，但是当此功能正式发布时，Microsoft 会自动启用它们。 如果现在未显式启用基线策略，并且未选择“将来自动启用策略”选项，则当此功能正式发布时，这些策略将保持禁用状态。


**若要启用基线策略，请执行以下操作：**  

1. 以全局管理员、安全管理员或条件访问管理员的身份登录 [Azure 门户](https://portal.azure.com)。

2. 在 **Azure 门户**的左侧导航栏中，单击“Azure Active Directory”。

    ![Azure Active Directory](./media/baseline-protection/02.png)

3. 在“Azure Active Directory”页的“安全性”部分中，单击“条件性访问”。

    ![条件性访问](./media/baseline-protection/05.png)

4. 在策略列表中，单击以 **Baseline policy:** 开头的某个策略。 

5. 若要启用策略，请单击“立即使用策略”。

6. 单击“ **保存**”。 
 
  
 

## <a name="what-you-should-know"></a>要点 

虽然管理自定义条件访问策略需要 Azure AD Premium 许可证，但基线策略在所有版本的 Azure AD 中都可用。     

基线策略中包含的目录角色是特权最高的 Azure AD 角色。 

如果脚本中使用了特权帐户，则应将这些帐户替换为 [Azure 资源的托管标识](../managed-identities-azure-resources/overview.md)或[带证书的服务主体](../../azure-resource-manager/resource-group-authenticate-service-principal.md)。 作为临时解决方法，可以从基线策略中排除特定的用户帐户。 

基线策略应用于旧式身份验证流，如 POP、IMAP、较旧的 Office 桌面客户端。 




## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

- [保护标识基础结构的五个步骤](https://docs.microsoft.com/azure/security/azure-ad-secure-steps)

- [什么是 Azure Active Directory 中的条件访问？](overview.md) 

