---
title: "教程：Azure Active Directory 与 RightAnswers 集成 | Microsoft Docs"
description: "了解如何使用 RightAnswers 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7f09e25a-a716-41e1-8ca3-fd00e3d1b8cc
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9a653ac435198e89a527070a0174a1adaf830dc3
ms.openlocfilehash: 9251f3f9311e1cd4b1d57c611cc1783855d8d2af


---
# <a name="tutorial-azure-active-directory-integration-with-rightanswers"></a>教程：Azure Active Directory 与 RightAnswers 集成
本教程的目的是说明 Azure 与 RightAnswers 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 RightAnswers 单一登录的订阅

完成本教程后，已向 RightAnswers 分配的 Azure AD 用户将能够使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到该应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 RightAnswers 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-rightanswers-tutorial/IC802925.png "方案")

## <a name="enabling-the-application-integration-for-rightanswers"></a>为 RightAnswers 启用应用程序集成
本部分的目的是概述如何为 RightAnswers 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-rightanswers-perform-the-following-steps"></a>若要为 RightAnswers 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-rightanswers-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-rightanswers-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-rightanswers-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-rightanswers-tutorial/IC749322.png "从库添加应用程序")

6. 在搜索框中，键入“RightAnswers”。
   
    ![应用程序库](./media/active-directory-saas-rightanswers-tutorial/IC802926.png "应用程序库")

7. 在结果窗格中，选择“RightAnswers”，然后单击“完成”以添加该应用程序。
   
## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 RightAnswers 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“RightAnswers”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-rightanswers-tutorial/IC802927.png "配置单一登录")

2. 在“你希望用户如何登录 RightAnswers”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-rightanswers-tutorial/IC802928.png "配置单一登录")

3. 在“配置应用设置”页的“登录 URL”文本框中，键入用户用于登录 RightAnswers 应用程序的 URL（例如：*https://fortify.rightanswers.com/portal/ss/*），然后单击“下一步”。
   
    ![配置应用设置](./media/active-directory-saas-rightanswers-tutorial/IC802929.png "配置应用设置")

4. 在“配置 RightAnswers 的单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
    ![配置单一登录](./media/active-directory-saas-rightanswers-tutorial/IC802930.png "配置单一登录")

5. 将下载的元数据文件发送给 RightAnswers 支持团队。
   
    > [!NOTE]
    > RightAnswers 支持团队需要执行实际的 SSO 配置。
    > 在为订阅启用了 SSO 后，你将收到通知。
    > 
    > 

6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-rightanswers-tutorial/IC802931.png "配置单一登录")
   
## <a name="configuring-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 RightAnswers，必须将这些用户预配到 RightAnswers 中。  
对于 RightAnswers，预配是一项自动化任务。  
不存在任何操作项。

如有必要，在第一次尝试单一登录时将自动创建用户。

> [!NOTE]
> 可以使用任何其他 Rally Software 用户帐户创建工具或 RightAnswers 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-rightanswers-perform-the-following-steps"></a>若要将用户分配到 RightAnswers，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“RightAnswers”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-rightanswers-tutorial/IC802932.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-rightanswers-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Jan17_HO1-->


