---
title: "教程：Azure Active Directory 与 Central Desktop 集成 | Microsoft Docs"
description: "了解如何使用 Central Desktop 与 Azure Active Directory 以启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: b805d485-93db-49b4-807a-18d446c7090e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
ms.openlocfilehash: fe32c1d68040ceb9d9de2ad6c4a6dc9ea93f5aef
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-central-desktop"></a>教程：Azure Active Directory 与 Central Desktop 集成
本教程的目的是说明 Azure 与 Central Desktop 的集成。 在本教程中概述的方案假定已有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Central Desktop 单一登录的订阅 / Central Desktop 租户

在本教程中概述的方案由以下构建基块组成：

* 为 Central Desktop 启用应用程序集成
* 配置单一登录 (SSO)
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "方案")

## <a name="enable-the-application-integration-for-central-desktop"></a>为 Central Desktop 启用应用程序集成
本部分的目的是概述如何为 Central Desktop 启用应用程序集成。

**若要为 Central Desktop 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“Central Desktop”。
   
   ![应用程序库](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "应用程序库")
7. 在结果窗格中，选择“Central Desktop”，并单击“完成”添加该应用程序。
   
   ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
   
## <a name="configure-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Central Desktop 证明自己的身份。

在此过程中，需要将 base-64 编码的证书上传到 Central Desktop 租户。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“Central Desktop”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "配置单一登录")
2. 在“你希望用户如何登录 Central Desktop”页上，选择“Microsoft Azure AD 单一登录”，并单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "配置单一登录")
3. 在“配置应用 URL”对话框页上，执行以下步骤，并单击“下一步”： 
   
   1. 在“Central Desktop 登录 URL”文本框中，键入 Central Desktop 租户的 URL（例如：*http://contoso.centraldesktop.com*）。
   2. 在“Central Desktop 回复 URL”文本框中，键入 Central Desktop AssertionConsumerService URL（例如：https://contoso.centraldesktop.com/saml2-assertion.php）。
   
   >[!NOTE]
   >可以从 Central Desktop 元数据（例如：*http://contoso.centraldesktop.com*）获取该值。
   >  
   
   ![配置应用 URL](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "配置应用 URL")
4. 在“配置 Central Desktop 的单一登录”页上，要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
  ![配置单一登录](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "配置单一登录")
5. 登录到 **Central Desktop** 租户。
6. 转到“设置”，单击“高级”，并单击“单一登录”。
   
  ![设置 - 高级](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "Setup - Advanced")
7. 在“单一登录设置”页上，执行以下步骤：
   
  ![单一登录设置](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Single Sign On Settings")
   
  1. 选择“启用 SAML v2 单一登录”。
  2. 在 Azure 经典门户的“配置 Central Desktop 的单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“SSO URL”文本框中。
  3. 在 Azure 经典门户的“配置 Central Desktop 的单一登录”页上，复制“远程登录 URL”值，然后将其粘贴到“SSO 登录 URL”文本框中。
  4. 在 Azure 经典门户的“配置 Central Desktop 的单一登录”页上，复制“单一登录服务 URL”值，然后将其粘贴到“SSO 注销 URL”文本框中。
8. 在“消息签名验证方法”部分执行以下步骤：
   
   ![消息签名验证方法](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "Message Signature Verification Method")
   
  1. 选择“证书”。
  2. 从“SSO 证书”列表中，选择 **RSH SHA256**。
  3. 根据已下载的证书创建一个文本文件，复制文本文件的内容，然后将其粘贴到“SSO 证书”字段中。  
     >[!TIP]
     >有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
      >  
   4. 选择“显示 SAMLv2 登录页的链接”。
9. 单击“更新”。
10. 在 Azure 经典门户中，选择“单一登录配置确认”，并单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "配置单一登录")
    
## <a name="configure-user-provisioning"></a>配置用户设置

要使 AAD 用户能够登录，必须将这些用户预配到 Central Desktop 应用程序中。 本部分介绍如何在 Central Desktop 中创建 AAD 用户帐户。

**为 Central Desktop 预配用户帐户：**
1. 登录到 Central Desktop 租户。
2. 转到“人员”\>“内部成员”。
3. 单击“添加内部成员”。
   
  ![人员](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "人员")
4. 在“新成员的电子邮件地址”文本框中，键入要预配的 AAD 帐户，并单击“下一步”。
   
  ![新成员的电子邮件地址](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "Email Addresses of New Members")
5. 单击“添加内部成员”。
   
  ![添加内部成员](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "Add Internal Member")
   
   >[!NOTE]
   >已添加的用户会收到一封电子邮件，其中包含一个确认链接，需要用户单击来激活帐户。
   > 

>[!NOTE]
>可以使用 Central Desktop 提供的任何其他 Central Desktop 用户帐户创建工具或 API 来预配 AAD 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**要将用户分配到 Central Desktop，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Central Desktop”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "分配用户")
3. 选择测试用户，单击“分配”，并单击“是”确认分配。
   
   ![是](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。

