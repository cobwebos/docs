---
title: 教程：将 Azure Active Directory 与 SAP Cloud Platform Identity Authentication 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP Cloud Platform Identity Authentication 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/03/2018
ms.author: jeedes
ms.openlocfilehash: 266c9523f45294899e3cddbe782cbc54846eb119
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39422301"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-platform-identity-authentication"></a>教程：将 Azure Active Directory 与 SAP Cloud Platform Identity Authentication 集成

本教程介绍如何将 SAP Cloud Platform Identity Authentication 与 Azure Active Directory (Azure AD) 集成。 SAP Cloud Platform Identity Authentication 可用作代理 IdP，从而访问使用 Azure AD 作为主 IdP 的 SAP 应用。

将 SAP Cloud Platform Identity Authentication 与 Azure AD 集成可带来以下优势：

- 可以在 Azure AD 中控制谁有权访问 SAP 应用。
- 可以支持用户使用 Azure AD 帐户自动登录 SAP 应用。
- 可在一个中心位置（即 Azure 门户）管理帐户。

若要详细了解 SaaS 应用与 Azure AD 集成，请参阅 [Azure Active Directory 的应用访问与单一登录是什么](../manage-apps/what-is-single-sign-on.md)一文。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP Cloud Platform Identity Authentication 的集成，需备齐以下各项：

- 一个 Azure AD 订阅。
- 已启用单一登录的 SAP Cloud Platform Identity Authentication 订阅。

