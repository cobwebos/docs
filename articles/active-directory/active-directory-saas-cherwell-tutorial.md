---
title: "教程：Azure Active Directory 与 Cherwell 的集成 | Microsoft 文档"
description: "了解如何将 Cherwell 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: ad891f99-179e-4487-834d-35f3bc01c1ec
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2d8d925f80830a0d7047e9567fdd413af2e8c5c3
ms.openlocfilehash: 43acab94bfde65793c4ff944cb6db5dc5de47feb
ms.lasthandoff: 02/28/2017


---
# <a name="tutorial-azure-active-directory-integration-with-cherwell"></a>教程：Azure Active Directory 与 Cherwell 集成
本教程的目标是展示 Azure 与 Cherwell 的集成。 在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 启用了 Cherwell 单一登录 (SSO) 的订阅

完成本教程后，分配到 Cherwell 的 Azure AD 用户将能够在你的 Cherwell 公司站点上（服务提供商启动的登录）或者使用[访问面板简介](active-directory-saas-access-panel-introduction.md)以单一登录方式登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Cherwell 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-cherwell-tutorial/IC798988.png "方案")

## <a name="enable-the-application-integration-for-cherwell"></a>为 Cherwell 启用应用程序集成
本部分的目的是概述如何为 Cherwell 启用应用程序集成。

**若要为 Cherwell 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-cherwell-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-cherwell-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-cherwell-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-cherwell-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Cherwell”。
   
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798989.png "Cherwell")
7. 在结果窗格中，选择“Cherwell”，然后单击“完成”以添加该应用程序。
   
## <a name="configure-single-sign-on"></a>配置单一登录
   ![Cherwell](./media/active-directory-saas-cherwell-tutorial/IC798996.png "Cherwell")

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Cherwell 证明自己的身份。

**若要配置 SSO，请执行以下步骤：**

1. 在 Azure 经典门户中，在 **Cherwell** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-cherwell-tutorial/IC798990.png "配置单一登录")
2. 在“你希望用户如何登录到 Cherwell”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-cherwell-tutorial/IC798991.png "配置单一登录")
3. 在“配置应用 URL”页上，执行以下步骤：
   
   ![配置应用 URL](./media/active-directory-saas-cherwell-tutorial/IC798992.png "配置应用 URL")
  1. 在“登录 URL”文本框中，键入用户用来登录 **Cherwell** 的 URL（例如：*https://\<company name\>.cherwellondemand.com/cherwellclient*）。 
  2.  单击“下一步”。
4. 在“在 Cherwell 处配置单一登录”页上，执行以下步骤：
   
   ![配置单一登录](./media/active-directory-saas-cherwell-tutorial/IC798993.png "配置单一登录")
  1.  单击“下载证书”，然后将证书文件保存在计算机本地。
  2.  复制“标识提供者 URL”。
  3.  复制“单一登录服务 URL”。
  4.  单击“下一步”。
5. 将下载的证书、“标识提供者 URL”和“单一登录服务 URL”提交给 Cherwell 支持团队。
   
   >[!NOTE]
   >必须由 Cherwell 支持团队执行实际的 SSO 配置。 在为订阅启用了 SSO 后，你将收到通知。
   > 
6. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
  ![配置单一登录](./media/active-directory-saas-cherwell-tutorial/IC798994.png "配置单一登录")

## <a name="configure-user-provisioning"></a>配置用户设置
为了使 Azure AD 用户能够登录到 Cherwell，必须将其预配到 Cherwell 中。

对于 Cherwell，需要由 Cherwell 支持团队创建用户帐户。

>[!NOTE]
>可以使用 Cherwell 提供的任何其他 Cherwell 用户帐户创建工具或 API 来预配 Azure Active Directory 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

**若要将用户分配到 Cherwell，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在 **Cherwell** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-cherwell-tutorial/IC798995.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-cherwell-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


