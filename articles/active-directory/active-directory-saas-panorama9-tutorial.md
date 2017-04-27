---
title: "教程：Azure Active Directory 与 Panorama9 集成 | Microsoft 文档"
description: "了解如何使用 Panorama9 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 5e28d7fa-03be-49f3-96c8-b567f1257d44
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/24/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 66d68ef805a28552c5d161b4761e2aa3052c118c
ms.lasthandoff: 04/03/2017


---
# <a name="tutorial-azure-active-directory-integration-with-panorama9"></a>教程：Azure Active Directory 与 Panorama9 的集成
本教程的目的是介绍 Azure 与 Panorama9 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用了 Panorama9 单一登录 (SSO) 的订阅

完成本教程以后，分配到 Panorama9 的 Azure AD 用户就可以通过单一登录登录到 Panorama9 公司站点的应用程序（通过服务提供商启动登录），或者参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）进行单一登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 Panorama9 启用应用程序集成
2. 配置单一登录 (SSO)
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-panorama9-tutorial/IC790016.png "方案")

## <a name="enable-the-application-integration-for-panorama9"></a>为 Panorama9 启用应用程序集成
本部分的目的是概述如何为 Panorama9 启用应用程序集成。

**若要为 Panorama9 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-panorama9-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-panorama9-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-panorama9-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-panorama9-tutorial/IC749322.png "从库添加应用程序")
6. 在**搜索框**中，键入“Panorama9”。
   
   ![应用程序库](./media/active-directory-saas-panorama9-tutorial/IC790017.png "应用程序库")
7. 在结果窗格中，选择“Panorama9”，然后单击“完成”添加该应用程序。
   
   ![Panorama9](./media/active-directory-saas-panorama9-tutorial/IC790018.png "Panorama9")
   
## <a name="configure-single-sign-on"></a>配置单一登录

此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Panorama9 进行身份验证。  

配置 Panorama9 的 SSO 需要检索证书的指纹值。  

如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

**若要配置 SSO，请执行以下步骤：**

1. 在 Azure 经典门户中的“Panorama9”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-panorama9-tutorial/IC790019.png "配置单一登录")
2. 在“你希望用户如何登录 Panorama9”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-panorama9-tutorial/IC790020.png "配置单一登录")
3. 在“配置应用 URL”页的“Panorama9 登录 URL”文本框中，键入用户登录 Panorama9 时使用的 URL（例如“*https://dashboard.panorama9.com/saml/access/3262*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-panorama9-tutorial/IC790021.png "配置应用 URL")
4. 在“配置 Panorama9 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后将其保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-panorama9-tutorial/IC790022.png "配置单一登录")
5. 在另一 Web 浏览器窗口中，以管理员身份登录到 Panorama9 公司站点。
6. 在顶部工具栏中，单击“管理”，然后单击“扩展”。
   
   ![扩展](./media/active-directory-saas-panorama9-tutorial/IC790023.png "扩展")
7. 在“扩展”对话框中，单击“单一登录”。
   
   ![单一登录](./media/active-directory-saas-panorama9-tutorial/IC790024.png "单一登录")
8. 在“设置”部分执行以下步骤：
   
   ![设置](./media/active-directory-saas-panorama9-tutorial/IC790025.png "设置")
   
   1. 在 Azure 经典门户的“配置 Panorama9 的单一登录”对话框页上，复制“单一登录服务 URL”值，然后将其粘贴到“标识提供者 URL”文本框中。
   2. 从导出的证书中复制“指纹”值，然后将其粘贴到“证书指纹”文本框中。    
   
      >[!TIP]
      >有关更多详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。
      > 
      
   3. 单击“保存” 。
9. 在 Azure AD 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-panorama9-tutorial/IC790026.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

若要让 Azure AD 用户登录 Panorama9，必须将其预配到 Panorama9 中。  

* 使用 Panorama9 时，预配属手动任务。

**若要配置用户预配，请执行以下步骤：**
1. 以管理员身份登录到 **Panorama9** 公司站点。
2. 在顶部菜单中，单击“管理”，然后单击“用户”。
   
  ![用户](./media/active-directory-saas-panorama9-tutorial/IC790027.png "用户")
3. 单击“+”。
4. 在“用户数据”部分执行以下步骤：
   
  ![用户](./media/active-directory-saas-panorama9-tutorial/IC790028.png "用户")

  1. 在“电子邮件”文本框中，键入要预配的有效 Azure Active Directory 用户的电子邮件地址。
  2. 单击“保存” 。

>[!NOTE]
>可以使用任何其他 Panorama9 用户帐户创建工具或 Panorama9 提供的 API 来预配 AAD 用户帐户。
>
>

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 Panorama9，请执行以下步骤：**

1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“Panorama9”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-panorama9-tutorial/IC790029.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-panorama9-tutorial/IC767830.png "是")

如果要测试 SSO 设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


