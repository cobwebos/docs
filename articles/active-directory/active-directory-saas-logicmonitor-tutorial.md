---
title: "教程：Azure Active Directory 与 LogicMonitor 的集成 | Microsoft Docs"
description: "了解如何将 LogicMonitor 与 Azure Active Directory 配合使用来启用单一登录、自动化预配及更多内容！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 496156c3-0e22-4492-b36f-2c29c055e087
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6dbb19605f92ff0c0d58478a56e564bab71123e4


---
# <a name="tutorial-azure-active-directory-integration-with-logicmonitor"></a>教程：Azure Active Directory 与 LogicMonitor 的集成
本教程的目的是展示 Azure 与 LogicMonitor 的集成。  
在本教程中概述的方案假定已具有以下各项：

* 一个有效的 Azure 订阅
* 一个 LogicMonitor 租户

本教程中概述的方案包括以下构建基块：

1. 为 LogicMonitor 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-logicmonitor-tutorial/IC790045.png "Scenario")

## <a name="enabling-the-application-integration-for-logicmonitor"></a>为 LogicMonitor 启用应用程序集成
本部分的目的是概述如何为 LogicMonitor 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-logicmonitor-perform-the-following-steps"></a>若要为 LogicMonitor 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户中，在左侧导航窗格上，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-logicmonitor-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-logicmonitor-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-logicmonitor-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-logicmonitor-tutorial/IC749322.png "Add an application from gallerry")
6. 在**搜索框**中，键入“logicmonitor”。
   
   ![应用程序库](./media/active-directory-saas-logicmonitor-tutorial/IC790046.png "Application Gallery")
7. 在结果窗格中，选择“LogicMonitor”，然后单击“完成”以添加该应用程序。
   
   ![LogicMonitor](./media/active-directory-saas-logicmonitor-tutorial/IC790047.png "LogicMonitor")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 LogicMonitor 证明自己的身份。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中，在 **LogicMonitor** 应用程序集成页上，单击“配置单一登录”，以打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-logicmonitor-tutorial/IC790048.png "Configure Single Sign-On")
2. 在“你希望用户如何登录到 LogicMonitor”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-logicmonitor-tutorial/IC790049.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上，在“登录 URL”文本框中，键入用户用来登录 LogicMonitor 的 URL（\(例如：“*http://company.logicmonitor.com*”\)），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-logicmonitor-tutorial/IC790050.png "Configure App URL")
4. 在“在 LogicMonitor 处配置单一登录”页上，单击“下载元数据”，然后将其保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-logicmonitor-tutorial/IC790051.png "Configure Single Sign-On")
5. 以管理员身份登录到你的 **LogicMonitor** 公司站点。
6. 在顶部菜单中，单击“设置”。
   
   ![设置](./media/active-directory-saas-logicmonitor-tutorial/IC790052.png "Settings")
7. 在左侧导航栏中，单击“单一登录”。
   
   ![单一登录](./media/active-directory-saas-logicmonitor-tutorial/IC790053.png "Single Sign-On")
8. 在“单一登录(SSO)设置”部分中，执行以下步骤：
   
   ![单一登录设置](./media/active-directory-saas-logicmonitor-tutorial/IC790054.png "Single Sign-On Settings")
   
   1. 选择“启用单一登录”。
   2. 对于“默认角色分配”，选择“只读”。
   3. 在记事本中打开下载的元数据文件，然后将文件内容复制并粘贴到“标识提供者元数据”文本框中。
   4. 单击“保存更改”。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”以关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-logicmonitor-tutorial/IC790055.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户预配

要使 AAD 用户能够登录，必须使用其 Azure Active Directory 用户名将其预配到 LogicMonitor 应用程序。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户预配，请执行以下步骤：
1. 以管理员身份登录到你的 LogicMonitor 公司站点。
2. 在顶部菜单中，单击“设置”，然后单击“角色和用户”。
   
   ![角色和用户](./media/active-directory-saas-logicmonitor-tutorial/IC790056.png "Roles and Users")
3. 单击“添加”。
4. 在“添加帐户”部分中，执行以下步骤：
   
   ![添加帐户](./media/active-directory-saas-logicmonitor-tutorial/IC790057.png "Add an account")
   
   1. 将要预配的 Azure Active Directory 用户的**用户名**、**电子邮件**、**密码**和**重新键入密码**值键入到相关文本框中。
   2. 依次选择“角色”、“查看权限”和“状态”。
   3. 单击“提交”。

> [!NOTE]
> 可以使用 LogicMonitor 提供的任何其他 LogicMonitor 用户帐户创建工具或 API 来预配 Azure Active Directory 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配用户的方式向要允许其使用应用程序的 Azure AD 用户授予应用程序访问权限。

### <a name="to-assign-users-to-logicmonitor-perform-the-following-steps"></a>若要将用户分配到 LogicMonitor，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在 **LogicMonitor** 应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-logicmonitor-tutorial/IC790058.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-logicmonitor-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


