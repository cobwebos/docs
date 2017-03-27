---
title: "教程：Azure Active Directory 与 Onit 集成 | Microsoft 文档"
description: "了解如何使用 Onit 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/26/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: b1c18ac614e17cbd25691dc92dbd14a781e4d8ab
ms.openlocfilehash: ee656180676450834940b996bccf93c26190f9d6


---

# <a name="tutorial-azure-active-directory-integration-with-onit"></a>教程：Azure Active Directory 与 Onit 的集成
本教程的目的是介绍 Azure 与 Onit 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用 Onit 单一登录的订阅

完成本教程后，分配到 Onit 的 Azure AD 用户将能够在 Onit 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录 (SSO) 方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Onit 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-onit-tutorial/IC791166.png "方案")

## <a name="enabling-the-application-integration-for-onit"></a>为 Onit 启用应用程序集成
本部分的目的是概述如何为 Onit 启用应用程序集成。

**若要为 Onit 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-onit-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-onit-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-onit-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-onit-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Onit”。
   
   ![应用程序库](./media/active-directory-saas-onit-tutorial/IC791167.png "应用程序库")
7. 在结果窗格中，选择“Onit”，然后单击“完成”添加该应用程序。
   
   ![Onit](./media/active-directory-saas-onit-tutorial/IC795325.png "Onit")
   
## <a name="configuring-single-sign-on"></a>配置单一登录

此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Onit 进行身份验证。  

配置 Onit 的单一登录要求检索证书的指纹值。  
如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

Onit 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 **SAML 令牌属性**配置。  

以下屏幕截图显示一个示例。

![单一登录](./media/active-directory-saas-onit-tutorial/IC791168.png "单一登录")

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Onit”应用程序集成页的顶部菜单中单击“属性”，打开“SAML 令牌属性”对话框。
   
   ![属性](./media/active-directory-saas-onit-tutorial/IC791169.png "属性")
2. 若要添加所需的属性映射，请执行以下步骤：

   |属性名称|属性值|
   |---|---|
   |名称|User.userprincipalname|
   |email|User.mail|

   1.  对于上表中的每个数据行，单击“添加用户属性”。
   2.  在“属性名称”文本框中，键入为该行显示的属性名称。
   3.  在“属性值”列表中，选择为该行显示的属性值。
   4.  单击“完成”。

3. 单击“应用更改”。
4. 在浏览器中，单击“后退”以再次打开“快速启动”对话框。
5. 单击“配置单一登录”以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-onit-tutorial/IC791170.png "配置单一登录")
6. 在“你希望用户如何登录 Onit”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-onit-tutorial/IC791171.png "配置单一登录")
7. 在“配置应用 URL”页的“Onit 登录 URL”文本框中，键入用户用于登录 Onit 应用程序的 URL（例如：“*https://ms-sso-test.onit.com*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-onit-tutorial/IC791172.png "配置应用 URL")
8. 在“配置 Onit 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-onit-tutorial/IC791173.png "配置单一登录")
9. 在另一 Web 浏览器窗口中，以管理员身份登录到 Onit 公司站点。
10. 在顶部菜单中，单击“管理”。
   
   ![管理](./media/active-directory-saas-onit-tutorial/IC791174.png "Administration")
11. 单击“编辑公司”。
   
   ![编辑公司](./media/active-directory-saas-onit-tutorial/IC791175.png "编辑公司")
12. 单击“安全”选项卡。
    
    ![编辑公司信息](./media/active-directory-saas-onit-tutorial/IC791176.png "编辑公司信息")
13. 在“安全”选项卡中，执行以下步骤：
    
   ![单一登录](./media/active-directory-saas-onit-tutorial/IC791177.png "单一登录")
    
   1. 选择“单一登录和密码”作为“身份验证策略”。
   2. 在 Azure 经典门户的“配置 Onit 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“IDP 目标 URL”文本框中。
   3. 在 Azure 经典门户的“配置 Onit 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“IDP 注销 URL”文本框中。
   4. 从导出的证书中复制“指纹”值，然后将其粘贴到“Idp 证书指纹(SHA1)”文本框中。    
      >[!TIP]
      >有关更多详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。
      >
      
   5. 对于“SSO 类型”，选择“SAML”。
   6. 在“SSO 登录按钮文本”文本框中，键入要使用的按钮文本。
   7. 选择“以下域/用户需要使用 SSO 登录: ”，将测试用户的电子邮件地址键入相关文本框中，然后单击“更新”。
   
       ![编辑公司](./media/active-directory-saas-onit-tutorial/IC791178.png "编辑公司")  
14. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-onit-tutorial/IC791179.png "配置单一登录")
    
## <a name="configuring-user-provisioning"></a>配置用户设置

若要让 Azure AD 用户登录 Onit，必须将其预配到 Onit 中。  

使用 Onit 时，预配属手动任务。

**若要配置用户预配，请执行以下步骤：**

1. 以管理员身份登录到 **Onit** 公司站点。
2. 单击“添加用户”。
   
   ![管理](./media/active-directory-saas-onit-tutorial/IC791180.png "Administration")
3. 在“添加用户”对话框页上，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-onit-tutorial/IC791181.png "添加用户")
   
   1. 在相关文本框中键入要预配的有效 AAD 帐户的“名称”和“电子邮件地址”。
   2. 单击“创建” 。    
   
      >[!NOTE]
      >帐户所有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
      > 
       

> [!NOTE]
> 可以使用任何其他 Onit 用户帐户创建工具或 Onit 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户

若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Onit，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Onit”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-onit-tutorial/IC791182.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-onit-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Feb17_HO1-->


