---
title: "教程：Azure Active Directory 与 TimeOffManager 集成 | Microsoft 文档"
description: "了解如何使用 TimeOffManager 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/10/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ef606389a71e58b671fe84d91b0dbb22a35aad7a


---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>教程：Azure Active Directory 与 TimeOffManager 集成
本教程的目的是说明 Azure 与 TimeOffManager 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 TimeOffManager 单一登录的订阅

完成本教程后，已向 TimeOffManager 分配的 Azure AD 用户将能够在 TimeOffManager 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 TimeOffManager 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-timeoffmanager-tutorial/IC795909.png "Scenario")

## <a name="enabling-the-application-integration-for-timeoffmanager"></a>为 TimeOffManager 启用应用程序集成
本部分的目的是概述如何为 TimeOffManager 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-timeoffmanager-perform-the-following-steps"></a>若要为 TimeOffManager 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-timeoffmanager-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-timeoffmanager-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-timeoffmanager-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-timeoffmanager-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“TimeOffManager”。
   
   ![应用程序库](./media/active-directory-saas-timeoffmanager-tutorial/IC795910.png "Application Gallery")
7. 在结果窗格中，选择“TimeOffManager”，然后单击“完成”以添加该应用程序。
   
   ![TimeOffManager](./media/active-directory-saas-timeoffmanager-tutorial/IC795911.png "TimeOffManager")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 TimeOffManager 进行身份验证。  
在此过程中，需要将 base-64 编码的证书上载到 TimeOffManager 租户。  
如果不熟悉此过程，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“TimeOffManager”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-timeoffmanager-tutorial/IC795912.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 TimeOffManager”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-timeoffmanager-tutorial/IC795913.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上的“TimeOffManager 回复 URL”文本框中，键入 TimeOffManager AssertionConsumerService URL（例如：“*示例：https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company\_id=IC34216*”），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-timeoffmanager-tutorial/IC795914.png "Configure App URL")
   
   可以从 TimeOffManager 单一登录设置页获取回复 URL。
   
   ![单一登录设置](./media/active-directory-saas-timeoffmanager-tutorial/IC795915.png "Single Sign-On Settings")
4. 在“配置 TimeOffManager 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-timeoffmanager-tutorial/IC795916.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 TimeOffManager 公司站点。
6. 转到“帐户”\>“帐户选项”\>“单一登录设置”。
   
   ![单一登录设置](./media/active-directory-saas-timeoffmanager-tutorial/IC795917.png "Single Sign-On Settings")
7. 在“单一登录设置”部分中，执行以下步骤：
   
   ![单一登录设置](./media/active-directory-saas-timeoffmanager-tutorial/IC795918.png "Single Sign-On Settings")
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  基于下载的证书创建一个 **Base-64 编码**的文件。  
   
       >[AZURE.TIP] 有关详细信息，请参阅[如何将二进制证书转换为文本文件](http://youtu.be/PlgrzUZ-Y1o)
   
   b.  在记事本中打开 base-64 编码的证书，将其内容复制到剪贴板，然后将整个证书粘贴到“X.509 证书”文本框中。
   
   c.  在 Azure 经典门户的“配置 TimeOffManager 的单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“Idp 颁发者”文本框中。
   
   d.单击“下一步”。  在 Azure 经典门户的“配置 TimeOffManager 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“Idp 终结点 URL”文本框中。
   
   e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。  对于“强制实施 SAML”，选择“否”。

    f.  对于“自动创建用户”，选择“是”。

    g.  在 Azure 经典门户的“配置 TimeOffManager 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“注销 URL”文本框中。

    h.  单击“保存更改”。

1. 在 Azure 经典门户的“配置 TimeOffManager 的单一登录”对话框页上，选择“单一登录配置确认”，然后单击“完成”。
   
   ![配置单一登录](./media/active-directory-saas-timeoffmanager-tutorial/IC795919.png "Configure Single Sign-On")
2. 在顶部菜单中单击“属性”，打开“SAML 令牌属性”对话框。
   
   ![属性](./media/active-directory-saas-timeoffmanager-tutorial/IC795920.png "Attributes")
3. 若要添加所需的属性映射，请执行以下步骤：
   
   ![SAML 令牌属性](./media/active-directory-saas-timeoffmanager-tutorial/123.png "saml token attributes")
   
   | 属性名称 | 属性值 |
   | --- | --- |
   | 电子邮件 |User.mail |
   | 名 |User.givenname |
   | 姓 |User.surname |
   
   a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。  对于上表中的每个数据行，单击“添加用户属性”。
   
   b.  在“属性名称”文本框中，键入为该行显示的属性名称。
   
   c.  在“属性值”文本框中，选择为该行显示的属性值。
   
   d.单击“下一步”。  单击“完成”。
4. 单击“应用更改”。

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 TimeOffManager，必须将这些用户预配到 TimeOffManager 中。  
TimeOffManager 支持实时用户预配。 不存在任何操作项。  
在首次使用单一登录进行登录期间，将自动添加用户。

> [!NOTE]
> 可以使用任何其他 TimeOffManager 用户帐户创建工具或 TimeOffManager 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-timeoffmanager-perform-the-following-steps"></a>若要将用户分配到 TimeOffManager，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“TimeOffManager”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-timeoffmanager-tutorial/IC795922.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-timeoffmanager-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


