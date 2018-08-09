---
title: 教程：Azure Active Directory 与 SAP HANA 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 SAP HANA 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: cef4a146-f4b0-4e94-82de-f5227a4b462c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: e498b0ca4b9efe09c2fe2f2bfcdcb3cc68b9c2c4
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39430247"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-hana"></a>教程：将 Azure Active Directory 与 SAP HANA 集成

在本教程中，了解如何将 SAP HANA 与 Azure Active Directory (Azure AD) 集成。

将 SAP HANA 与 Azure AD 集成可提供以下优势：

- 可在 Azure AD 中控制谁有权访问 SAP HANA。
- 可以让用户使用其 Azure AD 帐户自动登录到 SAP HANA。
- 可在一个中心位置（即 Azure 门户）管理帐户。

有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要配置 Azure AD 与 SAP HANA 的集成，需要以下项：

- Azure AD 订阅
- 已启用单一登录 (SSO) 的 SAP HANA 订阅
- 一个在任何公共 IaaS、本地、Azure VM 或 Azure 中的 SAP 大型实例上运行的 HANA 实例
- XSA 管理 Web 接口以及安装在 HANA 实例上的 HANA Studio

> [!NOTE]
> 我们不建议使用 SAP HANA 的生产环境测试本教程中的步骤。 首先在应用程序的开发环境或过渡环境中测试集成，然后使用生产环境。

若要测试本教程中的步骤，请遵循以下建议：

