---
title: "教程：Azure Active Directory 与 AppDynamics 集成 | Microsoft Docs"
description: "了解如何将 AppDynamics 与 Azure Active Directory 结合使用以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 6d2c8c8170562f398ae0dbb7339b0e9bfce86313
ms.openlocfilehash: 1feed4aac0241096fcccd715e01418ff86b8c06d
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>教程：Azure Active Directory 与 AppDynamics 集成
本教程旨在展示 Azure 与 AppDynamics 集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 AppDynamics 单一登录 (SSO) 的订阅

完成本教程后，分配到 AppDynamics 的 Azure AD 用户将能够在 AppDynamics 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

* 支持 AppDynamics 的应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-appdynamics-tutorial/IC790209.png "方案")

## <a name="enabling-the-application-integration-for-appdynamics"></a>支持 AppDynamics 的应用程序集成
本部分旨在概述如何为 AppDynamics 启用应用程序集成。

**若要为 AppDynamics 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-appdynamics-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-appdynamics-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-appdynamics-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-appdynamics-tutorial/IC749322.png "从库添加应用程序")
6. 在“搜索框”中，键入“AppDynamics”。
   
   ![应用程序库](./media/active-directory-saas-appdynamics-tutorial/IC790210.png "应用程序库")
7. 在“结果”窗格中，选择“AppDynamics”，然后单击“完成”，添加该应用程序。
   
   ![AppDynamics](./media/active-directory-saas-appdynamics-tutorial/IC790211.png "AppDynamics")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分旨在概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 AppDynamics 进行身份验证。  

作为此过程的一部分，要求创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户的“AppDynamics”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-appdynamics-tutorial/IC790212.png "配置单一登录")
2. 在“你希望用户如何登录 AppDynamics”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-appdynamics-tutorial/IC790213.png "配置单一登录")
3. 在“配置应用 URL”页的“AppDynamics 登录 URL”文本框中，键入用户用于登录 AppDynamics 的 URL（“*https://companyname.saas.appdynamics.com*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-appdynamics-tutorial/IC790214.png "配置应用 URL")
4. 在“配置 AppDynamics 的单一登录”页上，若要下载证书，请单击“下载证书”，然后在计算机上保存该证书文件。
   
   ![配置单一登录](./media/active-directory-saas-appdynamics-tutorial/IC790215.png "配置单一登录")
5. 在其他 Web 浏览器窗口中，以管理员身份登录 AppDynamics 公司站点。
6. 在顶部工具栏中，单击“设置”，然后单击“管理”。
   
   ![管理](./media/active-directory-saas-appdynamics-tutorial/IC790216.png "Administration")
7. 单击“身份验证提供程序”选项卡。
   
   ![身份验证提供程序](./media/active-directory-saas-appdynamics-tutorial/IC790224.png "Authentication Provider")
8. 在“身份验证提供程序”部分中，执行以下步骤：
   
   ![SAML 配置](./media/active-directory-saas-appdynamics-tutorial/IC790225.png "SAML Configuration")   
   1. 对于“身份验证提供程序”，选择“SAML”。
   2. 在 Azure 经典门户的“配置 AppDynamics 的单一登录”对话框页面上，复制“远程登录 URL”值，然后将其粘贴到“登录 URL”文本框。
   3. 在 Azure 经典门户的“配置 AppDynamics 的单一登录”对话框页面上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框。
   4. 基于下载的证书创建一个 **base-64 编码**的文件。  
   
      >[!TIP]
      >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。
      > 
   5. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“证书”文本框中
   6. 单击“保存” 。

     ![保存](./media/active-directory-saas-appdynamics-tutorial/IC777673.png "保存")
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-appdynamics-tutorial/IC790226.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

为了使 Azure AD 用户能够登录 AppDynamics，必须对其进行预配才能使其登录 AppDynamics。  
就 AppDynamics 来说，预配任务需要手动完成。

**若要配置用户预配，请执行以下步骤：**

1. 以管理员身份登录 AppDynamics 公司站点。
2. 转到“用户”，然后单击 **+**，打开“创建用户”对话框。
   
   ![用户](./media/active-directory-saas-appdynamics-tutorial/IC790229.png "用户")
3. 在“创建用户”部分中，执行以下步骤：
   
   ![创建用户](./media/active-directory-saas-appdynamics-tutorial/IC790230.png "创建用户")
   
   1. 键入希望在相关文本框中预配的有效 AAD 帐户的“用户名”、“姓名”、“电子邮件”、“新密码”、“重复新密码”。
   2. 单击“保存” 。

>[!NOTE]
>可使用其他任何 AppDynamics 用户帐户创建工具或 AppDynamics 提供的 API 预配 AAD 用户帐户。
> 

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 AppDynamics，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“AppDynamics”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-appdynamics-tutorial/IC790231.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-appdynamics-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


