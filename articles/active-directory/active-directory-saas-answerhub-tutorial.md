---
title: "教程：Azure Active Directory 与 AnswerHub 集成 | Microsoft Docs"
description: "了解如何将 AnswerHub 与 Azure Active Directory 结合使用以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 067d47bdfa5459a8e751292339a0e7ff4a8ad46b


---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>教程：Azure Active Directory 与 AnswerHub 集成
本教程旨在展示 Azure 与 [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) 集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用的订阅上的 [AnswerHub](http://www.dzonesoftware.com/products/answerhub-question-answer-software) 单一登录

完成本教程后，分配到 AnswerHub 的 Azure AD 用户将能够在 AnswerHub 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 支持 AnswerHubh 的应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-answerhub-tutorial/IC785165.png "Scenario")

## <a name="enabling-the-application-integration-for-answerhub"></a>支持 AnswerHubh 的应用程序集成
本部分旨在概述如何为 AnswerHub 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-answerhub-perform-the-following-steps"></a>若要支持 AnswerHub 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-answerhub-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-answerhub-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-answerhub-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-answerhub-tutorial/IC749322.png "Add an application from gallerry")
6. 在“搜索框”中，键入“AnswerHub”。
   
   ![应用程序库](./media/active-directory-saas-answerhub-tutorial/IC785166.png "Application Gallery")
7. 在“结果”窗格中，选择“AnswerHub”，然后单击“完成”，添加该应用程序。
   
   ![AnswerHub](./media/active-directory-saas-answerhub-tutorial/IC785167.png "AnswerHub")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分旨在概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 AnswerHub 进行身份验证。  
作为此过程的一部分，要求创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转化为文本文件](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“AnswerHub”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-answerhub-tutorial/IC785168.png "Configure single sign-on")
2. 在“你希望用户如何登录 AnswerHub”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-answerhub-tutorial/IC785169.png "Configure single sign-on")
3. 在“配置应用 URL”页上的“AnswerHub 登录 URL”文本框中，使用以下模式“*https://company.answerhub.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-answerhub-tutorial/IC785170.png "Configure App URL")
4. 在“配置 AnswerHub 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后在计算机上本地保存该证书文件。
   
   ![配置单一登录](./media/active-directory-saas-answerhub-tutorial/IC785171.png "Configure single sign-on")
5. 在其他 Web 浏览器窗口中，以管理员身份登录 AnswerHub 公司站点。
   
   > [!NOTE]
   > 如果需要帮助配置 AnswerHub，请联系 [AnswerHub 支持团队](mailto:success@answerhub.com.)。
   > 
   > 
6. 转到“管理”。
7. 单击“用户和组”选项卡。
8. 在左侧导航窗格的“社交设置”部分中，单击“SAML 设置”。
9. 单击“IDP 配置”选项卡。
10. 在“IDP 配置”选项卡上，执行以下步骤：
    
    ![SAML 设置](./media/active-directory-saas-answerhub-tutorial/IC785172.png "SAML Setup")
    
    1. 在 Azure 经典门户的“配置 AnswerHub 的单一登录”对话框页面上，复制“远程登录 URL”值，然后将其粘贴到“IDP 登录 URL”文本框。
    2. 在 Azure 经典门户的“配置 AnswerHub 的单一登录”对话框页面上，复制“远程注销 URL”值，然后将其粘贴到“IDP 注销 URL”文本框。
    3. 在 Azure 经典门户的“配置 AnswerHub 的单一登录”对话框页面上，复制“名称标识符格式”值，然后将其粘贴到“IDP 名称标识符格式”文本框。
    4. 单击“密钥和证书”。
11. 在“密钥和证书”选项卡上，执行以下步骤：
    
    ![密钥和证书](./media/active-directory-saas-answerhub-tutorial/IC785173.png "Keys and Certificates")
    
    1. 根据下载的证书创建 **base-64 编码**文件。  
       
       > [!TIP]
       > 有关详细信息，请参阅[如何将二进制证书转化为文本文件](http://youtu.be/PlgrzUZ-Y1o)
       > 
       > 
    2. 在记事本中打开 base-64 编码证书、将内容复制到剪贴板，然后将其粘贴到“IdP 公钥(x509 格式)”文本框。
    3. 单击“保存” 。
12. 在“IDP 配置”选项卡上，单击“保存”。
13. 在 Azure 经典门户中，选择单一登录配置确认，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-answerhub-tutorial/IC785174.png "Configure single sign-on")

## <a name="configuring-user-provisioning"></a>配置用户设置
为了使 Azure AD 用户能够登录 AnswerHub，必须对其进行预配才能使其登录 AnswerHub。  
就 AnswerHub 来说，预配任务需要手动完成。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 以管理员身份登录 **AnswerHub** 公司站点。
2. 转到“管理”。
3. 单击“用户和组”选项卡。
4. 在左侧导航窗格的“管理用户”部分中，单击“创建或导入用户”。
   
   ![用户和组](./media/active-directory-saas-answerhub-tutorial/IC785175.png "Users & Groups")
5. 键入希望在相关文本框中预配的有效 Azure Active Directory 帐户的“电子邮件地址”、“用户名”和“密码”，然后单击“保存”。

> [!NOTE]
> 可使用其他任何 AnswerHub 用户帐户创建工具或 AnswerHub 提供的 API 预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-answerhub-perform-the-following-steps"></a>若要将用户分配给 AnswerHub，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“AnswerHub”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-answerhub-tutorial/IC785176.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-answerhub-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


