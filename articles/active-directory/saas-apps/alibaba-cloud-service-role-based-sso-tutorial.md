---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Alibaba Cloud Service (Role-based SSO) 的集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 Alibaba Cloud Service (Role-based SSO)（阿里云服务（基于角色的 SSO））之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3667841e-acfc-4490-acf5-80d9ca3e71e8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99b9173c817cc3ecf4b9a34ec6906af0b4de70e6
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/19/2019
ms.locfileid: "71120774"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-alibaba-cloud-service-role-based-sso"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Alibaba Cloud Service (Role-based SSO) 的集成

本教程介绍如何将 Alibaba Cloud Service (Role-based SSO) 与 Azure Active Directory (Azure AD) 集成。 将 Alibaba Cloud Service (Role-based SSO) 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 Alibaba Cloud Service (Role-based SSO)。
* 让用户使用其 Azure AD 帐户自动登录到 Alibaba Cloud Service (Role-based SSO)。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Alibaba Cloud Service (Role-based SSO) 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* Alibaba Cloud Service (Role-based SSO) 支持 **IDP** 发起的 SSO

## <a name="adding-alibaba-cloud-service-role-based-sso-from-the-gallery"></a>从库中添加 Alibaba Cloud Service (Role-based SSO)

若要配置 Alibaba Cloud Service (Role-based SSO) 与 Azure AD 的集成，需要从库中将 Alibaba Cloud Service (Role-based SSO) 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“Alibaba Cloud Service (Role-based SSO)”   。
1. 从结果面板中选择“Alibaba Cloud Service (Role-based SSO)”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。
5. 在“Alibaba Cloud Service (Role-based SSO)”页上，单击左侧导航窗格中的“属性”，然后复制“对象 ID”并将其保存在计算机上，供以后使用。   

    ![属性配置](./media/alibaba-cloud-service-role-based-sso-tutorial/Properties.png)


## <a name="configure-and-test-azure-ad-single-sign-on-for-alibaba-cloud-service-role-based-sso"></a>配置和测试 Alibaba Cloud Service (Role-based SSO) 的 Azure AD 单一登录

使用名为 B.Simon 的测试用户配置和测试 Alibaba Cloud Service (Role-based SSO) 的 Azure AD SSO  。 若要正常使用 SSO，需要在 Azure AD 用户与 Alibaba Cloud Service (Role-based SSO) 中的相关用户之间建立链接关系。

若要配置并测试 Alibaba Cloud Service (Role-based SSO) 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
    1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 Britta Simon 测试 Azure AD 单一登录。
    1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 Britta Simon 能够使用 Azure AD 单一登录。
