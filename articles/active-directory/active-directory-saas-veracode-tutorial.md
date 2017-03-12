---
title: "教程：Azure Active Directory 与 Veracode 集成 | Microsoft Docs"
description: "了解如何使用 Veracode 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: 8c8ac0af8a39afdd9755040d21585185ceca890e
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>教程：Azure Active Directory 与 Veracode 集成
本教程旨在说明 Azure 与 Veracode 的集成。 在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Veracode 单一登录的订阅

完成本教程后，已向 Veracode 分配的 Azure AD 用户将能够使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Veracode 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-veracode-tutorial/IC802903.png "方案")

## <a name="enabling-the-application-integration-for-veracode"></a>为 Veracode 启用应用程序集成
本部分旨在概述如何为 Veracode 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-veracode-perform-the-following-steps"></a>若要为 Veracode 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-veracode-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-veracode-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-veracode-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-veracode-tutorial/IC749322.png "从库添加应用程序")

6. 在“搜索框”中，键入“Veracode”。
   
    ![应用程序库](./media/active-directory-saas-veracode-tutorial/IC802904.png "应用程序库")

7. 在结果窗格中，选择“Veracode”，然后单击“完成”以添加该应用程序。
   
    ![Veracode](./media/active-directory-saas-veracode-tutorial/IC802905.png "Veracode")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分旨在概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户对 Veracode 进行身份验证。  
Veracode 应用程序需要特定格式的 SAML 断言，这要求向“SAML 令牌属性”配置添加自定义属性映射。  
以下屏幕截图显示一个示例。

![属性](./media/active-directory-saas-veracode-tutorial/IC802906.png "属性")

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Veracode”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-veracode-tutorial/IC802907.png "配置单一登录")

2. 在“你希望用户如何登录 Veracode”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-veracode-tutorial/IC802908.png "配置单一登录")

3. 在“配置应用设置”页上，单击“下一步”。
   
    ![配置应用设置](./media/active-directory-saas-veracode-tutorial/IC802909.png "配置应用设置")

4. 在“配置 Veracode 的单一登录”页上，若要下载证书，单击“下载证书”，然后将证书文件保存在本地计算机上。
   
    ![配置单一登录](./media/active-directory-saas-veracode-tutorial/IC802910.png "配置单一登录")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 Veracode 公司站点。

6. 在顶部菜单中，单击“设置”，然后单击“管理员”。
   
    ![管理](./media/active-directory-saas-veracode-tutorial/IC802911.png "Administration")

7. 单击“SAML”选项卡。

8. 在“组织 SAML 设置”部分中执行以下步骤：
   
    ![管理](./media/active-directory-saas-veracode-tutorial/IC802912.png "Administration")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 在 Azure 经典门户的“配置 Veracode 的单一登录”对话框页上，复制“颁发者 URL”值，然后将其粘贴到“颁发者”文本框中
   
    b.保留“数据库类型”设置，即设置为“共享”。 若要上传下载的证书，请单击“选择文件”。
   
    c. 选择“启用自助注册”。

9. 在“自助注册设置”部分，执行以下步骤，然后单击“保存”：
   
    ![管理](./media/active-directory-saas-veracode-tutorial/IC802913.png "Administration")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于“新用户激活”，选择“不需要激活”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 对于“用户数据更新”，选择“首选项 Veracode 用户数据”。
   
    c. 对于“SAML 属性的详细信息”，选择以下选项：
      * “用户角色”
      * “策略管理员”
      * “审阅者”
      * “安全主管”
      * “执行经理”
      * “提交者”
      * “创建者”
      * “所有扫描类型”
      * “团队成员身份”
      * “默认团队”

10. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
    
    ![配置单一登录](./media/active-directory-saas-veracode-tutorial/IC802914.png "配置单一登录")

11. 在顶部菜单中，单击“属性”，打开“SAML 令牌属性”对话框。
    
    ![属性](./media/active-directory-saas-veracode-tutorial/IC795920.png "属性")

12. 若要添加所需的属性映射，请执行以下步骤：
    
    ![属性](./media/active-directory-saas-veracode-tutorial/IC802906.png "属性")
    
    | 属性名称 | 属性值 |
    |:--- |:--- |
    | 名 |User.givenname |
    | 姓 |User.surname |
    | email |User.mail |
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 对于上表中的每个数据行，单击“添加用户属性”。

    b. 在“属性名称”文本框中，键入为该行显示的属性名称。

    c. 在“属性值”文本框中，选择为该行显示的属性值。

    d.单击“下一步”。 单击“完成”。

13. 单击“应用更改”。

## <a name="configuring-user-provisioning"></a>配置用户设置
为使 Azure AD 用户能够登录到 Veracode，必须将其预配到 Veracode。  
就 Veracode 来说，预配是自动化任务。  
不存在任何操作项。

如有必要，在第一次尝试单一登录时将自动创建用户。

> [!NOTE]
> 可以使用 Veracode 提供的任何其他 Veracode 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-veracode-perform-the-following-steps"></a>若要将用户分配给 Veracode，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“Veracode”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-veracode-tutorial/IC802915.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-veracode-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