- 除非必要，请勿使用生产环境。
- 如果尚未获取 Azure AD 试用环境，可获取 [Azure AD 一个月免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，会在测试环境中测试 Azure AD 单一登录。 本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 SAP HANA
1. 配置和测试 Azure AD 单一登录

## <a name="add-sap-hana-from-the-gallery"></a>从库中添加 SAP HANA
若要配置 SAP HANA 与 Azure AD 的集成，请从库中将 SAP HANA 添加到托管 SaaS 应用列表。

**若要从库中添加 SAP HANA，请执行以下步骤：**

1. 在 [Azure 门户](https://portal.azure.com)的左窗格中，选择“Azure Active Directory”图标。 

    ![“Azure Active Directory”按钮][1]

1. 转到“企业应用程序”。 然后转到“所有应用程序”。

    ![“企业应用程序”边栏选项卡][2]
    
1. 若要添加新应用程序，请选择对话框顶部的“新建应用程序”按钮。

    ![“新建应用程序”按钮][3]

1. 在搜索框中，键入 **SAP HANA**。 然后从结果窗格中选择“SAP HANA”。 最后，选择“添加”按钮以添加该应用程序。 

    ![新建应用程序](./media/saphana-tutorial/tutorial_saphana_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>配置和测试 Azure AD 单一登录
在本部分中，基于一个名为“Britta Simon”的测试用户使用 SAP HANA 配置和测试 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SAP HANA 用户。 换句话说，需要在 Azure AD 用户与 SAP HANA 中的相关用户之间建立链接。

在“SAP HANA”中，提供与 Azure AD 中的“用户名”值相同的“用户名”值。 此步骤会在两个用户之间建立链接。

若要配置并测试 SAP HANA 的 Azure AD 单一登录，请完成以下构建基块：

1. [配置 Azure AD 单一登录](#configuring-azure-ad-single-sign-on)，使用户能够使用此功能。
1. [创建 Azure AD 测试用户](#creating-an-azure-ad-test-user)，使用 Britta Simon 测试 Azure AD 单一登录。
1. [创建 SAP HANA 测试用户](#creating-a-sap-hana-test-user) - 在 SAP HANA 中创建 Britta Simon 的对应用户，将其链接到用户的 Azure AD 表示形式。
1. [分配 Azure AD 测试用户](#assigning-the-azure-ad-test-user)，使 Britta Simon 能够使用 Azure AD 单一登录。
1. [测试单一登录](#testing-single-sign-on)，以验证配置是否正常工作。

### <a name="configure-azure-ad-single-sign-on"></a>配置 Azure AD 单一登录

在本部分中，将在 Azure 门户中启用 Azure AD 单一登录并在 SAP HANA 应用程序中配置单一登录。

**若要为 SAP HANA 配置 Azure AD 单一登录，请执行以下步骤：**

1. 在 Azure 门户中的“SAP HANA”应用程序集成页上，选择“单一登录”。

    ![配置单一登录][4]

1. 在“单一登录”对话框中的“基于 SAML 的登录”下，选择“模式”。
 
    ![“单一登录”对话框](./media/saphana-tutorial/tutorial_saphana_samlbase.png)

1. 在“SAP HANA 域和 URL”部分中执行以下步骤：

    ![域和 URL 单一登录信息](./media/saphana-tutorial/tutorial_saphana_url.png)

    a. 在“标识符”框中键入以下值：`HA100` 

    b. 在“回复 URL”框中，使用以下模式键入 URL：`https://<Customer-SAP-instance-url>/sap/hana/xs/saml/login.xscfunc`

    > [!NOTE] 
    > 这些不是实际值。 使用实际标识符和回复 URL 更新这些值。 请联系 [SAP HANA 客户支持团队](https://cloudplatform.sap.com/contact.html)获取这些值。 

1. 在“SAML 签名证书”部分中，选择“元数据 XML”。 然后，将元数据文件保存到计算机。

    ![证书下载链接](./media/saphana-tutorial/tutorial_saphana_certificate.png) 

    >[!Note]
    >如果证书未激活，请通过在 Azure AD 中选中“激活新证书”复选框来激活它。 

1. SAP HANA 应用程序需要特定格式的 SAML 断言。 以下屏幕截图显示了此格式的示例。 

    此处已使用 **user.mail** 的 **ExtractMailPrefix()** 函数映射了“用户标识符”。 这会提供用户电子邮件的前缀值，即唯一用户 ID。 在每条成功响应中，都会将此用户 ID 发送给 SAP HANA 应用程序。

    ![配置单一登录](./media/saphana-tutorial/attribute.png)

1. 在“单一登录”对话框的“用户属性”部分中执行以下操作：

    a. 在“用户标识符”下拉列表中选择“ExtractMailPrefix”。
    
    b. 在“邮件”下拉列表中选择“user.mail”。

1. 选择“保存”按钮。

    ![配置单一登录 -“保存”按钮](./media/saphana-tutorial/tutorial_general_400.png)
    
1. 若要在 SAP HANA 端配置单一登录，请通过转到相应的 HTTPS 终结点登录到“HANA XSA Web 控制台”。

    > [!NOTE]
    > 在默认配置中，URL 将请求重定向到登录屏幕，这需要经过身份验证的 SAP HANA 数据库用户的凭据。 登录的用户必须有权执行 SAML 管理任务。

1. 在 XSA Web 界面中，转到“SAML 标识提供者”。 选择屏幕底部的 **+** 按钮显示“添加标识提供者信息”窗格。 然后执行以下步骤：

    ![添加标识提供者](./media/saphana-tutorial/sap1.png)

    a. 在“添加标识提供者信息”窗格中，将从 Azure 门户下载的元数据 XML 内容粘贴到“元数据”框中。

    ![添加标识提供者设置](./media/saphana-tutorial/sap2.png)

    b. 如果 XML 文档的内容有效，则分析过程会提取“常规数据”屏幕区域中的“使用者、实体 ID 和颁发者”字段所需的信息。 此外，还会提取“目标”屏幕区域中的 URL 字段（例如，“基 URL”和“单一登录 URL (*)”字段）所需的信息。

    ![添加标识提供者设置](./media/saphana-tutorial/sap3.png)

    c. 在“常规数据”屏幕区域的“名称”框中，输入新 SAML SSO 标识提供者的名称。

    > [!NOTE]
    > SAML IDP 的名称是必需的，且必须唯一。 将 SAML 选作 SAP HANA XS 应用程序要使用的身份验证方法时，该名称会显示在可用的 SAML IDP 列表中。 例如，可以在 XS 项目管理工具的“身份验证”屏幕区域中执行此操作。

1. 选择“保存”保存 SAML 标识提供者的详细信息并将新 SAML IDP 添加到已知 SAML IDP 列表。

    ![“保存”按钮](./media/saphana-tutorial/sap4.png)

1. 在 HANA Studio 中“配置”选项卡的系统属性内，根据 **saml** 筛选设置。 然后将 **assertion_timeout** 从 **10 秒**调整为 **120 秒**。

    ![assertion_timeout 设置](./media/saphana-tutorial/sap7.png)

> [!TIP]
> 之后在设置应用时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！ 从“Active Directory” > “企业应用程序”部分添加该应用后，选择“单一登录”选项卡，即可通过底部的“配置”部分访问嵌入式文档。 可在 [Azure AD 嵌入式文档](https://go.microsoft.com/fwlink/?linkid=845985)中阅读有关嵌入式文档功能的详细信息。
> 

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
本部分的目的是在 Azure 门户中创建名为 Britta Simon 的测试用户。

![创建 Azure AD 用户][100]

**若要在 Azure AD 中创建测试用户，请执行以下步骤：**

1. 在 **Azure 门户**的左窗格中，选择“Azure Active Directory”图标。

    ![“Azure Active Directory”按钮](./media/saphana-tutorial/create_aaduser_01.png) 

1. 若要显示用户列表，请转到“用户和组”。 然后选择“所有用户”。
    
    ![“用户和组”以及“所有用户”链接](./media/saphana-tutorial/create_aaduser_02.png) 

1. 若要打开“用户”对话框，请选择对话框顶部的“添加”。
 
    ![“添加”按钮](./media/saphana-tutorial/create_aaduser_03.png) 

1. 在“用户”对话框中执行以下步骤：
 
    ![“用户”对话框](./media/saphana-tutorial/create_aaduser_04.png) 

    a. 在“姓名”框中，键入“BrittaSimon”。

    b. 在“用户名”框中，键入 BrittaSimon 的**电子邮件地址**。

    c. 选择“显示密码”并记下密码。

    d. 选择**创建**。
 
### <a name="create-a-sap-hana-test-user"></a>创建 SAP HANA 测试用户

若要使 Azure AD 用户能够登录到 SAP HANA，必须在 SAP HANA 中预配这些用户。
SAP HANA 支持默认启用的实时预配。

如果需要手动创建用户，请执行以下步骤：

>[!NOTE]
>可更改用户使用的外部身份验证。 他们可以使用 Kerberos 等外部系统进行身份验证。 有关外部标识的详细信息，请联系[域管理员](https://cloudplatform.sap.com/contact.html)。

1. 以管理员身份打开 [SAP HANA Studio](https://help.sap.com/viewer/a2a49126a5c546a9864aae22c05c3d0e/2.0.01/en-us)，并为 DB-User 启用 SAML SSO。

    ![创建用户](./media/saphana-tutorial/sap5.png)

1. 选中“SAML”左侧隐藏的复选框，并选择“配置”链接。

1. 选择“添加”以添加 SAML IDP。  选择相应的 SAML IDP，再选择“确定”。

1. 添加“外部标识”（本例中为 BrittaSimon）或选择“任何”。 然后选择“确定”。

    >[!Note]
    >如果未选中“任何”复选框，则 HANA 中的用户名必须与域后缀前面的 UPN 中的用户名完全匹配。 （例如，BrittaSimon@contoso.com 在 HANA 中将会变成 BrittaSimon。）

1. 针对测试，请将所有“XS”角色分配给该用户。

    ![分配角色](./media/saphana-tutorial/sap6.png)

    > [!TIP]
    > 请只授予适合用例的权限。

1. 保存用户。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 Britta Simon 访问 SAP HANA 的权限，允许她使用 Azure 单一登录。

![分配用户角色][200] 

**要将 Britta Simon 分配到 SAP HANA，请执行以下步骤：**

1. 在 Azure 门户中，打开应用程序视图。 转到目录视图，再转到“企业应用程序”。 选择“所有应用程序”。

    ![分配用户][201] 

1. 在应用程序列表中，选择“SAP HANA”。

    ![分配用户](./media/saphana-tutorial/tutorial_saphana_app.png) 

1. 在左侧菜单中，选择“用户和组”。

    ![“用户和组”链接][202] 

1. 选择“添加”按钮。 在“添加分配”对话框中选择“用户和组”。

    ![“添加分配”窗格][203]

1. 在“用户和组”对话框中，选择“用户”列表中的“Britta Simon”。

1. 在“用户和组”对话框中单击“选择”按钮。

1. 在“添加分配”对话框中选择“分配”按钮。
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

选择访问面板中的“SAP HANA”磁贴时，应会自动登录到 SAP HANA 应用程序。
有关访问面板的详细信息，请参阅[访问面板简介](../user-help/active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/saphana-tutorial/tutorial_general_01.png
[2]: ./media/saphana-tutorial/tutorial_general_02.png
[3]: ./media/saphana-tutorial/tutorial_general_03.png
[4]: ./media/saphana-tutorial/tutorial_general_04.png

[100]: ./media/saphana-tutorial/tutorial_general_100.png

[200]: ./media/saphana-tutorial/tutorial_general_200.png
[201]: ./media/saphana-tutorial/tutorial_general_201.png
[202]: ./media/saphana-tutorial/tutorial_general_202.png
[203]: ./media/saphana-tutorial/tutorial_general_203.png

