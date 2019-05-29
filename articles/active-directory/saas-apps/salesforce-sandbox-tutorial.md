---
title: 教程：Azure Active Directory 与 Salesforce 沙盒集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Salesforce 沙盒之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.openlocfilehash: b2940f3eee3112fe1c6d57cc92157c573ecad109
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2019
ms.locfileid: "65904283"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce-sandbox"></a>教程：Azure Active Directory 与 Salesforce 沙盒集成

本教程介绍如何将 Salesforce 沙盒与 Azure Active Directory (Azure AD) 集成。

使用沙盒可以在不同的环境中针对多种用途（如开发、测试和培训）创建组织的多个副本，而不会影响 Salesforce 产品组织中的的数据和应用程序。
有关更多详细信息，请参阅[沙盒概述](https://help.salesforce.com/articleView?id=create_test_instance.htm&language=en_us&type=5)。

将 Salesforce 沙盒与 Azure AD 集成具有以下优势：

* 可在 Azure AD 中控制谁有权访问 Salesforce 沙盒。
* 可让用户使用其 Azure AD 帐户自动登录到 Salesforce 沙盒（单一登录）。
* 可在中心位置（即 Azure 门户）管理帐户。

如果要了解有关 SaaS 应用与 Azure AD 集成的更多详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。
如果还没有 Azure 订阅，可以在开始前[创建一个免费帐户](https://azure.microsoft.com/free/)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 Salesforce 沙盒的集成，需要以下项：

* 一个 Azure AD 订阅。 如果没有 Azure AD 环境，可以获取一个[免费帐户](https://azure.microsoft.com/free/)
* 已启用单一登录的 Salesforce 沙盒订阅

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Salesforce 沙盒支持 **SP 和 IDP** 发起的 SSO
* Salesforce 沙盒支持**实时**用户预配
* Salesforce 沙盒支持[**自动**用户预配](salesforce-sandbox-provisioning-tutorial.md)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>从库中添加 Salesforce 沙盒

若要配置 Salesforce 沙盒与 Azure AD 的集成，需要从库中将 Salesforce 沙盒添加到托管的 SaaS 应用列表。

**若要从库中添加 Salesforce 沙盒，请执行以下步骤：**

1. 在 **[Azure 门户](https://portal.azure.com)** 的左侧导航面板中，单击“Azure Active Directory”  图标。

    ![“Azure Active Directory”按钮](common/select-azuread.png)

2. 转到“企业应用”，并选择“所有应用”选项   。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

3. 若要添加新应用程序，请单击对话框顶部的“新建应用程序”  按钮。

    ![“新增应用程序”按钮](common/add-new-app.png)

4. 在搜索框中键入 Salesforce Sandbox  ，在结果面板中选择“Salesforce Sandbox”，并单击“添加”按钮来添加该应用程序   。

    ![结果列表中的 Salesforce 沙盒](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

在本部分中，将基于名为 **Britta Simon** 的测试用户配置和测试 Salesforce 沙盒的 Azure AD 单一登录。

为使单一登录能正常工作，Azure AD 需要知道与 Azure AD 用户相对应的 Salesforce 沙盒用户。 换句话说，需要建立 Azure AD 用户与 Salesforce 沙盒中相关用户之间的关联。

若要配置和测试 Salesforce 沙盒的 Azure AD 单一登录，需要完成以下构建基块：

1. **[配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)** - 使用户能够使用此功能。
2. **[配置 Salesforce 沙盒单一登录](#configure-salesforce-sandbox-single-sign-on)** - 在应用程序端配置单一登录设置。
3. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
4. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
5. **[创建 Salesforce 沙盒测试用户](#create-salesforce-sandbox-test-user)** - 在 Salesforce 沙盒中创建 Britta Simon 的对应用户，该用户与 Azure AD 中表示 Britta Simon 的用户相关联。
6. **[测试单一登录](#test-single-sign-on)** - 验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录。

若要配置 Salesforce 沙盒的 Azure AD 单一登录，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)的“Salesforce 沙盒”应用程序集成页上，选择“单一登录”   。

    ![配置单一登录链接](common/select-sso.png)

2. 在**选择单一登录方法**对话框中，选择 **SAML/WS-Fed**模式以启用单一登录。

    ![单一登录选择模式](common/select-saml-option.png)

3. 在“使用 SAML 设置单一登录”页上，单击“编辑”图标以打开“基本 SAML 配置”对话框    。

    ![编辑基本 SAML 配置](common/edit-urls.png)

4. 如果已获取“服务提供商元数据文件”并想要在“IDP”发起的模式下进行配置，请在“基本 SAML 配置”部分执行以下步骤    ：

    a. 单击“上传元数据文件”  。

    ![上传元数据文件](common/upload-metadata.png)

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    ![选择元数据文件](common/browse-upload-metadata.png)

    > [!NOTE]
    > 将从 Salesforce 沙盒管理门户获取服务提供商元数据文件，本教程稍后会对此进行说明。

    c. 成功上传元数据文件后，“回复 URL”文本框会自动填入“回复 URL”值   。

    ![image](common/both-replyurl.png)

    > [!Note]
    > 如果“回复 URL”值未自动填充，请根据要求手动填充该值  。

5. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分，单击“下载”  以根据要求下载从给定选项提供的元数据 XML 并将其保存在计算机上。 

    ![证书下载链接](common/metadataxml.png)

6. 在“设置 Salesforce 沙盒”部分中，根据要求复制相应 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

    a. 登录 URL

    b. Azure AD 标识符

    c. 注销 URL

### <a name="configure-salesforce-sandbox-single-sign-on"></a>配置 Salesforce 沙盒单一登录

1. 在浏览器中打开新选项卡并登录到 Salesforce 沙盒管理员帐户。

2. 单击页面右上角设置图标  下的“安装”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure1.png)

3. 向下滚动到左侧导航窗格中的“设置”  ，单击“标识”  ，以展开相关部分。 然后单击“单一登录设置”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. 在“单一登录设置”  页上，单击“编辑”  按钮。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure3.png)

5. 选择“已启用 SAML”  ，并单击“保存”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. 若要配置 SAML 单一登录设置，请单击“从元数据文件新建”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. 单击“选择文件”  以上传从 Azure 门户下载的元数据 XML 文件，然后单击“创建”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. 在“SAML 单一登录设置”  页上，字段将自动填充，请单击“保存”。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. 在“单一登录设置”页上，单击“下载元数据”按钮以下载服务提供商元数据文件   。 在 Azure 门户的“基本 SAML 配置”部分中使用此文件，以便如上所述配置必要的 URL  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure4.png)

10. 如果要在 **SP** 发起的模式下配置应用程序，请满足以下先决条件：

    a. 应有一个已验证的域。

    b. 需在 Salesforce 沙盒中配置并启用域，本教程稍后会介绍相关步骤。

    c. 在 Azure 门户的“基本 SAML 配置”部分中，单击“设置其他 URL”并执行以下步骤   ：
  
    ![Salesforce 沙盒域和 URL 单一登录信息](common/both-signonurl.png)

    在“登录 URL”  文本框中，使用以下模式键入值：`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > 启用域后，应从 Salesforce 沙盒门户复制此值。

11. 在“SAML 签名证书”部分中，单击“联合元数据 XML”，然后将 xml 文件保存在计算机上   。

    ![证书下载链接](common/metadataxml.png)

12. 在浏览器中打开新选项卡并登录到 Salesforce 沙盒管理员帐户。

13. 单击页面右上角设置图标  下的“安装”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure1.png)

14. 向下滚动到左侧导航窗格中的“设置”  ，单击“标识”  ，以展开相关部分。 然后单击“单一登录设置”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. 在“单一登录设置”  页上，单击“编辑”  按钮。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure3.png)

16. 选择“已启用 SAML”  ，并单击“保存”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. 若要配置 SAML 单一登录设置，请单击“从元数据文件新建”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. 单击“选择文件”以上传元数据 XML 文件，然后单击“创建”   。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. 在“SAML 单一登录设置”页面上，各字段会自动填充数据，在“名称”文本框中键入配置的名称（例如：*SPSSOWAAD_Test*）并单击“保存”   。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. 若要在 Salesforce 沙盒中启用域，请执行以下步骤：

    > [!NOTE]
    > 在启用域之前，需在 Salesforce 沙盒中创建相同的域。 有关详细信息，请参阅[定义域名](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)。 创建域后，请确保配置正确。

21. 在 Salesforce 沙盒的左侧导航窗格中，单击“公司设置”  展开相关部分，然后单击“我的域”  。

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. 在“身份验证配置”部分，单击“编辑”。  

    ![配置单一登录](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. 在“身份验证配置”部分，对于“身份验证服务”，请选择在 Salesforce 沙盒中配置 SSO 期间设置的 SAML 单一登录设置的名称，然后单击“保存”。   

    ![配置单一登录](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”  、“用户”  和“所有用户”  。

    ![“用户和组”以及“所有用户”链接](common/users.png)

2. 选择屏幕顶部的“新建用户”  。

    ![“新建用户”按钮](common/new-user.png)

3. 在“用户属性”中，按照以下步骤操作。

    ![“用户”对话框](common/user-properties.png)

    a. 在“名称”  字段中，输入 BrittaSimon  。
  
    b. 在“用户名”字段中键入 `brittasimon@yourcompanydomain.extension`。  例如，BrittaSimon@contoso.com。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值  。

    d. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 Salesforce 沙盒的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”和“Salesforce 沙盒”    。

    ![“企业应用程序”边栏选项卡](common/enterprise-applications.png)

2. 在应用程序列表中，选择“Salesforce 沙盒”  。

    ![应用程序列表中的 Salesforce 沙盒链接](common/all-applications.png)

3. 在左侧菜单中，选择“用户和组”  。

    ![“用户和组”链接](common/users-groups-blade.png)

4. 单击“添加用户”  按钮，然后在“添加分配”  对话框中选择“用户和组”  。

    ![“添加分配”窗格](common/add-assign-user.png)

5. 在“用户和组”  对话框中，选择“用户”列表中的 Britta Simon  ，然后单击屏幕底部的“选择”  按钮。

6. 如果你在 SAML 断言中需要任何角色值，请在“选择角色”  对话框中从列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。 

7. 在“添加分配”对话框中，单击“分配”按钮。  

### <a name="create-salesforce-sandbox-test-user"></a>创建 Salesforce 沙盒测试用户

在本部分中，将在 Salesforce 沙盒中创建名为 Britta Simon 的用户。 Salesforce 沙盒支持在默认情况下保持启用的实时预配。 此部分不存在任何操作项。 尝试访问 Salesforce 沙盒时，如果 Salesforce 沙盒中没有用户，系统会创建一个新用户。 Salesforce 沙盒还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](salesforce-sandbox-provisioning-tutorial.md)。

### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Salesforce 沙盒”磁贴时，应会自动登录到设置了 SSO 的 Salesforce 沙盒。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

* [配置用户预配](salesforce-sandbox-provisioning-tutorial.md)
