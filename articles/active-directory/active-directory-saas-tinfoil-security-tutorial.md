---
title: "教程：Azure Active Directory 与 Tinfoil Security 集成 | Microsoft 文档"
description: "了解如何使用 Tinfoil Security 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: da02da92-e3b0-4c09-ad6c-180882b0f9f8
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/22/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 1c22e4fc17226578aaaf272fdf79178da65c63c2
ms.openlocfilehash: e3a93cc039d4e24d19f2df2d859c5899cd2a402e
ms.lasthandoff: 02/23/2017


---
# <a name="tutorial-azure-active-directory-integration-with-tinfoil-security"></a>教程：Azure Active Directory 与 Tinfoil Security 集成
本教程的目的是说明 Azure 与 Tinfoil Security 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* 已启用 Tinfoil Security 单一登录的订阅

完成本教程后，已向 Tinfoil Security 分配的 Azure AD 用户将能够在 Tinfoil Security 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Tinfoil Security 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![配置单一登录](./media/active-directory-saas-tinfoil-security-tutorial/IC798965.png "配置单一登录")

## <a name="enabling-the-application-integration-for-tinfoil-security"></a>为 Tinfoil Security 启用应用程序集成
本部分的目的是概述如何为 Tinfoil Security 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-tinfoil-security-perform-the-following-steps"></a>若要为 Tinfoil Security 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-tinfoil-security-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-tinfoil-security-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-tinfoil-security-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-tinfoil-security-tutorial/IC749322.png "从库添加应用程序")

6. 在搜索框中，键入“Tinfoil Security”。
   
    ![应用程序库](./media/active-directory-saas-tinfoil-security-tutorial/IC798966.png "应用程序库")

7. 在结果窗格中，选择“Tinfoil Security”，然后单击“完成”以添加该应用程序。
   
    ![Tinfoil Security](./media/active-directory-saas-tinfoil-security-tutorial/IC802771.png "Tinfoil Security")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Tinfoil Security 进行身份验证。  
配置 Tinfoil Security 的单一登录需要检索证书的指纹值。  
如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Tinfoil Security”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-tinfoil-security-tutorial/IC798967.png "配置单一登录")

2. 在“你希望用户如何登录 Tinfoil Security”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-tinfoil-security-tutorial/IC798968.png "配置单一登录")

3. 在“配置应用 URL”页上的“Tinfoil Security 回复 URL”文本框中，键入 Tinfoil Security 断言使用者服务 (ACS) URL（例如：“*https://www.tinfoilsecurity.com/saml/consume*”，然后单击“下一步”。
   
    > [!NOTE]
    > 应能够从 Tinfoil Security 元数据 (https://www.tinfoilsecurity.com/saml/metadata) 中获取 ACS URL。
    > 
    > 
   
    ![配置应用 URL](./media/active-directory-saas-tinfoil-security-tutorial/IC798969.png "配置应用 URL")

4. 在“配置 Tinfoil Security 的单一登录”页上，若要下载证书，请单击“下载证书”，然后在本地将证书文件保存为 **c:\\Tinfoil Security.cer**。
   
    ![配置单一登录](./media/active-directory-saas-tinfoil-security-tutorial/IC798970.png "配置单一登录")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录 Tinfoil Security 公司站点。

6. 在顶部菜单栏中，单击“我的帐户”。
   
    ![仪表板](./media/active-directory-saas-tinfoil-security-tutorial/IC798971.png "Dashboard")

7. 单击“安全”。
   
    ![安全](./media/active-directory-saas-tinfoil-security-tutorial/IC798972.png "安全")

8. 在“单一登录”配置页上，执行以下步骤：
   
    ![单一登录](./media/active-directory-saas-tinfoil-security-tutorial/IC798973.png "单一登录")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“启用 SAML”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“手动配置”。
   
    c. 在 Azure 经典门户的“配置 Tinfoil Security 的单一登录”对话框页上，复制“SAML SSO URL”值，然后将其粘贴到“SAML Post URL”文本框中。
   
    d.单击“下一步”。 从导出的证书中复制“指纹”值，然后将其粘贴到“SAML 证书指纹”文本框中。  
      
    > [!TIP]
    > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)
    > 
    > 
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 复制**帐户 ID**。
   
    f. 单击“保存” 。

9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-tinfoil-security-tutorial/IC798974.png "配置单一登录")

10. 在顶部菜单中，单击“属性”，打开“SAML 令牌属性”对话框。
    
    ![属性](./media/active-directory-saas-tinfoil-security-tutorial/IC795920.png "属性")

11. 若要添加所需的属性映射，请执行以下步骤：
    
    ![属性](./media/active-directory-saas-tinfoil-security-tutorial/IC798975.png "属性")
    
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“添加用户属性”。

    b.保留“数据库类型”设置，即设置为“共享”。 在“属性名称”文本框中，键入“accountid”。

    c. 在“属性值”文本框中，粘贴在前一部分中复制的帐户 ID 值。

    d.单击“下一步”。 单击“完成”。

12. 单击“应用更改”。

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 Tinfoil Security，必须将这些用户预配到 Tinfoil Security 中。  
对于 Tinfoil Security，预配是一项手动任务。

### <a name="to-get-a-user-provisioned-perform-the-following-steps"></a>若要预配用户，请执行以下步骤：
1. 如果用户是企业帐户的一部分，则需要联系 Tinfoil Security 支持团队，以便创建用户帐户。
2. 如果用户是常规 Tinfoil Security SaaS 用户，则该用户可以向任何用户站点添加协作者。 这会触发向指定的电子邮件发送邀请以创建新的 Tinfoil Security 用户帐户的过程。

> [!NOTE]
> 可以使用任何其他 Tinfoil Security 用户帐户创建工具或 Tinfoil Security 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-tinfoil-security-perform-the-following-steps"></a>若要将用户分配到 Tinfoil Security，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Tinfoil Security”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-tinfoil-security-tutorial/IC798976.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-tinfoil-security-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。


