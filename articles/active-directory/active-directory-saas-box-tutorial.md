---
title: "教程：Azure Active Directory 与 Box 集成 | Microsoft Docs"
description: "了解如何使用 Box 与 Azure Active Directory 启用单一登录、自动化预配和其他功能！"
services: active-directory
author: jeevansd
documentationcenter: na
manager: femila
ms.assetid: 5f3517f8-30f2-4be7-9e47-43d702701797
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c8254f41006c36509af3626cb14825602e6a4adb


---
# <a name="tutorial-azure-active-directory-integration-with-box"></a>教程：Azure Active Directory 与 Box 集成
本教程的目的是说明 Azure 与 Box 的集成。  
在本教程中概述的方案假定您已具有以下各项：

* 一个有效的 Azure 订阅
* Box 中的一个测试租户

完成本教程后，分配到 Box 的 Azure AD 用户将能够在 Box 公司站点（服务提供商启动的登录）或通过阅读[访问面板简介](active-directory-saas-access-panel-introduction.md)单一登录应用程序。

在本教程中概述的方案由以下构建基块组成：

1. 为 Box 启用应用程序集成
2. 配置单一登录
3. 配置用户和组的预配
4. 分配用户

![方案](./media/active-directory-saas-box-tutorial/IC769537.png "Scenario")

## <a name="enabling-the-application-integration-for-box"></a>为 Box 启用应用程序集成
本部分旨在概述如何为 Box 启用应用程序集成。

### <a name="to-enable-the-application-integration-for-box-perform-the-following-steps"></a>若要支持 Box 的应用程序集成，请执行以下步骤：
1. 在 Azure 经典门户的左侧导航窗格中，单击“Active Directory”。
   
   ![Active Directory](./media/active-directory-saas-box-tutorial/IC700993.png "Active Directory")
2. 从“目录”列表中，选择要为其启用目录集成的目录。
3. 若要打开应用程序视图，请在目录视图的顶部菜单中，单击“应用程序”。
   
   ![应用程序](./media/active-directory-saas-box-tutorial/IC700994.png "Applications")
4. 在页面底部单击“添加”。
   
   ![添加应用程序](./media/active-directory-saas-box-tutorial/IC749321.png "Add application")
5. 在“要执行什么操作”对话框中，单击“从库中添加应用程序”。
   
   ![从库中添加一个应用程序](./media/active-directory-saas-box-tutorial/IC749322.png "Add an application from gallerry")
6. 在搜索框中，键入“Box”。
   
   ![应用程序库](./media/active-directory-saas-box-tutorial/IC701023.png "Application gallery")
7. 在“结果”窗格中，选择“Box”，然后单击“完成”，添加该应用程序。
   
   ![Box](./media/active-directory-saas-box-tutorial/IC701024.png "Box")

## <a name="configuring-single-sign-on"></a>配置单一登录
本部分的目的是概述如何让用户能够使用基于 SAML 协议的联合身份验证通过他们在 Azure AD 中的帐户向 Box 证明自己的身份。 在此过程中，需要将元数据上传到 Box.com。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Box”应用程序集成页上，单击“配置单一登录”，打开“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-box-tutorial/IC769538.png "Configure single sign-on")
2. 在“你希望用户如何登录 Box”页上，选择“Microsoft Azure AD 单一登录”，然后单击“下一步”。
   
   ![配置单一登录](./media/active-directory-saas-box-tutorial/IC769539.png "Configure single sign-on")
3. 在“配置应用 URL”页上的“Box 租户 URL”文本框中，键入 Box 租户 URL（例如：https://<mydomainname>.box.com），然后单击“下一步”。
   
   ![配置应用 URL](./media/active-directory-saas-box-tutorial/IC669826.png "Configure app URL")
4. 在“配置 Box 的单一登录”页上，若要下载元数据，请单击“下载元数据”，然后将元数据文件本地保存在计算机上。
   
   ![配置单一登录](./media/active-directory-saas-box-tutorial/IC669824.png "Configure single sign-on")
5. 将该元数据文件转发给 Box 支持团队。 支持团队需要为你配置单一登录。
6. 选择单一登录配置确认，然后单击“完成”，关闭“配置单一登录”对话框。
   
   ![配置单一登录](./media/active-directory-saas-box-tutorial/IC769540.png "Configure single sign-on")
   
   ## <a name="configuring-user-provisioning"></a>配置用户设置

本部分的目的是概述如何对 Box 启用 Active Directory 用户帐户的预配。

