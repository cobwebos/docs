---
title: "教程：Azure Active Directory 与 Picturepark 集成 | Microsoft 文档"
description: "了解如何使用 Picturepark 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 98f2e5596a0af2fc9e633e005642cc3cd21621ce


---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>教程：Azure Active Directory 与 Picturepark 的集成
本教程的目的是介绍 Azure 与 Picturepark 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Picturepark 租户

完成本教程以后，分配到 Picturepark 的 Azure AD 用户就可以通过单一登录登录到 Picturepark 公司站点的应用程序（通过服务提供商启动登录），或者参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）进行单一登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 Picturepark 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-picturepark-tutorial/IC795055.png "Scenario")

## <a name="enabling-the-application-integration-for-picturepark"></a>为 Picturepark 启用应用程序集成
本部分的目的是概述如何为 Picturepark 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-picturepark-perform-the-following-steps"></a>若要为 Picturepark 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-picturepark-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-picturepark-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-picturepark-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-picturepark-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“Picturepark”。
   
   ![应用程序库](./media/active-directory-saas-picturepark-tutorial/IC795056.png "Application Gallery")
7. 在结果窗格中，选择“Picturepark”，然后单击“完成”添加该应用程序。
   
   ![Picturepark](./media/active-directory-saas-picturepark-tutorial/IC795057.png "Picturepark")

## <a name="configuring-single-sign-on"></a>配置单一登录
此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Picturepark 进行身份验证。  
配置 Picturepark 的单一登录要求检索证书的指纹值。  
如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Picturepark”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-picturepark-tutorial/IC795058.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 Picturepark”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-picturepark-tutorial/IC795059.png "Configure Single Sign-On")
3. 在“配置应用 URL”页的“Picturepark 登录 URL”文本框中，使用“*http://company.picturepark.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-picturepark-tutorial/IC795060.png "Configure App URL")
4. 在“配置 Picturepark 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-picturepark-tutorial/IC795061.png "Configure Single Sign-On")
5. 在另一 Web 浏览器窗口中，以管理员身份登录到 Picturepark 公司站点。
6. 在顶部工具栏中，单击“管理工具”，然后单击“管理控制台”。
   
   ![管理控制台](./media/active-directory-saas-picturepark-tutorial/IC795062.png "Management Console")
7. 单击“身份验证”，然后单击“标识提供者”。
   
   ![身份验证](./media/active-directory-saas-picturepark-tutorial/IC795063.png "Authentication")
8. 在“标识提供者配置”部分执行以下步骤：
   
   ![标识提供者配置](./media/active-directory-saas-picturepark-tutorial/IC795064.png "Identity provider configuration")
   
   1. 单击 **“添加”**。
   2. 键入配置名称。
   3. 选择“设为默认值”。
   4. 在 Azure 经典门户的“配置 Picturepark 的单一登录”对话框页上，复制“SAML SSO URL”值，然后将其粘贴到“颁发者 URI”文本框中。
   5. 从导出的证书中复制“指纹”值，然后将其粘贴到“可信颁发者指纹”文本框中。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)
      > 
      > 
   6. 单击“JoinDefaultUsersGroup”。
   7. 若要在“声明”文本框中设置 **Emailaddress** 属性，请键入 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**。
      ![配置](./media/active-directory-saas-picturepark-tutorial/IC795065.png "Configuration")
   8. 单击“保存” 。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-picturepark-tutorial/IC795066.png "Configure Single Sign-On")

## <a name="configuring-user-provisioning"></a>配置用户设置
若要让 Azure AD 用户登录 Picturepark，必须将其预配到 Picturepark 中。  
使用 Picturepark 时，预配属手动任务。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到 **Picturepark** 租户。
2. 在顶部工具栏中，单击“管理工具”，然后单击“用户”。
   
   ![用户](./media/active-directory-saas-picturepark-tutorial/IC795067.png "Users")
3. 在“用户概述”选项卡中，单击“新建”。
   
   ![用户管理](./media/active-directory-saas-picturepark-tutorial/IC795068.png "User management")
4. 在“创建用户”对话框中，执行以下步骤：
   
   ![创建用户](./media/active-directory-saas-picturepark-tutorial/IC795069.png "Create User")
   
   1. 键入希望在相关文本框中预配的有效 Azure Active Directory 用户的“电子邮件地址”、“密码”、“确认密码”、“名字”、“姓氏”、“公司”、“国家或地区”、“邮政编码”、“城市”。
   2. 选择一种**语言**。
   3. 单击“创建” 。

> [!NOTE]
> 可以使用任何其他 Picturepark 用户帐户创建工具或 Picturepark 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-picturepark-perform-the-following-steps"></a>若要将用户分配到 Picturepark，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Picturepark”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-picturepark-tutorial/IC795070.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-picturepark-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


