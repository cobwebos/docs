---
title: "教程：Azure Active Directory 与 Cisco Webex 的集成 | Microsoft 文档"
description: "了解如何将 Cisco Webex 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: ada8ffbbdc5565a517f5a06cb640a36aaf8879b4
ms.openlocfilehash: b44b1a5b3e988a51db3325ec8a181651fa84e768
ms.contentlocale: zh-cn
ms.lasthandoff: 02/17/2017

---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>教程：Azure Active Directory 与 Cisco Webex 的集成
本教程的目标是展示 Azure 与 Cisco Webex 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 有效的 Azure 订阅
* Cisco Webex 租户

完成本教程后，分配到 Cisco Webex 的 Azure AD 用户将能够在你的 Cisco Webex 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Cisco Webex 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "方案")

## <a name="enable-the-application-integration-for-cisco-webex"></a>为 Cisco Webex 启用应用程序集成
本部分的目的是概述如何为 Cisco Webex 启用应用程序集成。

**若要为 Cisco Webex 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Cisco Webex”。
   
   ![应用程序库](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "应用程序库")
7. 在结果窗格中，选择“Cisco Webex”，然后单击“完成”以添加该应用程序。
   
   ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Cisco Webex 证明自己的身份。  

在此过程中，需要创建 Base-64 编码的证书。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“Cisco Webex”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "配置单一登录")
2. 在“你希望用户如何登录到 Cisco Webex”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "配置单一登录")
3. 在“配置应用 URL”页上，执行以下步骤，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "配置应用 URL")   
   1. 在“登录 URL”文本框中，键入你的 Cisco Webex 租户 URL（例如：*http://contoso.webex.com*）。
   2. 在“Cisco Webex 回复 URL”文本框中，键入你的 **Cisco Webex AssertionConsumerService URL**（例如：*https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*）。
4. 在“在 Cisco Webex 处配置单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到你的 Cisco Webex 公司站点。
6. 在顶部菜单中，单击“网站管理”。
   
   ![网站管理](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "Site Administration")
7. 在“管理网站”部分中，单击“SSO 配置”。
   
   ![SSO 配置](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO Configuration")
8. 在“联合 Web SSO 配置”部分中，执行以下步骤：
   
   ![联合 SSO 配置](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "Federated SSO Configuration")  
   1. 从“联合身份验证协议”列表中，选择“SAML 2.0”。
   2. 基于下载的证书创建一个 **Base-64 编码**的文件。  
    >[!TIP]
    >有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
    >  
   3. 在记事本中打开 base-64 编码的证书，然后复制其内容。
   4. 单击“导入 SAML 元数据”，然后粘贴你的 base-64 编码的证书。
   5. 在 Azure 经典门户中，在“在 Cisco Webex 处配置单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“SAML 颁发者(IdP ID)”文本框中。
   6. 在 Azure 经典门户中，在“在 Cisco Webex 处配置单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“客户 SSO 服务登录 URL”文本框中。
   7. 从“NameID 格式”列表中，选择“电子邮件地址”。
   8. 在“AuthnContextClassRef”文本框中，键入“urn:oasis:names:tc:SAML:2.0:ac:classes:Password”。
   9. 在 Azure 经典门户中，在“在 Cisco Webex 处配置单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“客户 SSO 服务注销 URL”文本框中。
   10. 单击“更新”。
9. 在 Azure 经典门户中，在“在 Cisco Webex 处配置单一登录”对话框页上，选择“单一登录配置确认”，然后单击“完成”。
   
   ![配置单一登录](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Cisco Webex，必须将其预配到 Cisco Webex 中。  

* 对于 Cisco Webex，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 登录到你的 **Cisco Webex** 租户。
2. 转到“管理用户”\>“添加用户”。
   
   ![添加用户](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "添加用户")
3. 在“添加用户”部分中，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "添加用户")   
   1. 对于“帐户类型”，选择“主机”。
   2. 将现有 Azure AD 用户的信息键入到以下文本框中：“名字”、“姓氏”、“用户名”、“电子邮件”、“密码”、“确认密码”。
   3. 单击“添加”。

>[!NOTE]
>可以使用 Cisco Webex 提供的任何其他 Cisco Webex 用户帐户创建工具或 API 来预配 AAD 用户帐户。 
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Cisco Webex，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Cisco Webex”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


