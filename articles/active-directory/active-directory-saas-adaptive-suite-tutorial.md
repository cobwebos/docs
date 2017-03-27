---
title: "教程：Azure Active Directory 与 Adaptive Suite 集成 | Microsoft Docs"
description: "了解如何将 Adaptive Suite 与 Azure Active Directory 结合使用以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/12/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 9e83f60701158f791811ce368ee3541358215071
ms.openlocfilehash: aecd4ebb1e905c7341bd02d8db451eb82d64604b
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-adaptive-suite"></a>教程：Azure Active Directory 与 Adaptive Suite 集成
本教程旨在展示 Azure 与 Adaptive Suite 集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Adaptive Suite 租户

完成本教程后，分配到 Adaptive Suite 的 Azure AD 用户将能够在 Adaptive Suite 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

* 支持 Adaptive Suite 的应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-adaptive-suite-tutorial/IC805637.png "方案")

## <a name="enable-the-application-integration-for-adaptive-suite"></a>为 Adaptive Suite 启用应用程序集成
本部分旨在概述如何为 Adaptive Suite 启用应用程序集成。

**若要为 Adaptive Suite 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-adaptive-suite-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-adaptive-suite-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-adaptive-suite-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-adaptive-suite-tutorial/IC749322.png "从库添加应用程序")
6. 在“搜索”框中，键入“Adaptive Suite”。
   
   ![应用程序库](./media/active-directory-saas-adaptive-suite-tutorial/IC805638.png "应用程序库")
7. 在“结果”窗格中，选择“Adaptive Suite”，然后单击“完成”，添加该应用程序。
   
   ![Adaptive Suite](./media/active-directory-saas-adaptive-suite-tutorial/IC805639.png "Adaptive Suite")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分旨在概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 Adaptive Suite 进行身份验证。

配置 Adaptive Suite 的单一登录要求检索证书的指纹值。 如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Adaptive Suite”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-adaptive-suite-tutorial/IC805640.png "配置单一登录")
2. 在“你希望用户如何登录 Adaptive Suite”页面上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-adaptive-suite-tutorial/IC805641.png "配置单一登录")
3. 在“配置应用设置”页面的“回复 URL”文本框中，使用以下模式“*https://login.adaptiveinsights.com:443/samlsso/RlJFRVRSSUFMMTI3MTE=*”键入 URL，然后单击“下一步”。
   
>[!NOTE]
> 可从 Adaptive Suite 的 **SAML SSO 设置**页面获取此值。
>  
   
  ![配置应用设置](./media/active-directory-saas-adaptive-suite-tutorial/IC805642.png "配置应用设置")
4. 在“配置 Adaptive Suite 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后在计算机上本地保存该证书文件。
   
  ![配置单一登录](./media/active-directory-saas-adaptive-suite-tutorial/IC805643.png "配置单一登录")
5. 在其他 Web 浏览器窗口中，以管理员身份登录 Adaptive Suite 公司站点。
6. 转到“管理员”。
   
  ![管理员](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "管理员")
7. 在“用户和角色”部分中，单击“管理 SAML SSO 设置”。
   
  ![管理 SAML SSO 设置](./media/active-directory-saas-adaptive-suite-tutorial/IC805645.png "管理 SAML SSO 设置")
8. 在“SAML SSO 设置”页上，执行以下步骤：
   
  ![SAML SSO 设置](./media/active-directory-saas-adaptive-suite-tutorial/IC805646.png "SAML SSO 设置")
   
  1. 在“标识提供者名称”文本框中，键入配置名称。
  2. 在 Azure 经典门户的“配置 Adaptive Suite 的单一登录”对话框页面上，复制“实体 ID”值，然后将其粘贴到“标识提供者实体 ID”文本框。
  3. 在 Azure 经典门户的“配置 Adaptive Suite 的单一登录”对话框页面上，复制“SAML SSO URL”值，然后将其粘贴到“标识提供者 SSO URL”文本框。
  4. 在 Azure 经典门户的“配置 Adaptive Suite 的单一登录”对话框页面上，复制“SAML SSO URL”值，然后将其粘贴到“自定义注销 URL”文本框。
  5. 若要上传所下载的证书，请单击“选择文件”。
  6. 针对各对象，选择以下项：
    * 对于“SAML 用户 ID”，请选择“用户的 Adaptive Insights 用户名称”。
    * 对于“SAML 用户 ID 位置”，请选择“主题 NameID 中的用户 ID”。
    * 对于“SAML NameID 格式”，请选择“电子邮件地址”。
    * 对于“启用 SAML”，请选择“允许 SAML SSO 和直接 Adaptive Insights 登录”。
   7. 单击“保存” 。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-adaptive-suite-tutorial/IC805647.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录 Adaptive Suite，必须对其进行预配才能使其登录 Adaptive Suite。  

* 就 Adaptive Suite 来说，预配任务需要手动完成。

**若要配置用户设置，请执行以下步骤：** 

1. 以管理员身份登录 **Adaptive Suite** 公司站点。
2. 转到“管理员”。
   
   ![管理员](./media/active-directory-saas-adaptive-suite-tutorial/IC805644.png "管理员")
3. 在“用户和角色”部分中，单击“添加用户”。
   
   ![添加用户](./media/active-directory-saas-adaptive-suite-tutorial/IC805648.png "添加用户")
4. 在“新建用户”部分中，执行以下步骤：
   
   ![提交](./media/active-directory-saas-adaptive-suite-tutorial/IC805649.png "提交")   
  1. 键入希望在相关文本框中预配的有效 Azure Active Directory 用户的“姓名”、“登录名”、“电子邮件”、“密码”。
  2. 选择“角色”。
  3. 单击“提交”。

>[!NOTE]
>可以使用其他任何 Adaptive Suite 用户帐户创建工具或 Adaptive Suite 提供的 API 来预配 AAD 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Adaptive Suite，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Adaptive Suite”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-adaptive-suite-tutorial/IC805650.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-adaptive-suite-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