> [!NOTE]
> 不建议使用生产环境测试本教程中的步骤。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果没有 Azure AD 试用环境，请获取[免费试用一个月的版本](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库添加 SAP Cloud Platform Identity Authentication
1. 配置和测试 Azure AD 单一登录

深入了解技术细节前，先理解后面将会遇到的概念至关重要。 通过 SAP Cloud Platform Identity Authentication 和 Active Directory 联合身份验证服务，可以使用受 SAP Cloud Platform Identity Authentication 保护的 SAP 应用和服务，跨受 Azure AD（可用作 IdP）保护的应用或服务实现 SSO。

目前，SAP Cloud Platform Identity Authentication 可用作 SAP 应用的代理标识提供者， 而 Azure Active Directory 则在此设置中充当主要标识提供者。 

下图展示了此关系：

![创建 Azure AD 测试用户](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

在此设置中，SAP Cloud Platform Identity Authentication 租户被配置为 Azure Active Directory 中受信任的应用。 

随后会在 SAP Cloud Platform Identity Authentication 管理控制台中，配置要通过这种方式进行保护的所有 SAP 应用和服务。

因此，需要在 SAP Cloud Platform Identity Authentication（而不是 Azure Active Directory）中进行授权，授予对 SAP 应用和服务的访问权限。

通过使用 Azure Active Directory 市场将 SAP Cloud Platform Identity Authentication 配置为应用，便无需配置各个声明或 SAML 断言。

>[!NOTE] 
>目前，只有 Web SSO 已通过双方测试。 应用到 API 通信或 API 间通信所需的流应该可以正常运行，但尚未经过测试。 将在后续活动中对这些流进行测试。
>

## <a name="add-sap-cloud-platform-identity-authentication-from-the-gallery"></a>从库添加 SAP Cloud Platform Identity Authentication
若要配置 Azure AD 中的 SAP Cloud Platform Identity Authentication 集成，需要将 SAP Cloud Platform Identity Authentication 从库添加到托管 SaaS 应用列表中。

**若要从库添加 SAP Cloud Platform Identity Authentication，请按照以下步骤操作：**

1. 在 [Azure 门户](https://portal.azure.com)的左侧导航面板中，选择“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 转到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用，请选择对话框顶部的“新建应用”按钮。

    ![“新增应用程序”按钮][3]

1. 在搜索框中，键入“SAP Cloud Platform Identity Authentication”。 

1. 在结果面板中，依次选择“SAP Cloud Platform Identity Authentication”和“添加”按钮。

    ![结果列表中的 SAP Cloud Platform Identity Authentication](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录

此部分介绍了如何配置和测试 Azure AD 与 SAP Cloud Platform Identity Authentication 的单一登录。 请使用名为“Britta Simon”的测试用户对此进行配置和测试。

若要让单一登录正常运行，Azure AD 需要知道 SAP Cloud Platform Identity Authentication 中对应的用户是谁。 也就是说，需要关联 Azure AD 用户与 SAP Cloud Platform Identity Authentication 中的相关用户。

在 SAP Cloud Platform Identity Authentication 中，为“Username”指定与 Azure AD 中“用户名”相同的值。 现在，已关联这两个用户。

若要配置和测试 Azure AD 与 SAP Cloud Platform Identity Authentication 的单一登录，请完成以下构建基块：

1. [配置 Azure AD 单一登录](#configure-azure-ad-single-sign-on)，使用户能够使用此功能。
1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
1. [创建 SAP Cloud Platform Identity Authentication 测试用户](#create-an-sap-cloud-platform-identity-authentication-test-user)，以便在 SAP Cloud Platform Identity Authentication 中创建 Britta Simon 的对应用户，与 Azure AD 表示的用户相关联。
1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
1. [测试单一登录](#test-single-sign-on)，验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分，需在 Azure 门户中启用 Azure AD 单一登录，并在 SAP Cloud Platform Identity Authentication 应用程序中配置单一登录。

**若要配置 Azure AD 与 SAP Cloud Platform Identity Authentication 的单一登录，请按照以下步骤操作：**

1. 在 Azure 门户的“SAP Cloud Platform Identity Authentication”应用集成页上，选择“单一登录”。

    ![配置单一登录链接][4]

1. 在“单一登录”对话框中，选择“基于 SAML 的单一登录”下的“模式”，启用单一登录。
 
    ![“单一登录”对话框](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_samlbase.png)

1. 如果要在“IDP”发起的模式下配置应用程序，请在“SAP Cloud Platform Identity Authentication 域和 URL”部分中执行下列步骤：  

    ![SAP Cloud Platform Identity Authentication 域和 URL 单一登录信息](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url.png)

    a. 在“标识符”框中，使用以下模式键入 URL：`<IAS-tenant-id>.accounts.ondemand.com`

    b. 在“回复 URL”框中，使用以下模式键入 URL：`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [SAP Cloud Platform Identity Authentication 客户支持团队](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)获取这些值。 如果不了解标识符值，请阅读有关[租户 SAML 2.0 配置](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)的 SAP Cloud Platform Identity Authentication 文档。

1. 若要为应用配置 SP 启动模式，请选中“显示高级 URL 设置”。

    ![SAP Cloud Platform Identity Authentication 域和 URL 单一登录信息](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_url1.png)

    在“登录 URL”框中，键入采用以下模式的 URL：`{YOUR BUSINESS APPLICATION URL}`。

    > [!NOTE]
    > 此值不是真实值。 请将此值更新为实际登录 URL。 请使用特定业务应用程序登录 URL。 如果有任何疑问，请联系 [SAP Cloud Platform Identity Authentication 客户支持团队](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)。

1. 在“SAML 签名证书”部分中，选择“元数据 XML”。 然后，将元数据文件保存到计算机。

    ![证书下载链接](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_certificate.png)

1. SAP Cloud Platform Identity Authentication 应用程序要求 SAML 断言采用特定格式。 在应用集成页的“用户属性”部分中，管理这些属性的值。 下面的屏幕截图展示了此格式示例。 

    ![配置单一登录](./media/sap-hana-cloud-platform-identity-authentication-tutorial/attribute.png)

1. 如果 SAP 应用需要 firstName 等属性，请在“用户属性”部分中添加“firstName”属性。 在“SAML 令牌属性”对话框的“单一登录”对话框中，可以使用此选项。

    a. 若要打开“添加属性”对话框，请选择“添加属性”。 
    
    ![配置单一登录](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_04.png)
    
    ![配置单一登录](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_attribute_05.png)
    
    b. 在“名称”框中，键入属性名“firstName”。
    
    c. 在“值”列表中，选择属性值“user.givenname”。
    
    d. 选择“确定”。

1. 选择“保存”按钮。

    ![配置单一登录“保存”按钮](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_general_400.png)

1. 在“SAP Cloud Platform Identity Authentication 配置”部分中，选择“配置 SAP Cloud Platform Identity Authentication”，打开“配置登录”窗口。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”。

    ![SAP Cloud Platform Identity Authentication 配置](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_configure.png) 

1. 若要为应用配置 SSO，请转到“SAP Cloud Platform Identity Authentication 管理控制台”。 URL 采用以下模式：`https://<tenant-id>.accounts.ondemand.com/admin`。 然后，阅读与 SAP Cloud Platform Identity Authentication 相关的文档 - [与 Microsoft Azure AD 集成](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)。 

1. 在 Azure 门户中，选择“保存”按钮。

1. 仅当要为另一个 SAP 应用添加和启用 SSO 时，才继续执行以下步骤。 重复执行“从库添加 SAP Cloud Platform Identity Authentication”部分下的步骤。

1. 在 Azure 门户的“SAP Cloud Platform Identity Authentication”应用集成页上，选择“链接登录”。

    ![配置链接登录](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

1. 保存配置。

>[!NOTE] 
>新应用利用旧 SAP 应用的单一登录配置。 确保在 SAP Cloud Platform Identity Authentication 管理控制台中使用相同的企业标识提供者。

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！  从“Active Directory” > “企业应用”部分添加此应用后，选择“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)中阅读有关嵌入式文档功能的详细信息。
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

   ![创建 Azure AD 测试用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”按钮。

    ![“Azure Active Directory”按钮](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_01.png)

1. 若要显示用户列表，请转到“用户和组”，选择“所有用户”。

    ![“用户和组”以及“所有用户”链接](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_02.png)

1. 若要打开“用户”对话框，请在“所有用户”对话框顶部选择“添加”。

    ![“添加”按钮](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_03.png)

1. 在“用户”对话框中，执行以下步骤：

    ![“用户”对话框](./media/sap-hana-cloud-platform-identity-authentication-tutorial/create_aaduser_04.png)

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入用户 Britta Simon 的电子邮件地址。

    c. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    d. 选择**创建**。
 
### <a name="create-an-sap-cloud-platform-identity-authentication-test-user"></a>创建 SAP Cloud Platform Identity Authentication 测试用户

无需在 SAP Cloud Platform Identity Authentication 上创建用户。 Azure AD 用户存储中的用户可以使用 SSO 功能。

SAP Cloud Platform Identity Authentication 支持“联合身份验证”选项。 使用此选项，应用可以检查 SAP Cloud Platform Identity Authentication 的用户存储中是否有经过公司标识提供者验证的用户。 

“联合身份验证”选项默认处于禁用状态。 如果启用了“联合身份验证”，只有已导入 SAP Cloud Platform Identity Authentication 的用户才能访问应用。 

若要详细了解如何启用或禁用 SAP Cloud Platform Identity Authentication 的联合身份验证，请参阅[配置 SAP Cloud Platform Identity Authentication 用户存储的联合身份验证](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)中的“启用 SAP Cloud Platform Identity Authentication 的联合身份验证”。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP Cloud Platform Identity Authentication 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**若要将 Britta Simon 分配到 SAP Cloud Platform Identity Authentication，请按照以下步骤操作：**

1. 在 Azure 门户中，打开应用程序视图，并转到目录视图。 然后，转到“企业应用”，并选择“所有应用”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“SAP Cloud Platform Identity Authentication”。

    ![应用程序列表中的“SAP Cloud Platform Identity Authentication”链接](./media/sap-hana-cloud-platform-identity-authentication-tutorial/tutorial_sapcpia_app.png)  

1. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202]

1. 选择“添加”按钮。 然后，在“添加分配”对话框中，选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框中，选择“用户”列表内的“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

在访问面板中选择“SAP Cloud Platform Identity Authentication”磁贴后，就会自动登录 SAP Cloud Platform Identity Authentication 应用。

有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。 

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapcloudauth-tutorial/tutorial_general_01.png
[2]: ./media/sapcloudauth-tutorial/tutorial_general_02.png
[3]: ./media/sapcloudauth-tutorial/tutorial_general_03.png
[4]: ./media/sapcloudauth-tutorial/tutorial_general_04.png

[100]: ./media/sapcloudauth-tutorial/tutorial_general_100.png

[200]: ./media/sapcloudauth-tutorial/tutorial_general_200.png
[201]: ./media/sapcloudauth-tutorial/tutorial_general_201.png
[202]: ./media/sapcloudauth-tutorial/tutorial_general_202.png
[203]: ./media/sapcloudauth-tutorial/tutorial_general_203.png
