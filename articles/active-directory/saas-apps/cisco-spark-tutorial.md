---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Cisco Webex 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Cisco Webex 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f6ab2d5811060b7dc36323a80fed6961b8cf5a9
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76290676"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Cisco Webex 集成

本教程介绍如何将 Cisco Webex 与 Azure Active Directory (Azure AD) 集成。 将 Cisco Webex 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Cisco Webex。
* 让用户使用其 Azure AD 帐户自动登录到 Cisco Webex。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 Cisco Webex 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Cisco Webex 支持 SP 发起的 SSO  。
* Cisco Webex 支持**自动**用户预配。
* 配置 Cisco Webex 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>从库中添加 Cisco Webex

若要配置 Cisco Webex 与 Azure AD 的集成，需要从库中将 Cisco Webex 添加到托管 SaaS 应用的列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Cisco Webex”   。
1. 从结果面板中选择“Cisco Webex”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>配置和测试 Cisco Webex 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Cisco Webex 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 Cisco Webex 相关用户之间建立链接关系。

若要配置和测试 Cisco Webex 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** ，使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** ，以使用 B.Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** ，使 B.Simon 能够使用 Azure AD 单一登录。
2. **[配置 Cisco Webex](#configure-cisco-webex)** ，以在应用程序端配置 SSO 设置。
    1. **[创建 Cisco Webex 测试用户](#create-cisco-webex-test-user)** ，以在 Cisco Webex 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
3. **[测试 SSO](#test-sso)** ，验证配置是否正常工作。

### <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Cisco Webex”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，通过执行以下步骤上传已下载的**服务提供程序元数据**文件并配置应用程序：

    >[!Note]
    >你将从“配置 Cisco Webex”  部分获取服务提供商元数据文件，本教程稍后将对此进行说明。 

    a. 单击“上传元数据文件”  。

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    c. 成功完成服务提供程序元数据文件的上传后，**标识符**和**回复 URL** 值将自动填充在“基本 SAML 配置”部分： 

    在“登录 URL”文本框中，粘贴上传 SP 元数据文件后自动填充的“回复 URL”值。  

1. Cisco Webex 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除上述属性以外，Cisco Webex 应用程序还要求在 SAML 响应中传回其他几个属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。
  
    | 名称 |  源属性|
    | ---------------|--------- |
    | uid | user.userprincipalname |

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

   ![证书下载链接](common/metadataxml.png)

1. 在“设置 Cisco Webex”  部分中，根据要求复制相应的 URL。

   ![复制配置 URL](common/copy-configuration-urls.png)

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

在本部分中，通过授予 B. Simon 访问 Cisco Webex 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“Cisco Webex”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="configure-cisco-webex"></a>配置 Cisco Webex

1. 若要在 Cisco Webex 中自动执行配置，需要通过单击“安装扩展”，安装“我的应用安全登录浏览器扩展”   。

    ![我的应用扩展](common/install-myappssecure-extension.png)

2. 将扩展添加到浏览器后，单击“设置 Cisco Webex”，此时会将你定向到 Cisco Webex 应用程序  。 在此处提供管理员凭据以登录到 Cisco Webex。 浏览器扩展会自动配置应用程序，并自动执行步骤 3-8。

    ![设置配置](common/setup-sso.png)

3. 如果要手动设置 Cisco Webex，请使用完整管理员凭据登录到 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。

4. 选择“设置”，在“身份验证”部分下单击“修改”。   

    ![配置单一登录](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. 选择“集成第三方标识提供者。(高级)”  ，然后转到下一个屏幕。

6. 在“导入 Idp 元数据”页上，将 Azure AD 元数据文件拖放到页面上，或者使用文件浏览器选项找到并上传 Azure AD 元数据文件。  然后，选择“元数据中需要由证书颁发机构签名的证书(更安全)”并单击“下一步”。  

    ![配置单一登录](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. 选择“测试 SSO 连接”，当新的浏览器标签页打开时，通过登录使用 Azure AD 进行身份验证。 

8. 返回到 **Cisco Cloud Collaboration Management** 浏览器标签页。如果测试成功，则选择“此测试成功。  启用单一登录”，并单击“下一步”。 

### <a name="create-cisco-webex-test-user"></a>创建 Cisco Webex 测试用户

在本部分中，将在 Cisco Webex 中创建一个名为 B.Simon 的用户。 在本部分中，将在 Cisco Webex 中创建一个名为 B.Simon 的用户。

1. 使用完整管理员凭据转到 [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/)。

2. 单击“用户”，然后单击“管理用户”   。
   
    ![配置单一登录](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. 在“管理用户”窗口中，选择“手动添加或修改用户”，并单击“下一步”。   

4. 选择“姓名和电子邮件地址”。  然后，如下所述填写文本框：

    ![配置单一登录](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. 在“名字”  文本框中，键入用户的名字，例如 **B**。

    b. 在“姓氏”  文本框中，键入用户的姓氏，例如 **Simon**。

    c. 在“电子邮件地址”文本框中，键入用户的电子邮件地址，例如 b.simon@contoso.com。 

5. 单击加号以添加 B.Simon。 然后单击“下一步”。 

6. 在“为用户添加服务”窗口中，单击“保存”，并单击“完成”。   

## <a name="test-sso"></a>测试 SSO

选择访问面板中的 Cisco Webex 磁贴时，应当会自动登录到设置了 SSO 的 Cisco Webex。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Cisco Webex](https://aad.portal.azure.com)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/protect-webex)

- [如何通过高级可见性和控制保护 Cisco Webex](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
