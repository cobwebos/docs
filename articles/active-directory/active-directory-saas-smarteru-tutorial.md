---
title: "教程：Azure Active Directory 与 SmarterU 集成 | Microsoft 文档"
description: "了解如何使用 SmarterU 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 95fe3212-d052-4ac8-87eb-ac5305227e85
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 451369e21e7471180b6cd8c77d62b157d0bcddff
ms.openlocfilehash: 058f84f57c18735d42b95a74aee74468ccef0f8d


---
# <a name="tutorial-azure-active-directory-integration-with-smarteru"></a>教程：Azure Active Directory 与 SmarterU 集成
本教程的目的是说明 Azure 与 SmarterU 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* SmarterU 租户

完成本教程后，已向 SmarterU 分配的 Azure AD 用户将能够在 SmarterU 公司站点（服务提供商发起的登录）或使用[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录到应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 SmarterU 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-smarteru-tutorial/IC777320.png "方案")

## <a name="enabling-the-application-integration-for-smarteru"></a>为 SmarterU 启用应用程序集成
本部分的目的是概述如何为 SmarterU 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-smarteru-perform-the-following-steps"></a>若要为 SmarterU 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-smarteru-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-smarteru-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-smarteru-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-smarteru-tutorial/IC749322.png "从库添加应用程序")

6. 在搜索框中，键入“SmarterU”。
   
    ![应用程序库](./media/active-directory-saas-smarteru-tutorial/IC777321.png "应用程序库")

7. 在结果窗格中，选择“SmarterU”，然后单击“完成”以添加该应用程序。
   
    ![SmarterU](./media/active-directory-saas-smarteru-tutorial/IC777322.png "SmarterU")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 SmarterU 进行身份验证。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“SmarterU”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-smarteru-tutorial/IC777323.png "配置单一登录")

2. 在“你希望用户如何登录 SmarterU”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-smarteru-tutorial/IC777324.png "配置单一登录")

3. 在“配置 SmarterU 的单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将数据文件本地保存为 **c:\\SmarterUMetaData.cer**。
   
    ![配置单一登录](./media/active-directory-saas-smarteru-tutorial/IC777325.png "配置单一登录")

4. 在另一个 Web 浏览器窗口中，以管理员身份登录 SmarterU 公司站点。

5. 在顶部工具栏中，单击“帐户设置”。
   
    ![帐户设置](./media/active-directory-saas-smarteru-tutorial/IC777326.png "帐户设置")

6. 在帐户配置页面上，执行以下步骤：
   
    ![外部授权](./media/active-directory-saas-smarteru-tutorial/IC777327.png "外部授权")
 
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 选择“启用外部授权”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 在“主登录控制”部分中，选择“SmarterU”选项卡。
   
    c. 在“用户默认登录”部分中，选择“SmarterU”选项卡。
   
    d.单击“下一步”。 选择“启用 Okta”。
   
    e.在“新建 MySQL 数据库”边栏选项卡中，接受法律条款，然后单击“确定”。 复制下载的元数据文件的内容，然后将其粘贴到“Okta 元数据 ”文本框中。

    f. 单击“保存” 。

7. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-smarteru-tutorial/IC777328.png "配置单一登录")

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 Azure AD 用户能够登录 SmarterU，必须将这些用户预配到 SmarterU 中。  
对于 SmarterU，预配是一项手动任务。

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>若要预配用户帐户，请执行以下步骤：
1. 登录到 **SmarterU** 租户。

2. 转到“用户”。

3. 在“用户”部分中，执行以下步骤：
   
    ![新建用户](./media/active-directory-saas-smarteru-tutorial/IC777329.png "New User")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 单击“+用户”。
   
    b. 在以下文本框中键入 Azure AD 用户帐户的相关属性值：**主电子邮件**、**员工 ID**、**密码**、**验证密码**、**名字**、**姓氏**。
   
    c. 单击“活动”。
   
    d.单击“下一步”。 单击“保存” 。

> [!NOTE]
> 可以使用任何其他 SmarterU 用户帐户创建工具或 SmarterU 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向希望其使用应用程序的 Azure AD 用户授予该配置的访问权限。

### <a name="to-assign-users-to-smarteru-perform-the-following-steps"></a>若要将用户分配到 SmarterU，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“SmarterU”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-smarteru-tutorial/IC777330.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-smarteru-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Dec16_HO2-->


