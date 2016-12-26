---
title: "教程：Azure Active Directory 与 Citrix GoToMeeting 的集成 | Microsoft Docs"
description: "了解如何使用 Citrix GoToMeeting 与 Azure Active Directory 以启用单一登录、自动化预配及更多功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 7d7897f6-b88e-4dd5-8f3a-e612337b1413
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/18/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 5b775b3cb53086daea6258cccc15cd77fd0e68d9
ms.openlocfilehash: c4f33f3f856c5017c5b156e2b27b3b82a275afa6


---
# <a name="tutorial-azure-active-directory-integration-with-citrix-gotomeeting"></a>教程：Azure Active Directory 与 Citrix GoToMeeting 的集成

本教程的目标是展示 Azure 与 Citrix GoToMeeting 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Citrix GoToMeeting 中的租户

在本教程中概述的方案由以下构建基块组成：

1. 为 Citrix GoToMeeting 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![配置](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768996.png "Configuration")

## <a name="enabling-the-application-integration-for-citrix-gotomeeting"></a>为 Citrix GoToMeeting 启用应用程序集成
本部分的目的是概述如何为 Citrix GoToMeeting 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-citrix-gotomeeting-perform-the-following-steps"></a>若要为 Citrix GoToMeeting 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700993.png "Active Directory")

2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC700994.png "Applications")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749321.png "Add application")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC749322.png "Add an application from gallery")

6. 在“搜索”框中，键入“Citrix GoToMeeting”。
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701006.png "Citrix GoToMeeting")

7. 在结果窗格中，选择“Citrix GoToMeeting”，然后单击“完成”以添加该应用程序。
   
    ![Citrix GoToMeeting](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC701012.png "Citrix GoToMeeting")
   
## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Citrix GoToMeeting 进行身份验证。  
在此过程中，需要将 base-64 编码的证书上传到 Citrix GoToMeeting 租户。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在“Citrix GoToMeeting”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![启用单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768997.png "Enable single sign-on")

2. 在“你希望用户如何登录 Citrix GoToMeeting”页上，选择“Microsoft Azure AD 单一登录”。
   
    ![配置单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768998.png "Configure single sign-on")

3. 在“配置应用设置”页上，单击“下一步”。 
   
    ![启用单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689981.png "Enable single sign-on")

4. 在“配置 Citrix GoToMeeting 的单一登录”页上，请单击“下载证书”，然后在计算机上保存该证书文件。
   
    ![配置单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC768999.png "Configure single sign-on")

5. 在不同的浏览器窗口，登录到你的 [Citrix 组织中心](https://account.citrixonline.com/organization/administration/)。

6. 单击“标识提供者”选项卡，然后执行以下步骤：  
   
    ![SAML 设置](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC6892321.png "SAML setup")
   
    a. 选择“手动”

    b.保留“数据库类型”设置，即设置为“共享”。 在 Azure 经典门户的“配置 Citrix GoToMeeting 的单一登录”对话框页上，复制“登录页 URL”值，然后将其粘贴到“登录页 URL”文本框中。 

    c. 在 Azure 经典门户的“配置 Citrix GoToMeeting 的单一登录”对话框页上，复制“注销页 URL”值，然后将其粘贴到“注销页 URL”文本框中。

    d.单击“下一步”。 在 Azure 经典门户的“配置 Citrix GoToMeeting 的单一登录”对话框页面上，复制“实体 ID”值，然后将其粘贴到“标识提供者实体 ID”文本框中。

    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 若要上传所下载的证书，请单击“上传证书”。

    f. 单击“保存” 。


1. 在 Azure 经典门户上，选择“单一登录配置确认”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769000.png "Configure single sign-on")

2. 在“单一登录确认”页上，单击“完成”。
   
    ![SAML 设置](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC7689982.png "SAML setup")

## <a name="configuring-user-provisioning"></a>配置用户设置
本部分的目的是概述如何对 Citrix GoToMeeting 启用 Active Directory 用户帐户的预配。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 在 Azure 经典门户中的“Citrix GoToMeeting”应用程序集成页上，单击“配置用户预配”，打开“配置用户预配”对话框。
   
    ![配置用户设置](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769001.png "Configure user provisioning")

2. 在“设置和管理员凭据”页上，执行以下步骤：
   
    ![配置用户设置](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769002.png "Configure user provisioning")
   
    a. 在“Citrix GoToMeeting 管理员用户名”文本框中，键入管理员的用户名。

    b.保留“数据库类型”设置，即设置为“共享”。 在“Citrix GoToMeeting 管理员密码”文本框中，键入管理员密码。

    c. 单击“资源组名称” 的 Azure 数据工厂。

1. 在“确认”页上，单击复选标记以保存配置。
2. 单击“验证”按钮以验证配置。

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-citrix-gotomeeting-perform-the-following-steps"></a>若要将用户分配到 Citrix GoToMeeting，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Citrix GoToMeeting”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769003.png "Assign users")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC767830.png "Yes")

现在应等待 10 分钟，然后验证该帐户是否已同步到 Dropbox for Business。

作为第一个验证步骤，可以通过在 Azure 经典门户中的“Citrix GoToMeeting”应用程序集成页上单击“仪表板”检查预配状态。

![仪表板](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769004.png "Dashboard")

成功完成的用户预配周期将由一个相关状态予以指示：

![集成状态](./media/active-directory-saas-citrix-gotomeeting-tutorial/IC769005.png "Integration status")

如果要测试单一登录设置，请打开访问面板。

有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://msdn.microsoft.com/library/dn308586)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


