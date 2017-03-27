---
title: "教程：Azure Active Directory 与 InsideView 集成 | Microsoft Docs"
description: "了解如何使用 InsideView 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 48a8cb0afd9fc2f9201169b074cf10f6d910952f
ms.openlocfilehash: 235b1f11c60a595dfc760701213cfc1b5f792b61
ms.lasthandoff: 02/17/2017


---

# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>教程：Azure Active Directory 与 InsideView 集成
本教程的目的是说明 Azure 与 InsideView 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* InsideView 租户

完成本教程后，已向 InsideView 分配的 Azure AD 用户将能够在 InsideView 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 InsideView 启用应用程序集成
* 配置单一登录
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-insideview-tutorial/IC794128.png "方案")

## <a name="enable-the-application-integration-for-insideview"></a>为 InsideView 启用应用程序集成
本部分的目的是概述如何为 InsideView 启用应用程序集成。

**若要为 InsideView 启用应用程序集成，请执行以下步骤：**
 
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-insideview-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-insideview-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-insideview-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-insideview-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“InsideView”。
   
   ![应用程序库](./media/active-directory-saas-insideview-tutorial/IC794129.png "应用程序库")
7. 在结果窗格中，选择“InsideView”，然后单击“完成”以添加该应用程序。
   
   ![InsideView](./media/active-directory-saas-insideview-tutorial/IC794130.png "InsideView")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 InsideView 进行身份验证。  
 
在此过程中，需要创建 base-64 编码的证书文件。 如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“InsideView”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-insideview-tutorial/IC794131.png "配置单一登录")
2. 在“你希望用户如何登录 InsideView”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-insideview-tutorial/IC794132.png "配置单一登录")
3. 在“配置应用 URL”页上的“InsideView 回复 URL”文本框中，键入 InsideView SSO URL（例如：`https://my.insideview.com/iv/<STS Name>/login.iv`），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-insideview-tutorial/IC794133.png "配置应用 URL")
4. 在“配置 InsideView 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-insideview-tutorial/IC794134.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 InsideView 公司站点。
6. 在顶部工具栏中，依次单击“管理”、“单一登录设置”、“添加 SAML”。
   
   ![SAML 单一登录设置](./media/active-directory-saas-insideview-tutorial/IC794135.png "SAML Single Sign On Settings")
7. 在“添加新 SAML”部分中，执行以下步骤：
   
   ![添加新 SAML](./media/active-directory-saas-insideview-tutorial/IC794136.png "Add a New SAML")
   
   1. 在“STS 名称”文本框中，键入配置名称。
   2. 在 Azure 经典门户中的“配置 InsideView 的单一登录”对话框页上，复制**服务提供商(SP)启动的终结点**值，然后将其粘贴到“SamlP/WS-Fed 主动提供的终结点”文本框。
   3. 基于下载的证书创建一个 **base-64 编码**的文件。      

     >[!TIP]
     >有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）。 
     >
   4. 在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将其粘贴到“STS 证书”文本框。
   5. 完成以下操作：
    * 在“Crm 用户 ID 映射”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress”。
    * 在“Crm 电子邮件映射”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress”。
    * 在“Crm 名字映射”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname”。
    * 在“Crm 姓氏映射”文本框中，键入“http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname”。
   9. 单击“保存” 。
8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-insideview-tutorial/IC794137.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 InsideView，必须将这些用户预配到 InsideView 中。 对于 InsideView，预配是一项手动任务。

若要在 InsideView 中创建用户或联系人，请联系客户成功经理或发送电子邮件至 **support@insideview.com**

>[!NOTE]
>可以使用 InsideView 提供的任何其他 InsideView 用户帐户创建工具或 API 来预配 Azure AD 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 InsideView，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“InsideView”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-insideview-tutorial/IC794138.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-insideview-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


