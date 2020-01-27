---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 ServiceNow 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 ServiceNow 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: a5a1a264-7497-47e7-b129-a1b5b1ebff5b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 79f5284f95f6157c70461179177179a9fbee4fe0
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293685"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-servicenow"></a>教程：Azure Active Directory 单一登录 (SSO) 与 ServiceNow 集成

在本教程中，了解如何将 ServiceNow 与 Azure Active Directory (Azure AD) 集成。 将 ServiceNow 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 ServiceNow。
* 让用户使用其 Azure AD 帐户自动登录到 ServiceNow。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 ServiceNow 单一登录 (SSO) 的订阅。
* 对于 ServiceNow，需要 Calgary 版本或更高版本的 ServiceNow 实例或租户。
* 对于 ServiceNow Express，需要 Helsinki 版本或更高版本的 ServiceNow Express 实例。
* ServiceNow 租户必须启用[多提供程序单一登录插件](https://wiki.servicenow.com/index.php?title=Multiple_Provider_Single_Sign-On#gsc.tab=0)。 可以通过[提交服务请求](https://hi.service-now.com)完成此操作。
* 对于自动配置，请为 ServiceNow 启用多提供程序插件。
* 若要安装 ServiceNow Classic（移动版）应用程序，请转到相应的商店并搜索 ServiceNow Classic 应用程序。 然后下载。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 

* ServiceNow 支持 **SP** 发起的 SSO。

* ServiceNow 支持[自动用户预配](servicenow-provisioning-tutorial.md)。

* 配置 ServiceNow 后，就可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

* 可以通过 Azure AD 来配置 ServiceNow Classic（移动版）应用程序以启用 SSO。 它同时支持 Android 和 iOS 用户。 本教程在测试环境中配置并测试 Azure AD SSO。

## <a name="add-servicenow-from-the-gallery"></a>从库中添加 ServiceNow

要配置 ServiceNow 与 Azure AD 的集成，需要从库中将 ServiceNow 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左窗格中，选择“Azure Active Directory”服务。 
1. 转到“企业应用程序”，并选择“所有应用程序”。  
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，输入“ServiceNow”   。
1. 从结果面板中选择“ServiceNow”，然后添加该应用。  在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-servicenow"></a>配置和测试 ServiceNow 的 Azure AD 单一登录

使用名为 B. Simon 的测试用户配置和测试 ServiceNow 的 Azure AD SSO  。 若要运行 SSO，需要在 Azure AD 用户与 ServiceNow 相关用户之间建立链接关系。

若要配置和测试 ServiceNow 的 Azure AD SSO，请完成以下构建基块：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
    1. [创建 Azure AD 测试用户](#create-an-azure-ad-test-user)，以使用 B.Simon 测试 Azure AD 单一登录。
    1. [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)，使 B.Simon 能够使用 Azure AD 单一登录。
    1. [针对 ServiceNow Express 配置 Azure AD SSO](#configure-azure-ad-sso-for-servicenow-express) - 让用户能够使用此功能。
2. [配置 ServiceNow](#configure-servicenow)，以在应用程序端配置 SSO 设置。
    1. [创建 ServiceNow 测试用户](#create-servicenow-test-user)，以便在 ServiceNow 中创建 B.Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
    1. [配置 ServiceNow Express SSO](#configure-servicenow-express-sso) - 在应用程序端配置单一登录设置。  
3. [测试 SSO](#test-sso)，验证配置是否正常工作。
4. [测试 ServiceNow Classic（移动版）的 SSO](#test-sso-for-servicenow-classic-mobile)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“ServiceNow”应用程序集成页上，找到“管理”部分   。 选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“设置 SAML 单一登录”页上，选择“基本 SAML 配置”对应的笔形图标以编辑设置   。

   ![“设置 SAML 单一登录”页的屏幕截图，其中突出显示了笔形图标](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“登录 URL”  中，输入使用以下模式的 URL：`https://<instance-name>.service-now.com/navpage.do`

    b. 在“标识符(实体 ID)”  中，输入使用以下模式的 URL：`https://<instance-name>.service-now.com`

    > [!NOTE]
    > 这些不是实际值。 需要使用实际的登录 URL 和标识符（稍后在本教程中介绍）来更新这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分，找到“证书(Base64)”    。 

   ![“SAML 签名证书”部分的屏幕截图，其中突出显示了“下载”](common/certificatebase64.png)

   a. 选择“复制”按钮，以复制“应用联合元数据 URL”  并将其粘贴到记事本。 本教程稍后将使用此 URL。

    b. 选择“下载”  以下载“证书(Base64)”  ，并将证书文件保存在计算机上。

1. 在“设置 ServiceNow”部分中，根据要求复制相应 URL  。

   ![“设置 ServiceNow”部分的屏幕截图，其中突出显示了 URL](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory” > “用户” > “所有用户”。   
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 对于“名称”  ，请输入 `B.Simon`。  
   1. 对于“用户名”  ，请输入 username@companydomain.extension。 例如，`B.Simon@contoso.com` 。
   1. 选择“显示密码”，然后记下“密码”框中显示的值   。
   1. 选择“创建”  。

### <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，通过授予 B.Simon 访问 ServiceNow 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，选择“企业应用程序” > “所有应用程序”。  
1. 在应用程序列表中，选择“ServiceNow”  。
1. 在应用的概述页中找到“管理”部分，然后选择“用户和组”   。

   ![“管理”部分的屏幕截图，其中突出显示了“用户和组”](common/users-groups-blade.png)

1. 选择“添加用户”。  在“添加分配”对话框中选择“用户和组”。  

    ![用户和组的屏幕截图，其中已突出显示“添加用户”](common/add-assign-user.png)

1. 在“用户和组”对话框中，从用户列表中选择“B.Simon”，然后选择“选择”。   
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  然后选择“选择”。 
1. 在“添加分配”对话框中选择“分配”。  

### <a name="configure-azure-ad-sso-for-servicenow-express"></a>为 ServiceNow Express 配置 Azure AD SSO

1. 在 [Azure 门户](https://portal.azure.com/)中的 ServiceNow 应用程序集成页上，选择“单一登录”。  

    ![ServiceNow 应用程序集成页的屏幕截图，其中已突出显示“单一登录”](common/select-sso.png)

2. 在“选择单一登录方法”对话框中，选择“SAML/WS-Fed”模式以启用单一登录。  

    ![选择单一登录方法的屏幕截图，其中突出显示了 SAML](common/select-saml-option.png)

3. 在“设置 SAML 单一登录”页上，选择笔形图标以打开“基本 SAML 配置”对话框。  

    ![“设置 SAML 单一登录”页的屏幕截图，其中突出显示了笔形图标](common/edit-urls.png)

4. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 对于“登录 URL”  ，输入使用以下模式的 URL：`https://<instance-name>.service-now.com/navpage.do`

    b. 对于“标识符(实体 ID)”  ，输入使用以下模式的 URL：`https://<instance-name>.service-now.com`

    > [!NOTE]
    > 这些不是实际值。 需要使用实际的登录 URL 和标识符（稍后在本教程中介绍）来更新这些值。 还可以参考 Azure 门户中的“基本 SAML 配置”  部分中显示的模式。

5. 在“设置 SAML 单一登录”页上的“SAML 签名证书”部分，选择“下载”以根据自己的需要从指定的选项中下载“证书(Base64)”。     然后将其保存在计算机上。

    ![“SAML 签名证书”部分的屏幕截图，其中突出显示了“下载”](common/certificatebase64.png)

6. 可以让 Azure AD 自动配置 ServiceNow 以实现基于 SAML 的身份验证。 若要启用此服务，请转到“设置 ServiceNow”  部分，选择“查看分步说明”  以打开“配置登录”  窗口。

    ![“设置 ServiceNow”部分的屏幕截图，其中突出显示了“查看分步说明”](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

7. 在“配置登录”窗体中输入 ServiceNow 实例名称、管理员用户名和管理员密码。  选择“立即配置”  。 为使此操作生效，提供的管理员用户名必须具有在 ServiceNow 中分配的 security_admin  角色。 否则，若要手动配置 ServiceNow 以使用 Azure AD 作为 SAML 标识提供者，请选择“手动配置单一登录”  。 从“快速参考”部分中复制“注销 URL”、“Azure AD 标识符”和“登录 URL”。 

    ![配置登录窗体的屏幕截图，其中突出显示了“立即配置”](./media/servicenow-tutorial/configure.png "配置应用 URL")

## <a name="configure-servicenow"></a>配置 ServiceNow

1. 以管理员身份登录到 ServiceNow 应用程序。

2. 请按照以下步骤操作，激活“集成 - 多提供程序单一登录安装程序”  插件：

    a. 在左窗格中，从搜索框中搜索“系统定义”  部分，然后选择“插件”  。

    ![“系统定义”部分的屏幕截图，其中突出显示了系统定义和插件](./media/servicenow-tutorial/tutorial_servicenow_03.png "激活插件")

    b. 搜索“集成 - 多提供程序单一登录安装程序”  。

     ![“系统插件”页面的屏幕截图，其中突出显示了“集成-多提供程序单一登录安装程序”](./media/servicenow-tutorial/tutorial_servicenow_04.png "激活插件")

    c. 选择该插件。 右键单击并选择“激活/升级”  。

     ![插件右键单击菜单的屏幕截图，其中突出显示了“激活/升级”](./media/servicenow-tutorial/tutorial_activate.png "激活插件")

    d. 选择“激活”  。

     ![“激活插件”对话框的屏幕截图，其中突出显示了“激活”](./media/servicenow-tutorial/tutorial_activate1.png "激活插件")

3. 在左侧窗格中，通过搜索栏搜索“多提供程序 SSO”部分，然后选择“属性”。  

    ![“多提供程序 SSO”部分的屏幕截图，其中突出显示了“多提供程序 SSO”和“属性”](./media/servicenow-tutorial/tutorial_servicenow_06.png "配置应用 URL")

4. 在“多提供程序 SSO 属性”  对话框中，执行以下步骤：

    ![“多提供程序 SSO 属性”对话框的屏幕截图](./media/servicenow-tutorial/ic7694981.png "配置应用 URL")

    * 对于“启用多提供程序 SSO”  ，选择“是”  。
  
    * 对于“启用将所有标识提供者中的用户自动导入用户表”，选择“是”。  

    * 对于“为多提供程序 SSO 集成启用调试日志记录”  ，选择“是”  。

    * 对于“...用户表上的字段”  ，输入“user_name”  。
  
    * 选择“保存”。 

6. 可以自动或手动配置 ServiceNow。 若要自动配置 ServiceNow，请执行以下步骤：

    1. 返回到 Azure 门户中的 ServiceNow 单一登录页  。

    1. 为 ServiceNow 提供一次单击配置服务。 若要启用此服务，请转到“ServiceNow 配置”部分，选择“配置 ServiceNow”以打开“配置登录”窗口。   

        ![“设置 ServiceNow”的屏幕截图，其中突出显示了“查看分步说明”](./media/servicenow-tutorial/tutorial_servicenow_configure.png)

    1. 在“配置登录”窗体中输入 ServiceNow 实例名称、管理员用户名和管理员密码。  选择“立即配置”  。 为使此操作生效，提供的管理员用户名必须具有在 ServiceNow 中分配的 security_admin  角色。 否则，若要手动配置 ServiceNow 以使用 Azure AD 作为 SAML 标识提供者，请选择“手动配置单一登录”  。 从“快速参考”部分中复制“注销 URL”、“SAML 实体 ID”和“SAML 单一登录服务 URL”  。

        ![配置登录窗体的屏幕截图，其中突出显示了“立即配置”](./media/servicenow-tutorial/configure.png "配置应用 URL")

    1. 以管理员身份登录到 ServiceNow 应用程序。

       * 在自动配置中，所有必要的设置都在 ServiceNow  端配置，但 X.509 证书  默认情况下未启用。 必须手动将其映射到 ServiceNow 中的标识提供者。 执行以下步骤:

         1. 在左侧窗格中，从搜索框中搜索“多提供程序 SSO”  部分，然后选择“标识提供者”  。

            ![“多提供程序 SSO”部分的屏幕截图，其中突出显示了标识提供者](./media/servicenow-tutorial/tutorial_servicenow_07.png "配置单一登录")

         1. 选择自动生成的标识提供者。

            ![标识提供者的屏幕截图，其中突出显示了自动生成的标识提供者](./media/servicenow-tutorial/tutorial_servicenow_08.png "配置单一登录")

         1.  在“标识提供者”  部分中，执行以下步骤：

             ![“标识提供者”部分的屏幕截图](./media/servicenow-tutorial/automatic_config.png "配置单一登录")

               * 对于“名称”  ，为你的配置输入一个名称（例如，“Microsoft Azure Federated single sign-on”  ）。

               * 请从文本框中删除已填充的“标识提供者的 SingleLogoutRequest”  值。

               * 复制“ServiceNow 主页”值，将其粘贴到 Azure 门户上“ServiceNow 基本 SAML 配置”部分中的“登录 URL”内。   

                  > [!NOTE]
                  > ServiceNow 实例主页是 **ServiceNow 租户 URL** 和 **/navpage.do** 的串联（例如：`https://fabrikam.service-now.com/navpage.do`）。

              * 复制“实体 ID/颁发者”值，将其粘贴到 Azure 门户中“ServiceNow 基本 SAML 配置”部分的“标识符”内。   

              * 确认将“名称 ID 策略”  设置为 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 值。 

         1. 向下滚动到“X.509 证书”  部分，选择“编辑”  。

             ![“X.509 证书”部分的屏幕截图，其中突出显示了“编辑”](./media/servicenow-tutorial/tutorial_servicenow_09.png "配置单一登录")

         1. 选择证书，然后选择右箭头图标以添加该证书

            ![“集合”屏幕截图，其中突出显示了“证书”和右箭头图标](./media/servicenow-tutorial/tutorial_servicenow_11.png "配置单一登录")

          1. 选择“保存”。 

          1. 在页面右上角选择“测试连接”。 

             ![页面屏幕截图，其中突出显示了“测试连接”](./media/servicenow-tutorial/tutorial_activate2.png "激活插件")

          1. 当系统提示你输入凭据时，请输入凭据。 然后，会看到以下页面。 会发生“SSO 注销测试结果”  错误。 忽略此错误，然后选择“激活”  。

             ![“测试结果”页的屏幕截图](./media/servicenow-tutorial/servicenowactivate.png "配置单一登录")
  
6. 若要手动配置 ServiceNow  ，请执行以下步骤：

    1. 以管理员身份登录到 ServiceNow 应用程序。

    1. 在左窗格中，选择“标识提供者”  。

        ![“多提供程序 SSO”的屏幕截图，其中突出显示了“标识提供者”](./media/servicenow-tutorial/tutorial_servicenow_07.png "配置单一登录")

    1. 在“标识提供者”  对话框中，选择“新建”。 

        ![“标识提供者”对话框的屏幕截图，其中突出显示了“新建”](./media/servicenow-tutorial/ic7694977.png "配置单一登录")

    1. 在“标识提供者”  对话框中，选择“SAML”。 

        ![“标识提供者”对话框的屏幕截图，其中突出显示了“SAML”](./media/servicenow-tutorial/ic7694978.png "配置单一登录")

    1. 在“导入标识提供者元数据”  中，执行以下步骤：

        ![“导入标识提供者元数据”的屏幕截图，其中突出显示了“URL”和“导入”](./media/servicenow-tutorial/idp.png "配置单一登录")

        1. 输入从 Azure 门户复制的“应用联合元数据 URL”  。

        1. 选择“导入”  。

    1. 它将读取 IdP 元数据 URL，并填充所有字段信息。

        ![“标识提供者”的屏幕截图](./media/servicenow-tutorial/ic7694982.png "配置单一登录")

        * 对于“名称”  ，为你的配置输入一个名称（例如，“Microsoft Azure Federated single sign-on”  ）。

        * 从文本框中删除已填充的“标识提供者的 SingleLogoutRequest”  值。

        * 复制“ServiceNow 主页”  值。 将其粘贴到 Azure 门户中“ServiceNow 基本 SAML 配置”部分的“登录 URL”内。  

            > [!NOTE]
            > ServiceNow 实例主页是 **ServiceNow 租户 URL** 和 **/navpage.do** 的串联（例如：`https://fabrikam.service-now.com/navpage.do`）。

        * 复制“实体 ID/颁发者”值  。 将其粘贴到 Azure 门户中“ServiceNow 基本 SAML 配置”部分的“标识符”内。  

        * 确认将“名称 ID 策略”  设置为 `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified` 值。

        * 选择“高级”。  在“用户字段”  中，输入“电子邮件”  或“user_name”  ，具体取决于哪个字段用于唯一标识 ServiceNow 部署中的用户。

            > [!NOTE]
            > 可以将 Azure AD 配置为发出 Azure AD 用户 ID （用户主体名称）或电子邮件地址作为 SAML 令牌中的唯一标识符。 若要执行此操作，请转到 Azure 门户的“ServiceNow”   > “特性”   > “单一登录”  部分，并将所需字段映射到“nameidentifier”  属性。 Azure AD 中为所选属性存储的值（例如用户主体名称）必须匹配 ServiceNow 中为输入字段存储的值（例如 user_name）。

        * 选择页面右上角的“测试连接”  。

        * 当系统提示你输入凭据时，请输入凭据。 然后，会看到以下页面。 会发生“SSO 注销测试结果”  错误。 忽略此错误，然后选择“激活”  。

          ![“测试结果”页的屏幕截图](./media/servicenow-tutorial/servicenowactivate.png "配置单一登录")

### <a name="create-servicenow-test-user"></a>创建 ServiceNow 测试用户

本部分要在 ServiceNow 中创建名为“B.Simon”的用户。 ServiceNow 支持在默认情况下启用的自动用户预配。

> [!NOTE]
> 如果需要手动创建用户，请联系 [ServiceNow 客户端支持团队](https://www.servicenow.com/support/contact-support.html)。

### <a name="configure-servicenow-express-sso"></a>配置 ServiceNow Express SSO

1. 以管理员身份登录到 ServiceNow Express 应用程序。

2. 在左窗格中，选择“单一登录”  。

    ![ServiceNow Express 应用程序的屏幕截图，其中突出显示了“单一登录”](./media/servicenow-tutorial/ic7694980ex.png "配置应用 URL")

3. 在“单一登录”  对话框中，选择右上角的配置图标，并设置以下属性：

    ![“单一登录”对话框的屏幕截图](./media/servicenow-tutorial/ic7694981ex.png "配置应用 URL")

    a. 将“启用多提供程序 SSO”  切换到右侧。

    b. 将“为多提供程序 SSO 集成启用调试日志记录”  切换到右侧。

    c. 在“...用户表上的字段”  中，输入“user_name”  。

4. 在“单一登录”  对话框中，选择“添加新证书”  。

    ![“单一登录”对话框的屏幕截图，其中突出显示了“添加新证书”](./media/servicenow-tutorial/ic7694973ex.png "配置单一登录")

5. 在“X.509 证书”  对话框中，执行以下步骤：

    ![“X.509 证书”对话框的屏幕截图](./media/servicenow-tutorial/ic7694975.png "配置单一登录")

    a. 对于“名称”  ，输入配置名称（例如：TestSAML2.0）  。

    b. 选择“活动”  。

    c. 对于“格式”  ，选择“PEM”  。

    d. 对于“类型”  ，选择“信任存储证书”  。

    e. 在记事本中打开从 Azure 门户下载的“Base64 编码证书”。 将其内容复制到剪贴板，然后将其粘贴到“PEM 证书”  文本框。

    f. 选择“更新” 

6. 在“单一登录”  对话框中，选择“添加新 IdP”  。

    ![“单一登录”对话框的屏幕截图，其中突出显示了“添加新 IdP”](./media/servicenow-tutorial/ic7694976ex.png "配置单一登录")

7. 在“添加新的标识提供者”  对话框的“配置标识提供者”  下，执行以下步骤：

    ![“添加新的标识提供者”对话框的屏幕截图](./media/servicenow-tutorial/ic7694982ex.png "配置单一登录")

    a. 对于“名称”  ，输入配置名称（例如：SAML 2.0）  。

    b. 对于“标识提供者 URL”，粘贴从 Azure 门户复制的标识提供者 ID 的值  。

    c. 对于“标识提供者的 AuthnRequest”，粘贴从 Azure 门户复制的身份验证请求 URL 的值  。

    d. 对于“标识提供者的 SingleLogoutRequest”，粘贴从 Azure 门户复制的注销 URL 的值  。

    e. 对于“标识提供者证书”  ，选择在上一步已创建的证书。

8. 选择“高级设置”  。 在“其他标识提供者属性”  下，执行以下步骤：

    ![“添加新标识提供者”对话框的屏幕截图，其中突出显示了“高级设置”](./media/servicenow-tutorial/ic7694983ex.png "配置单一登录")

    a. 对于“IDP 的 SingleLogoutRequest 协议绑定”  ，输入“urn:oasis:names:tc:SAML:2.0:bindings:HTTP-Redirect”  。

    b. 对于“NameID 策略”  ，输入“urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified”  。

    c. 对于“AuthnContextClassRef 方法”  ，输入 `http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password`。

    d. 对于“创建 AuthnContextClass”  ，请将其切换到“关”（未选定）。

9. 在“其他服务提供者属性”  下，执行以下步骤：

    ![“添加新标识提供者”对话框的屏幕截图，其中突出显示了各个属性](./media/servicenow-tutorial/ic7694984ex.png "配置单一登录")

    a. 对于“ServiceNow 主页”  ，输入 ServiceNow 实例主页的 URL。

    > [!NOTE]
    > ServiceNow 实例主页是 **ServiceNow 租户 URL** 和 **/navpage.do** 的串联（例如：`https://fabrikam.service-now.com/navpage.do`）。

    b. 对于“实体 ID/颁发者”  ，输入 ServiceNow 租户的 URL。

    c. 对于“受众 URI”  ，输入 ServiceNow 租户的 URL。

    d. 对于“时钟偏差”，请输入“60”   。

    e. 对于“用户字段”  ，输入“电子邮件”  或“user_name”  ，具体取决于哪个字段用于唯一标识 ServiceNow 部署中的用户。

    > [!NOTE]
    > 可以将 Azure AD 配置为发出 Azure AD 用户 ID （用户主体名称）或电子邮件地址作为 SAML 令牌中的唯一标识符。 若要执行此操作，请转到 Azure 门户的“ServiceNow”   > “特性”   > “单一登录”  部分，并将所需字段映射到“nameidentifier”  属性。 Azure AD 中为所选属性存储的值（例如用户主体名称）必须匹配 ServiceNow 中为输入字段存储的值（例如 user_name）。

    f. 选择“保存”。 

## <a name="test-sso"></a>测试 SSO

选择访问面板中的 ServiceNow 磁贴时，应当会自动登录到已为其设置了 SSO 的 ServiceNow。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="test-sso-for-servicenow-classic-mobile"></a>测试 ServiceNow Classic（移动版）的 SSO

1. 打开 ServiceNow Classic（移动版）  应用程序并执行以下步骤：

    a. 选择右下角的加号。

    ![ServiceNow Classic 应用程序的屏幕截图，其中突出显示了加号](./media/servicenow-tutorial/test03.png)

    b. 输入 ServiceNow 实例名称，然后选择“继续”。 

    ![“添加实例”页的屏幕截图，其中突出显示了“继续”](./media/servicenow-tutorial/test04.png)

    c. 在“登录”页面上执行以下步骤： 

    ![“登录”页的屏幕截图，其中突出显示了“使用外部登录”](./media/servicenow-tutorial/test01.png)

    *  输入“用户名”  ，如 B.simon@contoso.com。

    *  选择“使用外部登录”  。 系统随后会将你重定向到 Azure AD 页面以进行登录。
    
    *  输入凭据。 如果启用了任何第三方身份验证或任何其他安全功能，则用户必须相应地做出响应。 随后会显示应用程序“主页”  。

        ![应用程序主页的屏幕截图](./media/servicenow-tutorial/test02.png)

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [配置用户预配](servicenow-provisioning-tutorial.md)

- [通过 Azure AD 试用 ServiceNow](https://aad.portal.azure.com)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/protect-servicenow)

- [如何通过高级可见性和控制保护 ServiceNow](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
