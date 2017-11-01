---
title: "在 Azure 门户中迁移经典策略 | Microsoft Docs"
description: "在 Azure 门户中迁移经典策略。"
services: active-directory
keywords: "对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略"
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: c584eddb5542c2c49d08d35bcaf8e7acb5c5b83a
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2017
---
# <a name="migrate-classic-policies-in-the-azure-portal"></a>在 Azure 门户中迁移经典策略 


[条件访问](active-directory-conditional-access-azure-portal.md)是 Azure Active Directory (Azure AD) 中的一项功能，用于控制已获授权的用户如何访问云应用。 尽管此功能的用途仍未变化，但新版 Azure 门户在条件访问的工作原理方面引入了重大改进。 在 Azure 门户外部配置的条件访问策略与在 Azure 门户中创建的新策略能够共存。 只要不禁用或删除这些策略，它们就仍会在环境中应用。 但是，我们建议将经典策略迁移到新的 Azure AD 条件访问策略，原因如下：

- 使用新策略可以解决经典策略所不能处理的方案。

- 可以通过合并来减少需要管理的策略数。   

本主题帮助读者将现有的经典策略迁移到新的 Azure AD 条件访问策略。


## <a name="classic-policies"></a>经典策略

不是在 Azure 门户中为 Azure AD 和 Intune 创建的条件访问策略也称为**经典策略**。 无需访问 Azure 经典门户即可迁移经典策略。 Azure 门户提供[**经典策略(预览)** 视图](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)，可在其中查看经典策略。

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


### <a name="open-a-classic-policy"></a>打开经典策略

**打开经典策略：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航栏中，单击“Azure Active Directory”。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. 在“Azure Active Directory”页的“管理”部分，单击“条件性访问”。

    ![条件性访问](./media/active-directory-conditional-access-migration/02.png)
 
2. 在“条件访问 - 策略”页的“管理”部分中，单击“经典策略(预览)”。

3. 在经典策略列表中，选择要处理的策略。   

    ![条件性访问](./media/active-directory-conditional-access-migration/34.png)



## <a name="azure-ad-conditional-access-policies"></a>Azure AD 条件访问策略

本主题提供在不熟悉 Azure AD 条件访问策略的情况下迁移经典策略的详细步骤。 但是，熟悉 Azure AD 条件访问的基本概念和术语有助于改善迁移体验。

请参阅：

- [Azure Active Directory 中的条件访问](active-directory-conditional-access-azure-portal.md)，了解基本概念和术语

- [Azure Active Directory 中的条件访问入门](active-directory-conditional-access-azure-portal-get-started.md)，熟悉 Azure 门户中的用户界面


 





## <a name="multi-factor-authentication-policy"></a>多重身份验证策略 

此示例演示如何迁移要求对云应用进行多重身份验证** 的经典策略。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


**迁移经典策略：**

1. [打开经典策略](#open-a-classic-policy)获取配置设置。
2. 创建新 Azure AD 条件访问策略用于替换经典策略。 


### <a name="create-a-new-conditional-access-policy"></a>创建新的条件访问策略


1. 在 [Azure 门户](https://portal.azure.com)的左侧导航栏中，单击“Azure Active Directory”。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration/01.png)

2. 在“Azure Active Directory”页的“管理”部分，单击“条件性访问”。

    ![条件性访问](./media/active-directory-conditional-access-migration/02.png)



3. 在“条件性访问”页顶部的工具栏中单击“添加”，打开“新建”页。

    ![条件性访问](./media/active-directory-conditional-access-migration/03.png)

4. 在“新建”页上的“名称”文本框中，键入策略的名称。

    ![条件性访问](./media/active-directory-conditional-access-migration/29.png)

5. 在“分配”部分中，单击“用户和组”。

    ![条件性访问](./media/active-directory-conditional-access-migration/05.png)

    a. 如果已在经典策略中选择所有用户，请单击“所有用户”。 

    ![条件性访问](./media/active-directory-conditional-access-migration/35.png)

    b. 如果已在经典策略中选择组，请单击“选择用户和组”，并选择所需的用户和组。

    ![条件性访问](./media/active-directory-conditional-access-migration/36.png)

    c. 如果需要排除组，请单击“排除”选项卡，并选择所需的用户和组。 

    ![条件性访问](./media/active-directory-conditional-access-migration/37.png)

6. 在“新建”页的“分配”部分中单击“云应用”，打开“云应用”页。

    ![条件性访问](./media/active-directory-conditional-access-azure-portal-get-started/07.png)

8. 在“云应用”页上执行以下步骤：

    ![条件性访问](./media/active-directory-conditional-access-migration/08.png)

    a. 单击“选择应用”。

    b. 单击“选择”。

    c. 在“选择”页上选择云应用，单击“选择”。

    d.单击“下一步”。 在“云应用”页上，单击“完成”。



9. 如果已选择“需要多重身份验证”：

    ![条件性访问](./media/active-directory-conditional-access-migration/26.png)

    a. 在“访问控制”部分中，单击“授予”。

    ![条件性访问](./media/active-directory-conditional-access-migration/27.png)

    b. 在“授予”页上，依次单击“授予访问权限”、“需要多重身份验证”。

    c. 单击“选择”。


10. 单击“打开”启用策略。

    ![条件性访问](./media/active-directory-conditional-access-migration/30.png)

11. 禁用经典策略。 

    ![条件性访问](./media/active-directory-conditional-access-migration/38.png)



 


## <a name="next-steps"></a>后续步骤

- 若要了解如何配置条件性访问策略，请参阅 [Get started with conditional access in Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md)（Azure Active Directory 中的条件性访问入门）。

- 如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](active-directory-conditional-access-best-practices.md)。 