2. **[在 Alibaba Cloud Service 中配置基于角色的单一登录](#configure-role-based-single-sign-on-in-alibaba-cloud-service)** - 使用户能够使用此功能。
    1. **[配置 Alibaba Cloud Service (Role-based SSO) SSO](#configure-alibaba-cloud-service-role-based-sso-sso)** - 在应用程序端配置单一登录设置。
    1. **[创建 Alibaba Cloud Service (Role-based SSO) 测试用户](#create-alibaba-cloud-service-role-based-sso-test-user)** - 在 Alibaba Cloud Service (Role-based SSO) 中创建 Britta Simon 的对应用户，并将其关联到其在 Azure AD 中的表示形式。
3. **[测试单个 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中的“Alibaba Cloud Service (Role-based SSO)”应用程序集成页上，找到“管理”部分，选择“单一登录”。   
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分，如果有**服务提供程序元数据文件**，请执行以下步骤：

    >[!NOTE]
    >可以从此 [URL](https://signin.alibabacloud.com/saml-role/sp-metadata.xml) 获取服务提供程序元数据

    a. 单击“上传元数据文件”  。

    b. 单击“文件夹徽标”  来选择元数据文件并单击“上传”。 

    c. 成功上传元数据文件后，“标识符”和“回复 URL”值会自动填充在“Alibaba Cloud Service (Role-based SSO)”部分的文本框中：  

    > [!Note]
    > 如果“标识符”和“回复 URL”值未自动填充，请根据要求手动填充这些值。  

1. Alibaba Cloud Service (Role-based SSO) 要求在 Azure AD 中配置角色。 角色声明已预先配置，因此不需配置它，但仍需按照此[文章](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)的说明在 Azure AD 中创建它。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中找到“联合元数据 XML”，选择“下载”以下载该证书并将其保存在计算机上     。

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 Alibaba Cloud Service (Role-based SSO)”部分，根据要求复制相应的 URL。 

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

在本部分，我们通过授予 B.Simon 访问 Alibaba Cloud Service (Role-based SSO) 的权限，使其能够使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在“应用程序”列表中，选择“Alibaba Cloud Service (Role-based SSO)”  。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”选项卡上，从用户列表中选择“u2”并单击“选择”。   然后单击“分配”  。

    ![测试配置](./media/alibaba-cloud-service-role-based-sso-tutorial/test01.png)

1. 查看分配的角色并测试 Alibaba Cloud Service (Role-based SSO)。

    ![测试配置](./media/alibaba-cloud-service-role-based-sso-tutorial/test02.png)

    >[!NOTE]
    >分配用户 (u2) 后，创建的角色会自动附加到该用户。 如果创建了多个角色，则需要将相应的角色附加到用户。 若要在多个 Alibaba Cloud 帐户中实现 Azure AD 的基于角色的 SSO，请重复前述步骤。

## <a name="configure-role-based-single-sign-on-in-alibaba-cloud-service"></a>在 Alibaba Cloud Service 中配置基于角色的单一登录

1. 使用 Account1 登录到 Alibaba Cloud [RAM 控制台](https://account.alibabacloud.com/login/login.htm?oauth_callback=https%3A%2F%2Fram.console.aliyun.com%2F%3Fspm%3Da2c63.p38356.879954.8.7d904e167h6Yg9)。

2. 在左侧导航窗格中选择“SSO”。 

3. 在“基于角色的 SSO”选项卡上，单击“创建 IdP”。  

4. 在显示的页面上的“IdP 名称”字段中输入 `AAD`，在“说明”字段中输入说明，单击“上传”以上传先前下载的联合元数据文件，然后单击“确定”。   

5. 成功创建 IdP 后，单击“创建 RAM 角色”。 

6. 在“RAM 角色名称”字段中输入 `AADrole`，从“选择 IdP”下拉列表中选择 `AAD`，然后单击“确定”。  

    >[!NOTE]
    >可根据需要向该角色授予权限。 创建 IdP 和相应的角色后，我们建议保存 IdP 和角色的 ARN，供以后使用。 可以在 IdP 信息页和角色信息页上获取 ARN。

7. 将 Alibaba Cloud RAM 角色 (AADrole) 与 Azure AD 用户 (u2) 相关联：若要将 RAM 角色与 Azure AD 用户相关联，必须执行以下步骤在 Azure AD 中创建一个角色：

    a. 登录到 [Azure AD Graph 资源管理器](https://developer.microsoft.com/graph/graph-explorer?spm=a2c63.p38356.879954.9.7d904e167h6Yg9)。

    b. 单击“修改权限”以获取创建角色所需的权限。 

    ![Graph 配置](./media/alibaba-cloud-service-role-based-sso-tutorial/graph01.png)

    c. 从列表中选择以下权限，然后单击“修改权限”，如下图所示。 

    ![Graph 配置](./media/alibaba-cloud-service-role-based-sso-tutorial/graph02.png)

    >[!NOTE]
    >授予权限后，再次登录到 Graph 资源管理器。

    d. 在“Graph 资源管理器”页上，从第一个下拉列表中选择“GET”，从第二个下拉列表中选择“beta”。   在下拉列表旁边的字段中输入 `https://graph.microsoft.com/beta/servicePrincipals`，然后单击“运行查询”。 

    ![Graph 配置](./media/alibaba-cloud-service-role-based-sso-tutorial/graph03.png)

    >[!NOTE]
    >如果使用多个目录，可以在查询字段中输入 `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`。

    e. 在“响应预览”部分，从“服务主体”中提取 appRoles 属性，供以后使用。 

    ![Graph 配置](./media/alibaba-cloud-service-role-based-sso-tutorial/graph05.png)

    >[!NOTE]
    >在查询字段中输入 `https://graph.microsoft.com/beta/servicePrincipals/<objectID>` 可以找到 appRoles 属性。 请注意，`objectID` 是从 Azure AD 的“属性”页复制的对象 ID。 

    f. 返回 Graph 资源管理器，将方法从 **GET** 更改为 **PATCH**，将以下内容粘贴到“请求正文”部分，然后单击“运行查询”：  
    ```
    { 
    "appRoles": [
        { 
        "allowedMemberTypes":[
            "User"
        ],
        "description": "msiam_access",
        "displayName": "msiam_access",
        "id": "41be2db8-48d9-4277-8e86-f6d22d35****",
        "isEnabled": true,
        "origin": "Application",
        "value": null
        },
        { "allowedMemberTypes": [
            "User"
        ],
        "description": "Admin,AzureADProd",
        "displayName": "Admin,AzureADProd",
        "id": "68adae10-8b6b-47e6-9142-6476078cdbce",
        "isEnabled": true,
        "origin": "ServicePrincipal",
        "value": "acs:ram::187125022722****:role/aadrole,acs:ram::187125022722****:saml-provider/AAD"
        }
    ]
    }
    ```
    > [!NOTE]
    > `value` 是在 RAM 控制台中创建的 IdP 和角色的 ARN。 在此处可根据需要添加多个角色。 Azure AD 会在 SAML 响应中将这些角色的值作为声明值发送。 但是，对于修补操作，只能在 `msiam_access` 部分的后面添加新角色。 若要顺利完成创建过程，我们建议使用 ID 生成器（例如 GUID 生成器）实时生成 ID。

    g. 使用所需的角色修补“服务主体”后，请遵循本教程的“分配 Azure AD 测试用户”部分所述的步骤，将角色附加到 Azure AD 用户 (u2)。 

### <a name="configure-alibaba-cloud-service-role-based-sso-sso"></a>配置 Alibaba Cloud Service (Role-based SSO) SSO

若要在 **Alibaba Cloud Service (Role-based SSO)** 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [Alibaba Cloud Service (Role-based SSO) 支持团队](https://www.aliyun.com/service/)。  他们会对此进行设置，使两端的 SAML SSO 连接均正确设置。

### <a name="create-alibaba-cloud-service-role-based-sso-test-user"></a>创建 Alibaba Cloud Service (Role-based SSO) 测试用户

在本部分，我们将在 Alibaba Cloud Service (Role-based SSO) 中创建名为 Britta Simon 的用户。 在 [Alibaba Cloud Service (Role-based SSO) 支持团队](https://www.aliyun.com/service/)的配合下，将用户添加到 Alibaba Cloud Service (Role-based SSO) 平台。 使用单一登录前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO 

完成前面所述的配置后，执行以下步骤测试 Alibaba Cloud Service (Role-based SSO)：

1. 在 Azure 门户中转到“Alibaba Cloud Service (Role-based SSO)”页，选择“单一登录”，然后单击“测试”。   

    ![测试配置](./media/alibaba-cloud-service-role-based-sso-tutorial/test03.png)

2. 单击“作为当前用户登录”。 

    ![测试配置](./media/alibaba-cloud-service-role-based-sso-tutorial/test04.png)

3. 在帐户选择页上选择“u2”。

    ![测试配置](./media/alibaba-cloud-service-role-based-sso-tutorial/test05.png)

4. 此时会显示以下页面，指出基于角色的 SSO 成功。

    ![测试配置](./media/alibaba-cloud-service-role-based-sso-tutorial/test06.png)

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [在 Azure AD 中试用 Alibaba Cloud Service (Role-based SSO)](https://aad.portal.azure.com/)

