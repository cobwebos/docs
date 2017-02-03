---
title: "教程：Azure Active Directory 与 Thoughtworks Mingle 集成 | Microsoft 文档"
description: "了解如何使用 Thoughtworks Mingle 与 Azure Active Directory 来启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 69d859d9-b7f7-4c42-bc8c-8036138be586
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/06/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 49352a5e8255468bbc54c02e0cd9242d49002dbd
ms.openlocfilehash: 6a9a1cf616531de1d0b8194bc0e6c186acc27008


---
# <a name="tutorial-azure-active-directory-integration-with-thoughtworks-mingle"></a>教程：Azure Active Directory 与 Thoughtworks Mingle 集成
本教程的目的是说明 Azure 与 Thoughtworks Mingle 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Thoughtworks Mingle 租户

在本教程中概述的方案由以下构建基块组成：

1. 为 Thoughtworks Mingle 启用应用程序集成
2. 配置单一登录
3. 配置用户设置
4. 分配用户

![方案](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "方案")

## <a name="enabling-the-application-integration-for-thoughtworks-mingle"></a>为 Thoughtworks Mingle 启用应用程序集成
本部分的目的是概述如何为 Thoughtworks Mingle 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-thoughtworks-mingle-perform-the-following-steps"></a>若要为 Thoughtworks Mingle 启用应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2. 在“目录”列表中，选择要启用目录集成的目录。

3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
    ![应用程序](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "应用程序")

4. 在页面底部单击“添加”。
   
    ![添加应用程序](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "添加应用程序")

5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
    ![从库添加应用程序](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "从库添加应用程序")

6. 在搜索框中，键入“thoughtworks mingle”。
   
    ![应用程序库](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "应用程序库")

7. 在结果窗格中，选择“Thoughtworks Mingle”，然后单击“完成”以添加该应用程序。
   
    ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Thoughtworks Mingle 进行身份验证。  
在此过程中，需要将证书上载到 Thoughtworks Mingle。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Thoughtworks Mingle”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "配置单一登录")

2. 在“你希望用户如何登录 Thoughtworks Mingle”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
    ![配置单一登录](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "配置单一登录")

3. 在“配置应用 URL”页上的“Thoughtworks Mingle 租户 URL”文本框中，使用“*http://company.mingle.thoughtworks.com*”模式键入 URL，然后单击“下一步”。
   
    ![配置应用 URL](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "配置应用 URL")

4. 在“配置 Thoughtworks Mingle 的单一登录”页上，单击“下载元数据”，然后将其保存在计算机上。
   
    ![配置单一登录](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "配置单一登录")

5. 以管理员身份登录 **Thoughtworks Mingle** 公司站点。

6. 单击“管理员”选项卡，然后单击“SSO 配置”。
   
    ![SSO 配置](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO Config")

7. 在“SSO 配置”部分中，执行以下步骤：
   
    ![SSO 配置](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO Config")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 若要上载元数据文件，请单击“选择文件”。
   
    b.保留“数据库类型”设置，即设置为“共享”。 单击“保存更改”。

8. 在 Azure 经典门户中，选择“单一登录配置确认”，然后单击“完成”，关闭“配置单一登录”对话框。
   
    ![配置单一登录](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "配置单一登录")

## <a name="configuring-user-provisioning"></a>配置用户设置
要使 AAD 用户能够登录，必须使用其 Azure Active Directory 用户名将其预配到 Thoughtworks Mingle 应用程序中。  
对于 Thoughtworks Mingle，预配是一项手动任务。

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>若要配置用户设置，请执行以下步骤：
1. 以管理员身份登录 Thoughtworks Mingle 公司站点。

2. 单击“配置文件”。
   
    ![第一个项目](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Your First Project")

3. 单击“管理员”选项卡，然后单击“用户”。
   
    ![用户](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "用户")

4. 单击“新建用户”。
   
    ![新建用户](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "New User")

5. 在“新建用户”对话框页上，执行以下步骤：
   
    ![新建用户](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "New User")
   
    a.在“解决方案资源管理器”中，右键单击项目文件夹下的“引用”文件夹，然后单击“添加引用”。 将要预配的有效 AAD 帐户的**登录名**、**显示名称**、**选择密码**、**确认密码**键入到相关文本框中。
   
    b.保留“数据库类型”设置，即设置为“共享”。 对于“用户类型”，请选择“全用户”。
   
    c. 单击“创建此配置文件”。

> [!NOTE]
> 可以使用任何其他 Thoughtworks Mingle 用户帐户创建工具或 Thoughtworks Mingle 提供的 API 来预配 AAD 用户帐户。
> 
> 

## <a name="assigning-users"></a>分配用户
若要测试配置，需要通过分配权限的方式向要允许其使用应用程序的 Azure AD 用户授予该应用程序的访问权限。

### <a name="to-assign-users-to-thoughtworks-mingle-perform-the-following-steps"></a>若要将用户分配到 Thoughtworks Mingle，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。

2. 在“Thoughtworks Mingle”应用程序集成页上，单击“分配用户”。
   
    ![分配用户](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "分配用户")

3. 选择测试用户，单击“分配”，然后单击“是”确认分配。
   
    ![是](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "是")

如果要测试单一登录设置，请打开访问面板。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](active-directory-saas-access-panel-introduction.md)（访问面板简介）。




<!--HONumber=Dec16_HO2-->


