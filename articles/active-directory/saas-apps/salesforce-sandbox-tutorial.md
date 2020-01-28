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
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d6d11b6b56483f954049fdc1858db31f35c14a
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289997"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>教程：Azure Active Directory 与 Salesforce Sandbox 的单一登录 (SSO) 集成

本教程介绍如何将 Salesforce Sandbox 与 Azure Active Directory (Azure AD) 集成。 将 Salesforce Sandbox 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Salesforce Sandbox。
* 让用户使用其 Azure AD 帐户自动登录到 Salesforce Sandbox。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 Salesforce Sandbox 订阅。

## <a name="scenario-description"></a>方案描述

本教程会在测试环境中配置和测试 Azure AD 单一登录。

* Salesforce 沙盒支持 **SP 和 IDP** 发起的 SSO
* Salesforce 沙盒支持**实时**用户预配
* Salesforce 沙盒支持[**自动**用户预配](salesforce-sandbox-provisioning-tutorial.md)
* 配置 Salesforce Sandbox 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>从库中添加 Salesforce 沙盒

若要配置 Salesforce 沙盒与 Azure AD 的集成，需要从库中将 Salesforce 沙盒添加到托管的 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“Salesforce Sandbox”   。
1. 从结果面板中选择“Salesforce Sandbox”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>配置并测试 Salesforce Sandbox 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 Salesforce Sandbox 的 Azure AD SSO。 若要正常使用 SSO，需要在 Azure AD 用户与 Salesforce Sandbox 中的相关用户之间建立链接关系。

若要配置并测试 Salesforce Sandbox 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    * **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
    * **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[配置 Salesforce Sandbox SSO](#configure-salesforce-sandbox-sso)** - 在应用程序端配置单一登录设置。
    * **[创建 Salesforce Sandbox 测试用户](#create-salesforce-sandbox-test-user)** - 在 Salesforce Sandbox 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **Salesforce Sandbox** 应用程序集成页面上，找到“管理”部分，选择“单一登录”   。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

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

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 Salesforce Sandbox 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Salesforce 沙盒”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-salesforce-sandbox-sso"></a>配置 Salesforce Sandbox SSO

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

### <a name="create-salesforce-sandbox-test-user"></a>创建 Salesforce 沙盒测试用户

在本部分中，将在 Salesforce 沙盒中创建名为 Britta Simon 的用户。 Salesforce 沙盒支持在默认情况下保持启用的实时预配。 此部分不存在任何操作项。 尝试访问 Salesforce 沙盒时，如果 Salesforce 沙盒中没有用户，系统会创建一个新用户。 Salesforce 沙盒还支持自动用户预配，有关如何配置自动用户预配的更多详细信息，请参见[此处](salesforce-sandbox-provisioning-tutorial.md)。

## <a name="test-sso"></a>测试 SSO 

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中单击“Salesforce 沙盒”磁贴时，应会自动登录到设置了 SSO 的 Salesforce 沙盒。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试通过 Azure AD 使用 Salesforce Sandbox](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [配置用户预配](salesforce-sandbox-provisioning-tutorial.md)

- [如何通过高级可见性和控制保护 Salesforce Sandbox](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
