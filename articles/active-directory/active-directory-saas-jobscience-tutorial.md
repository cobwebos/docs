---
title: "教程：Azure Active Directory 与 Jobscience 的集成 | Microsoft Docs"
description: "了解如何使用 Jobscience 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 77282dcc-bbe2-4728-953d-adb4ab6a713b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 53fc622aa659f157f82dd27c5d6d2d8c9d8cc94b
ms.openlocfilehash: c881e28adf09440e329ddcae366ca787c868502b
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-jobscience"></a>教程：Azure Active Directory 与 Jobscience 集成
本教程的目的是说明 Azure 与 Jobscience 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Jobscience 单一登录 (SSO) 的订阅

完成本教程后，已向 Jobscience 分配的 Azure AD 用户将能够在 Jobscience 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Jobscience 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-jobscience-tutorial/IC784341.png "方案")

## <a name="enable-the-application-integration-for-jobscience"></a>为 Jobscience 启用应用程序集成
本部分的目的是概述如何为 Jobscience 启用应用程序集成。

**若要为 Jobscience 启用应用程序集成，请执行以下步骤：**
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-jobscience-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-jobscience-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-jobscience-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“jobscience”。
   
   ![应用程序库](./media/active-directory-saas-jobscience-tutorial/IC784342.png "应用程序库")
7. 在结果窗格中，选择“Jobscience”，然后单击“完成”以添加该应用程序。
   
   ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Jobscience 进行身份验证。  

配置 Jobscience 的单一登录需要检索证书的指纹值。 如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

**若要配置单一登录，请执行以下步骤：**

1. 以管理员身份登录到 Jobscience 公司站点。
2. 转到“设置”。
   
   ![设置](./media/active-directory-saas-jobscience-tutorial/IC784358.png "设置")
3. 在左侧导航窗格中的“管理”部分中，单击“域管理”以展开相关部分，然后单击“我的域”，打开“我的域”页。 
   
   ![我的域](./media/active-directory-saas-jobscience-tutorial/IC767825.png "我的域")
4. 若要验证你的域是否已正确设置，请确保它在“步骤 4 部署到用户”中，并复查“我的域设置”。
   
   ![部署到用户的域](./media/active-directory-saas-jobscience-tutorial/IC784377.png "部署到用户的域")
5. 在另一个 Web 浏览器窗口中，登录到 Azure 经典门户。
6. 在“Jobscience”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/IC784360.png "配置单一登录")
7. 在“你希望用户如何登录 Jobscience”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/IC784361.png "配置单一登录")
8. 在“配置应用 URL”页上的“Jobscience 登录 URL”文本框中，使用模式“*http://company.my.salesforce.com*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-jobscience-tutorial/IC784362.png "配置应用 URL")
9. 在“配置 Jobscience 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/IC784363.png "配置单一登录")
10. 在 Jobscience 公司站点上，单击“安全控制”，然后单击“单一登录设置”。
    
    ![安全性控制](./media/active-directory-saas-jobscience-tutorial/IC784364.png "安全性控制")
11. 在“单一登录设置”部分中，执行以下步骤：
    
    ![单一登录设置](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Single Sign-On Settings")
    
    1. 选择“已启用 SAML”。
    2. 单击“新建” 。
12. 在“SAML 单一登录设置编辑”对话框中，执行以下步骤：
    
    ![SAML 单一登录设置](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML Single Sign-On Settings")
    
    1. 在“名称”文本框中，键入配置名称。
    2. 在 Azure 经典门户的“配置 Jobscience 单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者”文本框中。
    3. 在“实体 ID”文本框中，键入 **https://salesforce-jobscience.com**
    4. 单击“浏览”以上载 Azure AD 证书。
    5. 对于“SAML 标识类型”，选择“断言包含用户对象的联合 ID”。
    6. 对于“SAML 标识位置”，选择“标识位于 Subject 语句的 NameIdentifier 元素中”。
    7. 在 Azure 经典门户的“配置 Jobscience 单一登录”对话框页上，复制“远程登录 URL”的值，然后将其粘贴到“标识提供者登录 URL”文本框中。
    8. 在 Azure 经典门户的“配置 Jobscience 单一登录”对话框页上，复制“远程注销 URL”的值，然后将其粘贴到“标识提供者注销 URL”文本框中。
    9. 单击“保存” 。
13. 在左侧导航窗格中的“管理”部分中，单击“域管理”以展开相关部分，然后单击“我的域”，打开“我的域”页。 
    
    ![我的域](./media/active-directory-saas-jobscience-tutorial/IC767825.png "我的域")
14. 在“我的域”页上的“登录页品牌打造”部分中，单击“编辑”。
    
    ![登录页品牌打造](./media/active-directory-saas-jobscience-tutorial/IC767826.png "登录页品牌打造")
15. 在“登录页品牌打造”页上的“身份验证服务”部分中，会显示 **SAML SSO 设置**的名称。 选择它，然后单击“保存”。
    
    ![登录页品牌打造](./media/active-directory-saas-jobscience-tutorial/IC784366.png "登录页品牌打造")
16. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-jobscience-tutorial/IC784367.png "配置单一登录")

若要获取 SP 启动的单一登录的登录 URL，请单击“安全控制”菜单部分中的“单一登录设置”。

![安全性控制](./media/active-directory-saas-jobscience-tutorial/IC784368.png "安全性控制")

单击上一步中创建的 SSO 配置文件。 此页显示你的公司的单一登录 URL（例如 *https://companyname.my.salesforce.com?so=companyid*）。

## <a name="configure-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 Jobscience，必须将这些用户预配到 Jobscience 中。  
对于 Jobscience，预配是一项手动任务。

**若要配置用户预配，请执行以下步骤：**

1. 以管理员身份登录到 **Jobscience** 公司站点。
2. 转到“设置”。
   
   ![设置](./media/active-directory-saas-jobscience-tutorial/IC784358.png "设置")
3. 转到“管理用户”\>“用户”。
   
   ![用户](./media/active-directory-saas-jobscience-tutorial/IC784369.png "用户")
4. 单击“新建用户”。
   
   ![所有用户](./media/active-directory-saas-jobscience-tutorial/IC784370.png "所有用户")
5. 在“编辑用户”对话框中，执行以下步骤：
   
   ![用户编辑](./media/active-directory-saas-jobscience-tutorial/IC784371.png "用户编辑")
   
   1. 在相关文本框中键入要预配的 Azure AD 用户的名字、姓氏、别名、电子邮件、用户名和昵称属性。
   2. 单击“保存” 。
   
   >[!NOTE]
   >Azure AD 帐户持有者将收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接。
   > 

>[!NOTE]
>可以使用 Jobscience 提供的任何其他 Jobscience 用户帐户创建工具或 API 来预配 AAD 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Jobscience，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Jobscience”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-jobscience-tutorial/IC784372.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-jobscience-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


