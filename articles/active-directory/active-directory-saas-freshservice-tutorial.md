---
title: "教程：Azure Active Directory 与 FreshService 集成 | Microsoft Docs"
description: "了解如何使用 FreshService 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/10/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 64421f1b7424751332b76508ae65d009a3e1f48b
ms.openlocfilehash: df90d455f2c3bdbc59c38447fe65bf1b64979249
ms.lasthandoff: 02/17/2017


---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>教程：Azure Active Directory 与 FreshService 集成
本教程的目的是说明 Azure 与 FreshService 的集成。  

在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 FreshService 单一登录 (SSO) 的订阅

完成本教程后，分配到 FreshService 的 Azure AD 用户可通过[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

* 为 FreshService 启用应用程序集成
* 配置单一登录
* 配置用户设置
* 分配用户

![方案](./media/active-directory-saas-freshservice-tutorial/IC790807.png "方案")

## <a name="enable-the-application-integration-for-freshservice"></a>为 FreshService 启用应用程序集成
本部分的目的是概述如何为 FreshService 启用应用程序集成。

**若要为 FreshService 启用应用程序集成，请执行以下步骤：**

1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-freshservice-tutorial/IC700993.png "Active Directory")
2. 在“目录”列表中，选择要启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-freshservice-tutorial/IC700994.png "应用程序")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-freshservice-tutorial/IC749321.png "添加应用程序")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库添加应用程序](./media/active-directory-saas-freshservice-tutorial/IC749322.png "从库添加应用程序")
6. 在搜索框中，键入“FreshService”。
   
   ![应用程序库](./media/active-directory-saas-freshservice-tutorial/IC790808.png "应用程序库")
7. 在结果窗格中，选择“FreshService”，然后单击“完成”以添加该应用程序。
   
   ![Freshservice](./media/active-directory-saas-freshservice-tutorial/IC790809.png "Freshservice")
   
## <a name="configure-single-sign-on"></a>配置单一登录

此部分的旨在概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 FreshService 进行身份验证。 

配置 FreshService 的单一登录需要检索证书的指纹值。 如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

**若要配置单一登录，请执行以下步骤：**

1. 在 Azure 经典门户中的“FreshService”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-freshservice-tutorial/IC790810.png "配置单一登录")
2. 在“你希望用户如何登录 FreshService”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-freshservice-tutorial/IC790811.png "配置单一登录")
3. 在“配置应用 URL”页的“FreshService 登录 URL”文本框中，键入用户用于登录 FreshService 应用程序的 URL（例如：“**http://democompany.freshservice.com/**”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-freshservice-tutorial/IC790812.png "配置应用 URL")
4. 在“配置 FreshService 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-freshservice-tutorial/IC790813.png "配置单一登录")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 FreshService 公司站点。
6. 在顶部菜单中，单击“管理员”。
   
   ![管理员](./media/active-directory-saas-freshservice-tutorial/IC790814.png "管理员")
7. 在“客户门户”中单击“安全”。
   
   ![安全](./media/active-directory-saas-freshservice-tutorial/IC790815.png "安全")
8. 在“安全”部分中，执行以下步骤：
   
   ![单一登录](./media/active-directory-saas-freshservice-tutorial/IC790816.png "单一登录")
   
   1. 切换“单一登录”。
   2. 选择“SAML SSO”。
   3. 在 Azure 经典门户的“配置 FreshService 的单一登录”对话框页面上，复制“远程登录 URL”值，然后将其粘贴到“SAML 登录 URL”文本框中。
   4. 在 Azure 经典门户的“配置 FreshService 的单一登录”对话框页面上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框中。
   5. 从导出的证书中复制“指纹”值，然后将其粘贴到“安全证书指纹”文本框中。
   
      >[!TIP]
      >有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)
      >
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-freshservice-tutorial/IC790817.png "配置单一登录")
   
## <a name="configure-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 FreshService，必须将这些用户预配到 FreshService 中。 对于 FreshService，需要手动执行预配。

**若要预配用户帐户，请执行以下步骤：**

1. 以管理员身份登录到 **FreshService** 公司站点。
2. 在顶部菜单中，单击“管理员”。
   
   ![管理员](./media/active-directory-saas-freshservice-tutorial/IC790814.png "管理员")
3. 在“用户管理”部分，单击“请求者”。
   
   ![请求者](./media/active-directory-saas-freshservice-tutorial/IC790818.png "请求者")
4. 单击“新建申请者”。
   
   ![新建请求者](./media/active-directory-saas-freshservice-tutorial/IC790819.png "New Requesters")
5. 在“新建请求者”部分中，执行以下步骤：
   
   ![新建请求者](./media/active-directory-saas-freshservice-tutorial/IC790820.png "新建请求者")   
   1. 在相关文本框中输入要预配的有效 Azure Active Directory 帐户的“名字”和“电子邮件”属性。
   2. 单击“保存” 。
   
    >[!NOTE]
    >Azure Active Directory 帐户持有者会收到一封电子邮件，其中包含用于在激活帐户前确认帐户的链接
    >  

>[!NOTE]
>可以使用 FreshService 提供的任何其他 FreshService 用户帐户创建工具或 API 来预配 AAD 用户帐户。
>  

## <a name="assign-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

**若要将用户分配到 FreshService，请执行以下步骤：**

1. 在 Azure 经典门户中，创建测试帐户。
2. 在“FreshService”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-freshservice-tutorial/IC790821.png "分配用户")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-freshservice-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


