---
title: "教程：Azure Active Directory 与 PolicyStat 集成 | Microsoft 文档"
description: "了解如何使用 PolicyStat 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: af5eb0f1-1c8e-4809-b0c4-8ccfb915ca42
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: ecd58d8f1df60aff99d9ac2e0245d06f61e0b6bb
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-policystat"></a>教程：Azure Active Directory 与 PolicyStat 的集成
本教程的目的是介绍 Azure 与 PolicyStat 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* PolicyStat 租户

完成本教程以后，分配到 PolicyStat 的 Azure AD 用户就可以通过单一登录登录到 PolicyStat 公司站点的应用程序（通过服务提供商启动登录），或者参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）进行单一登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 PolicyStat 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-policystat-tutorial/IC808662.png "方案")

## <a name="enable-the-application-integration-for-policystat"></a>为 PolicyStat 启用应用程序集成
本部分的目的是概述如何为 PolicyStat 启用应用程序集成。

**若要为 PolicyStat 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-policystat-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-policystat-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-policystat-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“PolicyStat”。
   
   ![应用程序库](./media/active-directory-saas-policystat-tutorial/IC808627.png "应用程序库")
7. 在结果窗格中，选择“PolicyStat”，然后单击“完成”添加该应用程序。
   
   ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
   
## <a name="configure-single-sign-on"></a>配置单一登录

此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 PolicyStat 进行身份验证。  

PolicyStat 应用程序需要特定格式的 SAML 断言，这要求将自定义属性映射添加到 **SAML 令牌属性**配置。  

以下屏幕截图显示了相关示例。

![属性](./media/active-directory-saas-policystat-tutorial/IC808628.png "属性")

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“PolicyStat”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-policystat-tutorial/IC808629.png "配置单一登录")
2. 在“你希望用户如何登录 PolicyStat”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-policystat-tutorial/IC808630.png "配置单一登录")
3. 在“配置应用设置”页的“登录 URL”文本框中，键入用户用于登录 PolicyStat 应用程序的 URL（例如：*“https://demo-azure.policystat.com”*），然后单击“下一步”。
   
   ![配置应用设置](./media/active-directory-saas-policystat-tutorial/IC808631.png "配置应用设置")
4. 在“配置 PolicyStat 的单一登录”页上，单击“下载元数据”，然后将元数据文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-policystat-tutorial/IC808632.png "配置单一登录")
5. 在另一 Web 浏览器窗口中，以管理员身份登录到 PolicyStat 公司站点。
6. 单击“管理员”选项卡，然后单击左侧导航窗格中的“单一登录配置”。
   
   ![管理员菜单](./media/active-directory-saas-policystat-tutorial/IC808633.png "管理员菜单")
7. 在“设置”部分，选择“启用单一登录集成”。
   
   ![单一登录配置](./media/active-directory-saas-policystat-tutorial/IC808634.png "单一登录配置")
8. 单击“配置属性”，然后在“配置属性”部分执行以下步骤：
   
   ![单一登录配置](./media/active-directory-saas-policystat-tutorial/IC808635.png "单一登录配置")
   
   1. 在“用户名属性”文本框中，键入“uid”。
   2. 在“名字属性”文本框中，键入“firstname”。
   3. 在“姓氏属性”文本框中，键入“lastname”。
   4. 在“电子邮件属性”文本框中，键入“emailaddress”。
   5. 单击“保存更改”。
9. 单击“IDP 元数据”，然后在“IDP 元数据”部分执行以下步骤：
   
   ![单一登录配置](./media/active-directory-saas-policystat-tutorial/IC808635.png "单一登录配置")
   
   1. 打开下载的元数据文件，复制其内容，然后将其粘贴到“标识提供者元数据”文本框中。
   2. 单击“保存更改”。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-policystat-tutorial/IC771723.png "配置单一登录")
  
11. 在顶部菜单中，单击“属性”，打开“SAML 令牌属性”对话框。
    
    ![属性](./media/active-directory-saas-policystat-tutorial/IC795920.png "属性")
12. 若要添加所需的属性映射，请执行以下步骤：
    
    ![属性](./media/active-directory-saas-policystat-tutorial/IC804823.png "属性")
    
   1. 单击“添加用户属性”。
   2. 在“属性名称”文本框中，键入“uid”。
   3. 在“属性值”文本框中，选择“ExtractMailPrefix()”。    
   4. 从“邮件”列表中，选择“User.mail”。
   5. 单击“完成”。

##<a name="configure-user-provisioning"></a>配置用户设置

若要让 Azure AD 用户登录 PolicyStat，必须将其预配到 PolicyStat 中。  

PolicyStat 支持实时用户预配。 这意味着你不需手动将用户添加到 PolicyStat。 系统会在用户首次通过 SSO 登录时自动将其添加到其中。

>[!NOTE]
>可以使用任何其他 PolicyStat 用户帐户创建工具或 PolicyStat 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 PolicyStat，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“PolicyStat”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-policystat-tutorial/IC808636.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-policystat-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


