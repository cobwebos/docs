---
title: "教程：Azure Active Directory 与 IdeaScale 集成 | Microsoft Docs"
description: "了解如何使用 IdeaScale 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e16dda6b-fdf9-43cc-9bbb-a523f085a8af
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2695483b7dad4418bf24f21ebe808dd145fb9503
ms.openlocfilehash: 6362e5b6232a3a42fdbad3cf0e5a36a2e4b178b8
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-ideascale"></a>教程：Azure Active Directory 与 IdeaScale 集成
本教程的目的是说明 Azure 与 IdeaScale 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 IdeaScale 单一登录 (SSO) 的订阅

完成本教程后，已向 IdeaScale 分配的 Azure AD 用户将能够使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

*  为 IdeaScale 启用应用程序集成
*  配置单一登录
*  配置用户设置
*  分配用户

![方案](./media/active-directory-saas-ideascale-tutorial/IC790838.png "方案")

## <a name="enable-the-application-integration-for-ideascale"></a>为 IdeaScale 启用应用程序集成
本部分的目的是概述如何为 IdeaScale 启用应用程序集成。

**若要为 IdeaScale 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-ideascale-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-ideascale-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-ideascale-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-ideascale-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“IdeaScale”。
   
   ![应用程序库](./media/active-directory-saas-ideascale-tutorial/IC790841.png "应用程序库")
7. 在结果窗格中，选择“IdeaScale”，然后单击“完成”以添加该应用程序。
   
   ![IdeaScale](./media/active-directory-saas-ideascale-tutorial/IC790842.png "IdeaScale")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 IdeaScale 进行身份验证。  

配置 IdeaScale 的单一登录需要检索证书的指纹值。 如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“IdeaScale”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-ideascale-tutorial/IC790843.png "配置单一登录")
2. 在“你希望用户如何登录 IdeaScale”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-ideascale-tutorial/IC790844.png "配置单一登录")
3. 在“配置应用 URL”页的“IdeaScale 登录 URL”文本框中，键入用户用于登录 IdeaScale 应用程序的 URL（例如：“*https://company.IdeaScale.com*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-ideascale-tutorial/IC790845.png "配置应用 URL")
4. 在“配置 IdeaScale 的单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-ideascale-tutorial/IC790846.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 IdeaScale 公司站点。
6. 转到“社区设置”。
   
   ![社区设置](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
7. 转到“安全性”\>“单一登录设置”。
   
   ![单一登录设置](./media/active-directory-saas-ideascale-tutorial/IC790848.png "Single Signon Settings")
8. 对于“单一登录类型”，选择“SAML 2.0”。
   
   ![单一登录类型](./media/active-directory-saas-ideascale-tutorial/IC790849.png "Single Signon Type")
9. 在“单一登录设置”对话框上，执行以下步骤：
   
   ![单一登录设置](./media/active-directory-saas-ideascale-tutorial/IC790850.png "Single Signon Settings")
   
   1. 在 Azure 经典门户的“配置 IdeaScale 的单一登录”对话框页上，复制“实体 ID”值，然后将其粘贴到“SAML IdP 实体 ID”文本框中。
   2. 复制下载的元数据文件的内容，然后将其粘贴到“SAML IdP 元数据”文本框中。
   3. 在 Azure 经典门户的“配置 IdeaScale 的单一登录”对话框页面上，复制“远程注销 URL”值，然后将其粘贴到“注销成功 URL”文本框中。
   4. 单击“保存更改”。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-ideascale-tutorial/IC790851.png "配置单一登录")
    
## <a name="configure-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 IdeaScale，必须将这些用户预配到 IdeaScale 中。 对于 IdeaScale，预配是一项手动任务。

**若要配置用户预配，请执行以下步骤：**

1. 以管理员身份登录到 **IdeaScale** 公司站点。
2. 转到“社区设置”。
   
   ![社区设置](./media/active-directory-saas-ideascale-tutorial/IC790847.png "Community Settings")
3. 转到“基本设置”\>“成员管理”。
4. 单击“添加成员”。
   
   ![成员管理](./media/active-directory-saas-ideascale-tutorial/IC790852.png "Member Management")
5. 在“添加新成员”部分中，执行以下步骤：
   
   ![添加新成员](./media/active-directory-saas-ideascale-tutorial/IC790853.png "Add New Member")
   
   1. 在“电子邮件地址”文本框中，键入要预配的有效 AAD 帐户的电子邮件地址。
   2. 单击“保存更改”。 
   
    >[!NOTE]
    >Azure Active Directory 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
    >  

>[!NOTE]
>可以使用 IdeaScale 提供的任何其他 IdeaScale 用户帐户创建工具或 API 来预配 AAD 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 IdeaScale，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“IdeaScale”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-ideascale-tutorial/IC790854.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-ideascale-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


