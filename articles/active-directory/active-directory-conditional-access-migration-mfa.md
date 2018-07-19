---
title: 迁移要求在 Azure 门户中进行多重身份验证的经典策略 | Microsoft Docs
description: 本文演示如何迁移要求在 Azure 门户中进行多重身份验证的经典策略。
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
ms.date: 06/13/2018
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 6190a8ee90855223779751373bf16ca3db0fe761
ms.sourcegitcommit: ab3b2482704758ed13cccafcf24345e833ceaff3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/06/2018
ms.locfileid: "37870341"
---
# <a name="migrate-a-classic-policy-that-requires-multi-factor-authentication-in-the-azure-portal"></a>迁移要求在 Azure 门户中进行多重身份验证的经典策略 

本文演示如何迁移要求对云应用进行**多重身份验证**的经典策略。 虽然不是先决条件，但我们建议你在开始迁移经典策略之前阅读[在 Azure 门户中迁移经典策略](active-directory-conditional-access-migration.md)。


 
## <a name="overview"></a>概述 

本文中的方案演示如何迁移要求对云应用进行**多重身份验证**的经典策略。 

![Azure Active Directory](./media/active-directory-conditional-access-migration/33.png)


迁移过程包括以下步骤：

1. [打开经典策略](#open-a-classic-policy)获取配置设置。
2. 创建新 Azure AD 条件访问策略用于替换经典策略。 
3. 禁用经典策略。



## <a name="open-a-classic-policy"></a>打开经典策略

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航栏中，单击“Azure Active Directory”。

    ![Azure Active Directory](./media/active-directory-conditional-access-migration-mfa/01.png)

2. 在“Azure Active Directory”页的“管理”部分，单击“条件性访问”。

    ![条件性访问](./media/active-directory-conditional-access-migration-mfa/02.png)

3. 在“管理”部分中，单击“经典策略(预览)”。

    ![经典策略](./media/active-directory-conditional-access-migration-mfa/12.png)

4. 在经典策略列表中，单击要求对云应用进行**多重身份验证**的策略。

    ![经典策略](./media/active-directory-conditional-access-migration-mfa/13.png)


## <a name="create-a-new-conditional-access-policy"></a>创建新的条件访问策略


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

8. 在“云应用”页上执行以下步骤：

    ![条件性访问](./media/active-directory-conditional-access-migration/08.png)

    a. 单击“选择应用”。

    b. 单击“选择”。

    c. 在“选择”页上选择云应用，单击“选择”。

    d. 在“云应用”页上，单击“完成”。



9. 如果已选择“需要多重身份验证”：

    ![条件性访问](./media/active-directory-conditional-access-migration/26.png)

    a. 在“访问控制”部分中，单击“授予”。

    ![条件性访问](./media/active-directory-conditional-access-migration/27.png)

    b. 在“授予”页上，依次单击“授予访问权限”、“需要多重身份验证”。

    c. 单击“选择”。


10. 单击“打开”启用策略。

    ![条件性访问](./media/active-directory-conditional-access-migration/30.png)



## <a name="disable-the-classic-policy"></a>禁用经典策略

若要禁用经典策略，请单击“详细信息”视图中的“禁用”。

![经典策略](./media/active-directory-conditional-access-migration-mfa/14.png)



## <a name="next-steps"></a>后续步骤

- 有关经典策略迁移的详细信息，请参阅[在 Azure 门户中迁移经典策略](active-directory-conditional-access-migration.md)。


- 若要了解如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用必须使用 MFA](active-directory-conditional-access-app-based-mfa.md)。

- 如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](active-directory-conditional-access-best-practices.md)。 
