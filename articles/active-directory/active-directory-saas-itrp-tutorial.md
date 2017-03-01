---
title: "教程：Azure Active Directory 与 ITRP 集成 | Microsoft Docs"
description: "了解如何使用 ITRP 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: e09716a3-4200-4853-9414-2390e6c10d98
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/05/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: c4983024e5f5902620c0be7b422bc3d1eaf4a36f
ms.openlocfilehash: 8142454e1ee8bf439e9186ac94a3feb05b10ae69


---
# <a name="tutorial-azure-active-directory-integration-with-itrp"></a>教程：Azure Active Directory 与 ITRP 集成
本教程的目的是说明 Azure 与 ITRP 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* ITRP 租户

完成本教程后，已向 ITRP 分配的 Azure AD 用户将能够在 ITRP 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 ITRP 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-itrp-tutorial/IC775551.png "Scenario")

## <a name="enabling-the-application-integration-for-itrp"></a>为 ITRP 启用应用程序集成
本部分的目的是概述如何为 ITRP 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-itrp-perform-the-following-steps"></a>若要为 ITRP 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-itrp-tutorial/IC700993.png "Active Directory")

2. 从“目录”列表中，选择要为其启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-itrp-tutorial/IC700994.png "Applications")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-itrp-tutorial/IC749321.png "Add application")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库中添加应用程序](./media/active-directory-saas-itrp-tutorial/IC749322.png "Add an application from gallerry")

6. 在搜索框中，键入“ITRP”。
   
    ![应用程序库](./media/active-directory-saas-itrp-tutorial/IC775565.png "Application Gallery")

7. 在结果窗格中，选择“ITRP”，然后单击“完成”以添加该应用程序。
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775566.png "ITRP")
   
## <a name="configuring-single-sign-on"></a>配置单一登录

本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 ITRP 进行身份验证。  
配置 ITRP 的单一登录需要检索证书的指纹值。  
如果不熟悉此过程，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“ITRP”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-itrp-tutorial/IC771709.png "Configure single sign-on")

2. 在“你希望用户如何登录 ITRP”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-itrp-tutorial/IC775567.png "Configure Single Sign-On")

3. 在“配置应用 URL”页上的“ITRP 登录 URL”文本框中，使用模式“*https://\<tenant-name\>.ITRP.com*”键入 URL，然后单击“下一步”。
   
    ![配置应用 URL](./media/active-directory-saas-itrp-tutorial/IC775568.png "Configure App URL")

4. 在“配置 ITRP 的单一登录”页上，若要下载证书，请单击“下载证书”，然后将证书文件本地保存为“c:\\ITRP.cer”。
   
    ![配置单一登录](./media/active-directory-saas-itrp-tutorial/IC775569.png "Configure Single Sign-On")

5. 在另一个 Web 浏览器窗口中，以管理员身份登录到 ITRP 公司站点。

6. 在顶部工具栏中，单击“设置”。
   
    ![ITRP](./media/active-directory-saas-itrp-tutorial/IC775570.png "ITRP")

7. 在左侧导航窗格中，选择“单一登录”。
   
    ![单一登录](./media/active-directory-saas-itrp-tutorial/IC775571.png "Single Sign-On")

8. 在“单一登录配置”部分中，执行以下步骤：
   
    ![单一登录](./media/active-directory-saas-itrp-tutorial/IC775572.png "Single Sign-On")
    
    ![单一登录](./media/active-directory-saas-itrp-tutorial/IC775573.png "Single Sign-On")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“启用”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在 Azure 经典门户的“配置 ITRP 的单一登录”对话框页上，复制“远程注销 URL”值，然后将其粘贴到“远程注销 URL”文本框中。
   
    c. 在 Azure 经典门户的“配置 ITRP 的单一登录”对话框页上，复制“SAML SSO URL”值，然后将其粘贴到“SAML SSO URL”文本框中。
   
    d.单击“下一步”。 从导出的证书中复制“指纹”值，然后将其粘贴到“证书指纹”文本框。
      
    > [!TIP]
    > 有关详细信息，请参阅[如何检索证书的指纹值](http://youtu.be/YKQF266SAxI)
    > 
    > 
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 单击“保存” 。

9. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-itrp-tutorial/IC775574.png "Configure Single Sign-On")
   
## <a name="configuring-user-provisioning"></a>配置用户设置

要使 Azure AD 用户能够登录 ITRP，必须将这些用户预配到 ITRP 中。  
对于 ITRP，预配是一项手动任务。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到 **ITRP** 租户。

2. 在顶部工具栏中，单击“记录”。
   
    ![管理员](./media/active-directory-saas-itrp-tutorial/IC775575.png "Admin")

3. 在弹出菜单中，选择“人员”。
   
    ![人员](./media/active-directory-saas-itrp-tutorial/IC775587.png "People")

4. 单击“添加新人员”(“+”)。
   
    ![管理员](./media/active-directory-saas-itrp-tutorial/IC775576.png "Admin")

5. 在“添加新人员”对话框中，执行以下步骤：
   
    ![用户](./media/active-directory-saas-itrp-tutorial/IC775577.png "User")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 键入要预配的有效 AAD 帐户的“名称”、“电子邮件”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“保存” 。

> [!NOTE]
> 可以使用 ITRP 提供的任何其他 ITRP 用户帐户创建工具或 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-itrp-perform-the-following-steps"></a>若要将用户分配到 ITRP，请执行以下步骤：
1. 在 Azure AD 门户中，创建一个测试帐户。

2. 在“ITRP”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-itrp-tutorial/IC775588.png "Assign Users")

3. 选择测试用户，单击“分配”，然后单击“是”，确认分配。
   
    ![是](./media/active-directory-saas-itrp-tutorial/IC767830.png "Yes")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Dec16_HO1-->


