---
title: 教程：将 Azure Active Directory 与 SAP Business Object Cloud 集成 | Microsoft 文档
description: 了解如何在 Azure Active Directory 和 SAP Business Object Cloud 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: jeedes
ms.openlocfilehash: 42eb1c8332a29ff07c83b962aaa8f8e2f04fd07b
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/21/2018
ms.locfileid: "36308293"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-object-cloud"></a>教程：将 Azure Active Directory 与 SAP Business Object Cloud 集成

本教程介绍了如何将 SAP Business Object Cloud 与 Azure Active Directory (Azure AD) 集成。

将 SAP Business Object Cloud 与 Azure AD 集成具有以下优势：

- 可在 Azure AD 中控制谁有权访问 SAP Business Object Cloud。
- 可以通过使用单一登录和用户的 Azure AD 帐户使用户自动登录到 SAP Business Object Cloud。
- 可在一个中心位置（即 Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)。

## <a name="prerequisites"></a>先决条件

若要设置 Azure AD 与 SAP Business Object Cloud 的集成，需备齐以下各项：

- Azure AD 订阅
- 已启用单一登录的 SAP Business Object Cloud

> [!NOTE]
> 不建议在生产环境中测试本教程中的步骤。

测试本教程中的步骤的建议：

- 除非必要，否则请勿使用生产环境。
- 如果没有 Azure AD 试用环境，可以[获取一个月免费试用版](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="scenario-description"></a>方案描述
在本教程中，将在测试环境中测试 Azure AD 单一登录。 

本教程中概述的方案包括两个主要构建基块：

1. 从库中添加 SAP Business Object Cloud。
2. 设置和测试 Azure AD 单一登录。

## <a name="add-sap-business-object-cloud-from-the-gallery"></a>从库中添加 SAP Business Object Cloud
若要设置 SAP Business Object Cloud 与 Azure AD 的集成，需要在库中将 SAP Business Object Cloud 添加到托管 SaaS 应用列表。

从库中添加 SAP Business Object Cloud：

1. 在 [Azure 门户](https://portal.azure.com) 的左侧菜单中，选择“Azure Active Directory”。 

    ![“Azure Active Directory”按钮][1]

2. 选择“企业应用程序”，然后选择“所有应用程序”。

    ![企业应用程序页][2]
    
3. 若要添加新的应用程序，请选择“新建应用程序”。

    ![“新增应用程序”按钮][3]

4. 在搜索框中，输入“SAP Business Object Cloud”。

    ![搜索框](./media/sapboc-tutorial/tutorial_sapboc_search.png)

5. 在结果面板中，选择“SAP Business Object Cloud”，然后单击“添加”。

    ![结果列表中的 SAP Business Object Cloud](./media/sapboc-tutorial/tutorial_sapboc_addfromgallery.png)

##  <a name="set-up-and-test-azure-ad-single-sign-on"></a>设置和测试 Azure AD 单一登录

在本部分中，将基于一个名为 Britta Simon 的测试用户设置和测试 SAP Business Object Cloud 的 Azure AD 单一登录。

若要运行单一登录，Azure AD 需要知道与 Azure AD 用户相对应的 SAP Business Object Cloud 用户。 必须建立 Azure AD 用户与 SAP Business Object Cloud 中相关用户之间的关联关系。

可通过将 Azure AD 中“用户名”的值指定为 SAP Business Object Cloud 中“用户名”的值来建立此关联关系。

若要配置和测试 SAP Business Object Cloud 的 Azure AD 单一登录，请完成以下任务：

1. [设置 Azure AD 单一登录](#set-up-azure-ad-single-sign-on)。 设置用户以使用此功能。
2. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)。 通过用户 Britta Simon 测试 Azure AD 单一登录。
3. [创建 SAP Business Object Cloud 测试用户](#create-an-sap-business-object-cloud-test-user)。 在 SAP Business Object Cloud 中创建一个与 Azure AD 中的 Britta Simon 相对应的关联用户。
4. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)。 设置 Britta Simon 以使用 Azure AD 单一登录。
5. [测试单一登录](#test-single-sign-on)。 验证配置是否正常工作。

### <a name="set-up-azure-ad-single-sign-on"></a>设置 Azure AD 单一登录

在此部分中，启用 Azure 门户中的 Azure AD 单一登录。 然后，在 SAP Business Object Cloud 应用程序中设置单一登录。

若要设置 SAP Business Object Cloud 的 Azure AD 单一登录：

1. 在 Azure 门户中的“SAP Business Object Cloud”应用程序集成页上，选择“单一登录”。

    ![选择“单一登录”][4]

2. 在“单一登录”页上，选择“基于 SAML 的登录”作为“模式”。
 
    ![选择“基于 SAML 的登录”](./media/sapboc-tutorial/tutorial_sapboc_samlbase.png)

3. 在“SAP Business Object Cloud 域和 URL”下，完成以下步骤：

    1. 在“登录 URL”框中，输入具有以下模式的 URL： 
    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    2. 在“标识符”框中，输入具有以下模式的 URL：
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    ![SAP Business Object Cloud 域和 URL 网页的 URL](./media/sapboc-tutorial/tutorial_sapboc_url.png)
 
    > [!NOTE] 
    > 这些 URL 中的值是仅用于演示。 请使用实际登录 URL 和标识符 URL 来更新这些值。 若要获取登录 URL，请联系 [SAP Business Object Cloud 客户端支持团队](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/en-US)。 可以通过从管理员控制台下载 SAP Business Object Cloud 元数据来获取标识符 URL。 本教程后面将对有所解释。 

4. 在“SAML 签名证书”下，选择“元数据 XML”。 然后，将元数据文件保存到计算机。

    ![选择元数据 XML](./media/sapboc-tutorial/tutorial_sapboc_certificate.png) 

5. 选择“保存”。

    ![选择“保存”](./media/sapboc-tutorial/tutorial_general_400.png)

6. 在其他 Web 浏览器窗口中，以管理员身份登录到 SAP Business Object Cloud 公司站点。

7. 选择“菜单” > “系统” > “管理”。
    
    ![依次选择“菜单”、“系统”和“管理”](./media/sapboc-tutorial/config1.png)

8. 在“安全性”选项卡上，选择“编辑”（笔形）图标。
    
    ![在“安全性”选项卡上，选择“编辑”图标](./media/sapboc-tutorial/config2.png)  

9. 选择“SAML 单一登录 (SSO)”作为“身份验证方法”。

    ![选择“SAML 单一登录 (SSO)”作为身份验证方法](./media/sapboc-tutorial/config3.png)  

10. 若要下载服务提供程序元数据（步骤 1），请选择“下载”。 在元数据文件中，查找并复制“entityID”值。 在 Azure 门户的“SAP Business Object Cloud 域和 URL”下，在“标识符”框中粘贴值。

    ![复制并粘贴 entityID 值](./media/sapboc-tutorial/config4.png)  

11. 若要上传从 Azure 门户中下载的文件中的服务提供者元数据（步骤 2），请在“上传标识提供者元数据”下，选择“上传”。  

    ![在“上传标识提供者元数据”下，选择“上传”](./media/sapboc-tutorial/config5.png)

12. 在“用户属性”列表中，选择要用于实现的用户属性（步骤 3）。 此用户属性将映射到标识提供程者。 若要在用户页上输入自定义属性，请使用“自定义 SAML 映射”选项。 或者，可以选择“电子邮件”或“用户 ID”作为用户属性。 本示例中选择的是“电子邮件”，因为我们映射的用户标识符声明包含 Azure 门户中“用户属性”部分的“userprincipalname”属性。 这提供了一个唯一的用户电子邮件，它将被发送到 SAP Business Object Cloud 应用程序作为每个成功的 SAML 响应。

    ![选择用户属性](./media/sapboc-tutorial/config6.png)

13. 若要验证标识提供者的帐户（步骤 4），请在“登录凭据（电子邮件）”框中，输入用户的电子邮件地址。 然后，选择“验证帐户”。 系统会将登录凭据添加到用户帐户。

    ![输入电子邮件，并选择验证帐户](./media/sapboc-tutorial/config7.png)

14. 选择“保存”图标。

    ![“保存”图标](./media/sapboc-tutorial/save.png)

> [!TIP]
> 之后在设置应用程序时，就可以在 [Azure 门户](https://portal.azure.com)中阅读这些说明的简明版本了！ 从“Active Directory” > “企业应用程序”添加应用后，选择“单一登录”选项卡。然后可以在页面底部的“配置”部分访问嵌入式文档。 有关详细信息，请参阅 [Azure AD 嵌入式文档]( https://go.microsoft.com/fwlink/?linkid=845985)。

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户
在本部分中，会在 Azure 门户中创建名为“Britta Simon”的测试用户。

若要在 Azure AD 中创建测试用户：

1. 在 Azure 门户的左侧菜单中，选择“Azure Active Directory”。

    ![创建 Azure AD 测试用户](./media/sapboc-tutorial/create_aaduser_01.png) 

2. 若要显示用户列表，请转到“用户和组”，选择“所有用户”。
    
    ![创建 Azure AD 测试用户](./media/sapboc-tutorial/create_aaduser_02.png) 

3. 若要打开“用户”对话框，请选择“添加”。
 
    ![创建 Azure AD 测试用户](./media/sapboc-tutorial/create_aaduser_03.png) 

4. 在“用户”对话框中，完成以下步骤：
 
    1. 在“姓名”框中，输入“BrittaSimon”。

    2. 在“用户名”框中，输入用户 Britta Simon 的电子邮件地址。

    3. 选中“显示密码”复选框，然后记下“密码”框中显示的值。

    4. 选择**创建**。

        ![“用户”对话框](./media/sapboc-tutorial/create_aaduser_04.png) 

    ![创建 Azure AD 用户][100]

### <a name="create-an-sap-business-object-cloud-test-user"></a>创建 SAP Business Object Cloud 测试用户

为了使 Azure AD 用户能够登录到 SAP Business Object Cloud，必须将其预配到 SAP Business Object Cloud 中。 在 SAP Business Object Cloud 中，预配属手动任务。

若要预配用户帐户：

1. 以管理员身份登录到 SAP Business Object Cloud 公司站点。

2. 选择“菜单” > “安全” > “用户”。

    ![添加员工](./media/sapboc-tutorial/user1.png)

3. 在“用户”页上，若要添加新用户详细信息，请选择 +。 

    ![添加用户页](./media/sapboc-tutorial/user4.png)

    然后完成以下步骤：

    1. 在“用户 ID”框中，输入用户的用户 ID，如 Britta。

    2. 在“名字”框中，输入用户的名字，如 Britta。

    3. 在“姓氏”框中，输入用户的姓氏，如 Simon。

    4. 在“显示名称”框中，输入用户的全名，如 Britta Simon。

    5. 在“电子邮件”框中，输入用户的电子邮件地址，如 brittasimon@contoso.com。

    6. 在“选择角色”页上，选择适当的用户角色，然后选择“确定”。

      ![选择角色](./media/sapboc-tutorial/user3.png)

    7. 选择“保存”图标。    


### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予用户帐户访问 SAP Business Object Cloud 的权限，允许用户 Britta Simon 使用 Azure AD 单一登录。

若要将 Britta Simon 分配到 SAP Business Object Cloud：

1. 在 Azure 门户中，打开应用程序视图，并转到目录视图。 选择“企业应用程序”，然后选择“所有应用程序”。

    ![分配用户][201] 

2. 在应用程序列表中，选择“SAP Business Object Cloud”。

    ![配置单一登录](./media/sapboc-tutorial/tutorial_sapboc_app.png) 

3. 在左侧菜单中，选择“用户和组”。

    ![选择“用户和组”][202] 

4. 选择 **添加** 。 然后，在“添加分配”页上，选择“用户和组”。

    ![“添加分配”页][203]

5. 在“用户和组”页的用户列表中，选择“Britta Simon”。

6. 在“用户和组”页上，选择“选择”。

7. 在“添加分配”页上，选择“分配”。

![分配用户角色][200] 
    
### <a name="test-single-sign-on"></a>测试单一登录

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

选择访问面板中的“SAP Business Object Cloud”磁贴时，应自动登录到 SAP Business Object Cloud 应用程序。

有关访问面板的详细信息，请参阅[访问面板简介](../active-directory-saas-access-panel-introduction.md)。

## <a name="additional-resources"></a>其他资源

* [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/sapboc-tutorial/tutorial_general_01.png
[2]: ./media/sapboc-tutorial/tutorial_general_02.png
[3]: ./media/sapboc-tutorial/tutorial_general_03.png
[4]: ./media/sapboc-tutorial/tutorial_general_04.png

[100]: ./media/sapboc-tutorial/tutorial_general_100.png

[200]: ./media/sapboc-tutorial/tutorial_general_200.png
[201]: ./media/sapboc-tutorial/tutorial_general_201.png
[202]: ./media/sapboc-tutorial/tutorial_general_202.png
[203]: ./media/sapboc-tutorial/tutorial_general_203.png

