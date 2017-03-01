---
title: "教程：Azure Active Directory 与 Mindflash 的集成 | Microsoft Docs"
description: "了解如何将 Mindflash 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bdf91993-aaaa-4598-89b7-77ef8ca065d5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/03/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 57a6428315a4942a0c6566fef5a4db4ee66cb55a
ms.openlocfilehash: 89bd515fa988e0347508b739dd0676c5eeb1f44d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-mindflash"></a>教程：Azure Active Directory 与 Mindflash 的集成
本教程的目的是展示 Azure 与 Mindflash 的集成。 在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Mindflash 单一登录 (SSO) 的订阅

完成本教程后，分配到 Mindflash 的 Azure AD 用户将能够在你的 Mindflash 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

本教程中概述的方案包括以下构建基块：

1. 为 Mindflash 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-mindflash-tutorial/IC787132.png "方案")

## <a name="enabling-the-application-integration-for-mindflash"></a>为 Mindflash 启用应用程序集成
本部分的目的是概述如何为 Mindflash 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-mindflash-perform-the-following-steps"></a>若要为 Mindflash 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-mindflash-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-mindflash-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-mindflash-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-mindflash-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Mindflash”。
   
   ![应用程序库](./media/active-directory-saas-mindflash-tutorial/IC787133.png "应用程序库")
7. 在结果窗格中，选择“Mindflash”，然后单击“完成”以添加该应用程序。
   
   ![Mindflash](./media/active-directory-saas-mindflash-tutorial/IC787134.png "Mindflash")
   
## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Mindflash 证明自己的身份。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **Mindflash** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-mindflash-tutorial/IC787135.png "配置单一登录")
2. 在“你希望用户如何登录到 Mindflash”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-mindflash-tutorial/IC787136.png "配置单一登录")
3. 在“配置应用 URL”页上，在“登录 URL”文本框中，使用“*http://company.mindflash.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-mindflash-tutorial/IC787137.png "配置应用 URL")
4. 在“在 Mindflash 处配置单一登录”页上，单击“下载元数据”，然后将元数据文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-mindflash-tutorial/IC787138.png "配置单一登录")
5. 将元数据文件发送给 Mindflash 支持团队。
   
   > [!NOTE]
   > 单一登录配置必须由 Mindflash 支持团队执行。 配置完成后，你会立即收到通知。
   > 
   > 
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-mindflash-tutorial/IC787139.png "配置单一登录")
   
## <a name="configuring-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Mindflash，必须将其预配到 Mindflash 中。 对于 Mindflash，需要手动执行预配。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录到你的 **Mindflash** 公司站点。
2. 转到“管理用户”。
   
   ![管理用户](./media/active-directory-saas-mindflash-tutorial/IC787140.png "管理用户")
3. 单击“添加用户”，然后单击“新建”。
4. 在“添加新用户”部分中，执行以下步骤：
   
   ![添加新用户](./media/active-directory-saas-mindflash-tutorial/IC787141.png "添加新用户")
   
   1. 将要预配的有效 AAD 帐户的**名字**、**姓氏**和**电子邮件**键入到相关文本框中。
   2. 单击“添加”。

>[!NOTE]
>可以使用 Mindflash 提供的任何其他 Mindflash 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配用户的方式向要允许其使用应用程序的 Azure AD 用户授予应用程序访问权限。

### <a name="to-assign-users-to-mindflash-perform-the-following-steps"></a>若要将用户分配到 Mindflash，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Mindflash”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-mindflash-tutorial/IC787142.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-mindflash-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


