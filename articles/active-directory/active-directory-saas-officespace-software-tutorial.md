---
title: "教程：Azure Active Directory 与 OfficeSpace Software 集成 | Microsoft 文档"
description: "了解如何使用 OfficeSpace Software 与 Azure Active Directory 以启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 237ac5a0-5488-4a58-b420-c6d2c78d532c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9049186d485f07fbe3a9fa6d18fdc0e79aaa46b3


---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>教程：Azure Active Directory 与 OfficeSpace Software 的集成
本教程的目的是介绍 Azure 与 OfficeSpace Software 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 启用 OfficeSpace Software 单一登录的订阅

完成本教程以后，分配到 OfficeSpace Software 的 Azure AD 用户就可以通过单一登录登录到 OfficeSpace Software 公司站点的应用程序（通过服务提供商启动登录），或者参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）进行单一登录。

在本教程中概述的方案由以下构建基块组成：

1. 为 OfficeSpace Software 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Scenario")

## <a name="enabling-the-application-integration-for-officespace-software"></a>为 OfficeSpace Software 启用应用程序集成
本部分的目的是概述如何为 OfficeSpace Software 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-officespace-software-perform-the-following-steps"></a>若要为 OfficeSpace Software 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“OfficeSpace Software”。
   
   ![应用程序库](./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Application gallery")
7. 在结果窗格中，选择“OfficeSpace Software”，然后单击“完成”添加该应用程序。
   
   ![OfficeSpace Software](./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

此部分的目的是概述如何使用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 OfficeSpace Software 进行身份验证。  
配置  OfficeSpace Software 的单一登录要求检索证书的指纹值。  
如果不熟悉此步骤，请参阅 [How to retrieve a certificate's thumbprint value](http://youtu.be/YKQF266SAxI)（如何检索证书的指纹值）。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“OfficeSpace Software”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configure single sign=on")
2. 在“你希望用户如何登录 OfficeSpace Software”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configure single sign-on")
3. 在“配置应用 URL”页的“OfficeSpace Software 登录 URL”文本框中，键入用户用于登录 OfficeSpace Software 应用程序的 URL（例如：“*https://company.officespacesoftware.com*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configure App URL")
4. 在“配置 OfficeSpace Software 的单一登录”页面上，若要下载证书，请单击“下载证书”，然后将证书文件保存在本地计算机上。
   
   ![配置单一登录](./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configure single sign-on")
5. 在另一 Web 浏览器窗口中，以管理员身份登录到 OfficeSpace Software 公司站点。
6. 转到**“管理员”\>“连接器”**。
   
   ![管理员](./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Admin")
7. 单击“SAML 授权”。
   
   ![连接器](./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Connectors")
8. 在“SAML 授权”部分执行以下步骤：
   
   ![SAML 配置](./media/active-directory-saas-officespace-software-tutorial/IC777771.png "SAML configuration")
   
   1. 在 Azure 经典门户的“配置 OfficeSpace Software 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“注销提供程序 URL”文本框中。
   2. 在 Azure 经典门户的“配置 OfficeSpace Software 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“客户端 idp 目标 URL”文本框中。
   3. 从导出的证书中复制“指纹”值，然后将其粘贴到“客户端 idp 证书指纹”文本框中。  
      
      > [!TIP]
      > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)
      > 
      > 
   4. 单击“保存设置”。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

若要让 Azure AD 用户登录 OfficeSpace Software，必须将其预配到 OfficeSpace Software 中。 使用 OfficeSpace Software 时，预配属自动化任务。  
不存在任何操作项。  
如有必要，在第一次尝试单一登录时将自动创建用户。

> [!NOTE]
> 可以使用任何其他 OfficeSpace Software 用户帐户创建工具或 OfficeSpace Software 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-officespace-software-perform-the-following-steps"></a>若要将用户分配到 OfficeSpace Software，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“OfficeSpace Software”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
   ![是](./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