### <a name="to-configure-single-sign-on-perform-the-following-steps"></a>若要配置单一登录，请执行以下步骤：
1. 在 Azure 经典门户中的“Box”应用程序集成页上，单击“配置用户预配”，打开“配置用户预配”对话框。 
   
    ![启用自动预配](./media/active-directory-saas-box-tutorial/IC769541.png "Enable automatic user provisioning")
2. 在“启用对 Box 的用户预配”对话框页上，单击“启用用户预配”。 
   
    ![启用自动预配](./media/active-directory-saas-box-tutorial/IC769544.png "Enable automatic user provisioning")
3. 在“登录授予对 Box 的访问权限”页上，提供所需的凭据，然后单击“授权”。 
   
    ![启用自动预配](./media/active-directory-saas-box-tutorial/IC769546.png "Enable automatic user provisioning")
4. 单击“授予对 Box 的访问权限”对此操作授权，并返回到 Azure 经典门户。 
   
    ![启用自动预配](./media/active-directory-saas-box-tutorial/IC769549.png "Enable automatic user provisioning")
5. 在“预配选项”页上，可以使用“要预配的对象类型”复选框选择是否在用户对象的基础上为 Box 预配组对象。  有关详细信息，请参阅下面的“分配用户和组”部分。
6. 若要完成配置，请单击“完成”按钮。 
   
    ![启用自动预配](./media/active-directory-saas-box-tutorial/IC769551.png "Enable automatic user provisioning")

## <a name="assigning-a-test-user"></a>分配测试用户
若要测试配置，需要向希望能够通过应用程序访问配置的 Azure AD 用户分配访问权限，从而向他们授予该权限。

### <a name="to-assign-users-to-box-perform-the-following-steps"></a>若要将用户分配到 Box，请执行以下步骤：
1. 在 Azure 经典门户中，创建测试帐户。
2. 在“Box”应用程序集成页上，单击“分配用户”。 
   
    ![分配用户](./media/active-directory-saas-box-tutorial/IC769552.png "Assign users")
3. 选择测试用户，单击“分配”，然后单击“是”确认分配。 
   
   ![是](./media/active-directory-saas-box-tutorial/IC767830.png "Yes")

现在应等待 10 分钟，然后验证该帐户是否已同步到 Box。

作为第一个验证步骤，可以通过在 Azure 经典门户中的 Box 应用程序集成页上单击“仪表板”检查预配状态。

![仪表板](./media/active-directory-saas-box-tutorial/IC769553.png "Dashboard")

成功完成的用户预配周期将由一个相关状态予以指示：

![集成状态](./media/active-directory-saas-box-tutorial/IC769555.png "Integration status")

在 Box 租户中，同步的用户列在“管理控制台”的“托管用户”下。

![集成状态](./media/active-directory-saas-box-tutorial/IC769556.png "Integration status")

## <a name="assigning-users-and-groups"></a>分配用户和组
可以使用 Azure 经典门户中的“Box > 用户和组”选项卡指定应向哪些用户和组授予对 Box 的访问权限。 分配用户或组会导致以下结果：

* Azure AD 允许分配的用户（不管是直接分配还是以组成员的方式进行分配）向 Box 进行身份验证。 如果用户尚未分配，则 Azure AD 不会允许其登录到 Box，并会在 Azure AD 登录页上返回一个错误。
* Box 的应用磁贴会添加到用户的[应用程序启动程序](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)中。
* 如果启用了自动预配，则会将分配的用户和/或组添加到预配队列进行自动预配。
  
  * 如果仅将用户对象配置为进行预配，则会将所有直接分配的用户置于预配队列中，所有属于已分配组成员的用户也会置于预配队列中。 
  * 如果已将组对象配置为进行预配，则会为 Box 预配所有已分配的组以及属于这些组的成员的所有用户。 写入 到 Box 以后，组和用户成员身份会保留。

可在进行基于 SAML 的身份验证时使用“属性 > 单一登录”选项卡配置需呈现给 Box 的用户属性（或声明），并可在预配操作过程中使用“属性 > 预配”选项卡配置用户和组属性如何从 Azure AD 流向 Box。 有关详细信息，请参阅下面的资源。

## <a name="additional-resources"></a>其他资源
* [自定义在 SAML 令牌中颁发的声明](active-directory-saml-claims-customization.md)
* [预配：自定义属性映射](active-directory-saas-customizing-attribute-mappings.md)
* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](active-directory-saas-tutorial-list.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](active-directory-appssoaccess-whatis.md)




<!--HONumber=Nov16_HO3-->


