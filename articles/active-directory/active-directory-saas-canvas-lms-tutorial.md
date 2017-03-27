---
title: "教程：Azure Active Directory 与 Canvas LMS 集成 | Microsoft Docs"
description: "了解如何使用 Canvas LMS 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: bfed291c-a33e-410d-b919-5b965a631d45
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 325d92e493f6e011367d2c85b52c92838327101e
ms.openlocfilehash: 15310828b15f266527573809adae2ddbe4de60be
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-canvas-lms"></a>教程：Azure Active Directory 与 Canvas LMS 集成
本教程的目的是说明 Azure 与 Canvas 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 一个 Canvas 租户

完成本教程后，分配到 Canvas 的 Azure AD 用户将能够在 Canvas 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 Canvas 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-canvas-lms-tutorial/IC775984.png "方案")

## <a name="enable-the-application-integration-for-canvas"></a>为 Canvas 启用应用程序集成
本部分的目的是概述如何为 Canvas 启用应用程序集成。

**若要为 Canvas 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-canvas-lms-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-canvas-lms-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-canvas-lms-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-canvas-lms-tutorial/IC749322.png "从库添加应用程序")
6. 在“搜索框”中，键入“Canvas”。
   
   ![应用程序库](./media/active-directory-saas-canvas-lms-tutorial/IC775985.png "应用程序库")
7. 在“结果”窗格中，选择“Canvas”，然后单击“完成”以添加该应用程序。
   
   ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775986.png "Canvas")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Canvas 证明自己的身份。  

配置 Canvas 的 SSO 需要检索证书的指纹值。 如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Canvas”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-canvas-lms-tutorial/IC771709.png "配置单一登录")
2. 在“你希望用户如何登录 Canvas”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-canvas-lms-tutorial/IC775987.png "配置单一登录")
3. 在“配置应用 URL”页上，在“Canvas 登录 URL”文本框中，使用 `https://<tenant-name>.instructure.com` 模式键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-canvas-lms-tutorial/IC775988.png "配置应用 URL")
4. 在“配置 Canvas 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在计算机本地。
   
   ![配置单一登录](./media/active-directory-saas-canvas-lms-tutorial/IC775989.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Canvas 公司站点。
6. 转到“课程”**\>“托管帐户”\>**“Microsoft”。
   
   ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
7. 在左侧导航窗格中，选择“身份验证”，然后单击“添加新 SAML 配置”。
   
   ![身份验证](./media/active-directory-saas-canvas-lms-tutorial/IC775991.png "身份验证")
8. 在“当前集成”页上，执行以下步骤：
   
   ![当前集成](./media/active-directory-saas-canvas-lms-tutorial/IC775992.png "Current Integration")

  1. 在 Azure 经典门户的“配置 Canvas 的单一登录”对话框页上，复制“实体 ID”值，然后将其粘贴到“IDP 实体 ID”文本框中。
   2. 在 Azure 经典门户的“配置 Canvas 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框中。
   3. 在 Azure 经典门户的“配置 Canvas 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“注销 URL”文本框中。
   4. 在 Azure 经典门户的“配置 Canvas 的单一登录”对话框页面上，复制“更改密码 URL”值，然后将其粘贴到“更改密码链接”文本框。
   5. 从导出的证书中复制“指纹”值，然后将其粘贴到“证书指纹”文本框中。      
      >[!TIP]
      >有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI) 
      > 
   6. 从“登录名属性”列表中，选择 **NameID**。
   7. 从“标识符格式”列表中，选择 **emailAddress**。
   8. 单击“保存身份验证设置”。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-canvas-lms-tutorial/IC775993.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录到 Canvas，必须将其预配到 Canvas 中。

* 对于 Canvas，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 登录“Canvas”租户。
2. 转到“课程”**\>“托管帐户”\>**“Microsoft”。
   
   ![Canvas](./media/active-directory-saas-canvas-lms-tutorial/IC775990.png "Canvas")
3. 单击“用户”。
   
   ![用户](./media/active-directory-saas-canvas-lms-tutorial/IC775995.png "用户")
4. 单击“添加新用户”。
   
   ![用户](./media/active-directory-saas-canvas-lms-tutorial/IC775996.png "用户")
5. 在“添加新用户”对话框页上，执行以下步骤：
   
   ![添加用户](./media/active-directory-saas-canvas-lms-tutorial/IC775997.png "添加用户")
   
   1. 在“完整名称”文本框中，键入用户的名称。
   2. 在“电子邮件”文本框中，键入用户的电子邮件地址。
   3. 在“登录名”文本框中，键入用户的 Azure AD 电子邮件地址。
   4. 选择“通过电子邮件告知用户此帐户创建操作”。
   5. 单击“添加用户”。

>[!NOTE]
>可以使用 Canvas 提供的任何其他 Canvas 用户帐户创建工具或 API 来预配 AAD 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Canvas，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Canvas”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-canvas-lms-tutorial/IC775998.png "Assigning users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-canvas-lms-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


