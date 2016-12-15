---
title: "教程：Azure Active Directory 与 ShiftPlanning 集成 | Microsoft 文档"
description: "了解如何使用 ShiftPlanning 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 6aa771e9-31c6-48d1-8dde-024bebc06943
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 403f297e7f0bfdde8ade3f35c401bcefb79ea2f5


---
# <a name="tutorial-azure-active-directory-integration-with-shiftplanning"></a>教程：Azure Active Directory 与 ShiftPlanning 集成
本教程的目的是说明 Azure 与 ShiftPlanning 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 ShiftPlanning 单一登录的订阅

完成本教程后，已向 ShiftPlanning 分配的 Azure AD 用户将能够在 ShiftPlanning 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 ShiftPlanning 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scenario")

## <a name="enabling-the-application-integration-for-shiftplanning"></a>为 ShiftPlanning 启用应用程序集成
本部分的目的是概述如何为 ShiftPlanning 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-shiftplanning-perform-the-following-steps"></a>若要为 ShiftPlanning 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加应用程序](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“ShiftPlanning”。
   
   ![应用程序库](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Application Gallery")
7. 在结果窗格中，选择“ShiftPlanning”，然后单击“完成”以添加该应用程序。
   
   ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
   
   ## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 ShiftPlanning 进行身份验证。  
在此过程中，需要创建 base-64 编码的证书文件。  
如果不熟悉此过程，请参阅[如何将二进制证书转化为文本文件](http://youtu.be/PlgrzUZ-Y1o)

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“ShiftPlanning”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configure Single Sign-On")
2. 在“你希望用户如何登录 ShiftPlanning”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configure Single Sign-On")
3. 在“配置应用 URL”页上的“ShiftPlanning 登录 URL”文本框中，使用模式“*https://company.shiftplanning.com/includes/saml/*”键入 URL，然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configure App URL")
4. 在“配置 ShiftPlanning 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将该证书文件保存到计算机上。
   
   ![配置单一登录](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configure Single Sign-On")
5. 在另一个 Web 浏览器窗口中，以管理员身份登录 **ShiftPlanning** 公司站点。
6. 在顶部菜单中，单击“管理员”。
   
   ![管理员](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")
7. 在“集成”下，单击“单一登录”。
   
   ![单一登录](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")
8. 在“单一登录”部分中，执行以下步骤：
   
   ![单一登录](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")
   
   1. 选择“已启用 SAML”。
   2. 选择“允许密码登录”
   3. 在 Azure 经典门户的“配置 ShiftPlanning 的单一登录”对话框页上，复制“远程登录 URL”值，然后将其粘贴到“SAML 颁发者 URL”文本框中。
   4. 在 Azure 经典门户的“配置 ShiftPlanning 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“远程注销 URL”文本框中。
   5. 基于下载的证书创建一个 **base-64 编码**的文件。  
      
      > [!TIP]
      > 有关详细信息，请参阅 [How to convert a binary certificate into a text file](http://youtu.be/PlgrzUZ-Y1o)（如何将二进制证书转换为文本文件）
      > 
      > 
   6. 在记事本中打开 base-64 编码证书，将其内容复制到剪贴板，然后再粘贴到“X.509 证书”文本框中
   7. 单击“保存设置”。
9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configure Single Sign-On")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 ShiftPlanning，必须将这些用户预配到 ShiftPlanning 中。  
对于 ShiftPlanning，预配是一项手动任务。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 以管理员身份登录 **ShiftPlanning** 公司站点。
2. 单击“管理”。
   
   ![管理员](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")
3. 单击“人员”。
   
   ![人员](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Staff")
4. 在“操作”下，单击“添加员工”。
   
   ![添加员工](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Add Employees")
5. 在“添加员工”部分中，执行以下步骤：
   
   ![保存员工](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Save Employees")
   
   1. 将要预配的有效 AAD 帐户的**名字**、**姓氏**和**电子邮件**键入到相关文本框中。
   2. 单击“保存员工”。

> [!NOTE]
> 可以使用任何其他 ShiftPlanning 用户帐户创建工具或 ShiftPlanning 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-shiftplanning-perform-the-following-steps"></a>若要将用户分配到 ShiftPlanning，请执行以下步骤：
1. 在 Azure 经典门户中，创建一个测试帐户。
2. 在“ShiftPlanning”应用程序集成页上，单击“分配用户”。
   
   ![分配用户](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Assign Users")
3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
   ![是](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Nov16_HO3-->


