---
title: "教程：Azure Active Directory 与 Pagerduty 集成 | Microsoft 文档"
description: "了解如何使用 Pagerduty 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a7fae61f9b2dc400cbbd7054c11ec622f8683ccb
ms.lasthandoff: 04/03/2017


---

# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>教程：Azure Active Directory 与 Pagerduty 的集成
本教程的目的是介绍 Azure 与 Pagerduty 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Pagerduty 租户

完成本教程后，分配到 Pagerduty 的 Azure AD 用户将能够在 Pagerduty 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录 (SSO) 方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Pagerduty 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-pagerduty-tutorial/IC778528.png "方案")

## <a name="enabling-the-application-integration-for-pagerduty"></a>为 Pagerduty 启用应用程序集成
本部分的目的是概述如何为 Pagerduty 启用应用程序集成。

**若要为 Pagerduty 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 管理门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-pagerduty-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-pagerduty-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-pagerduty-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-pagerduty-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Pagerduty”。
   
   ![应用程序库](./media/active-directory-saas-pagerduty-tutorial/IC778529.png "应用程序库")
7. 在结果窗格中，选择“Pagerduty”，然后单击“完成”添加该应用程序。
   
   ![PagerDuty](./media/active-directory-saas-pagerduty-tutorial/IC778530.png "PagerDuty")
   
## <a name="configure-single-sign-on"></a>配置单一登录

此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Pagerduty 进行身份验证。  

在此过程中，需要创建 base-64 编码的证书文件。  

如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Pagerduty”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-pagerduty-tutorial/IC778531.png "配置单一登录")
2. 在“你希望用户如何登录 Pagerduty”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-pagerduty-tutorial/IC778532.png "配置单一登录")
3. 在“配置应用 URL”页的“Pagerduty 登录 URL”文本框中，使用“*https://\<tenant-name\>.Pagerduty.com*”模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-pagerduty-tutorial/IC778533.png "配置应用 URL")
4. 在“配置 Pagerduty 的单一登录”页上，请单击“下载证书”，然后在计算机上保存该证书文件。
   
   ![配置单一登录](./media/active-directory-saas-pagerduty-tutorial/IC778534.png "配置单一登录")
5. 在另一 Web 浏览器窗口中，以管理员身份登录到 Pagerduty 公司站点。
6. 在顶部菜单中，单击“帐户设置”。
   
   ![帐户设置](./media/active-directory-saas-pagerduty-tutorial/IC778535.png "帐户设置")
7. 单击“单一登录”。
   
   ![单一登录](./media/active-directory-saas-pagerduty-tutorial/IC778536.png "单一登录")
8. 在“启用单一登录(SSO)”页上，执行以下步骤：
   
   ![启用单一登录](./media/active-directory-saas-pagerduty-tutorial/IC778537.png "启用单一登录")
   
   1. 基于下载的证书创建一个 **base-64 编码**的文件。  
      
      >[!TIP]
      >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。
      >

  2. 在记事本中打开 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“X.509 证书”文本框中
  3. 在 Azure 经典门户的“配置 Pagerduty 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框中。
  4. 在 Azure 经典门户的“配置 Pagerduty 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框中。
  5. 选择“启用单一登录”。
  6. 单击“保存更改”。

9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-pagerduty-tutorial/IC778538.png "配置单一登录")
   

## <a name="configure-user-provisioning"></a>配置用户设置

若要让 Azure AD 用户登录 Pagerduty，必须将其预配到 Pagerduty 中。  

* 使用 Pagerduty 时，预配属手动任务。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到 **Pagerduty** 租户。
2. 在顶部菜单中，单击“用户”。
3. 单击“添加用户”。
   
   ![添加用户](./media/active-directory-saas-pagerduty-tutorial/IC778539.png "添加用户")
4. 在“邀请团队”对话框中，键入要预配的 Azure AD 用户的“姓名”和“电子邮件”地址，单击“添加”，然后单击“发送邀请”。
   
   ![邀请团队](./media/active-directory-saas-pagerduty-tutorial/IC778540.png "邀请团队")
   
   >[!NOTE]
   >所有已添加的用户都会收到创建 PagerDuty 帐户的邀请。
   > 
   > 

>[!NOTE]
>可以使用任何其他 Pagerduty 用户帐户创建工具或 Pagerduty 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Pagerduty，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Pagerduty”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-pagerduty-tutorial/IC778541.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-pagerduty-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)

