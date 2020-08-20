---
title: 教程：Azure Active Directory 与 iProva 的单一登录 (SSO) 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 与 iProva 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 187ec5c42c733837689de95c8111b557519f8b7b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552829"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-iprova"></a>教程：Azure Active Directory 与 iProva 的单一登录 (SSO) 集成

本教程介绍如何将 iProva 与 Azure Active Directory (Azure AD) 集成。 将 iProva 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 iProva。
* 让用户使用其 Azure AD 帐户自动登录到 iProva。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了单一登录 (SSO) 的 iProva 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。

* iProva 支持 SP 发起的 SSO 

* 配置 iProva 后，可以强制实施会话控制，从而实时保护组织的敏感数据免于外泄和渗透。 会话控制从条件访问扩展而来。 [了解如何通过 Microsoft Cloud App Security 强制实施会话控制](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)。

## <a name="adding-iprova-from-the-gallery"></a>从库中添加 iProva

若要配置 iProva 与 Azure AD 的集成，需要从库中将 iProva 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 导航到“企业应用程序”，选择“所有应用程序”   。
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分中，在搜索框中键入“iProva”   。
1. 在结果面板中选择“iProva”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-single-sign-on-for-iprova"></a>配置并测试 iProva 的 Azure AD 单一登录

使用名为 **B.Simon** 的测试用户配置并测试 iProva 的 Azure AD SSO。 若要使 SSO 正常工作，需要在 Azure AD 用户与 iProva 中的相关用户之间建立链接关系。

若要配置并测试 iProva 的 Azure AD SSO，请完成以下构建基块：

1. **[从 iProva 中检索配置信息](#retrieve-configuration-information-from-iprova)** - 为后续步骤做准备。
1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够使用此功能。
1. **[创建 Azure AD 测试用户](#create-an-azure-ad-test-user)** - 使用 B. Simon 测试 Azure AD 单一登录。
1. **[分配 Azure AD 测试用户](#assign-the-azure-ad-test-user)** - 使 B. Simon 能够使用 Azure AD 单一登录。
1. **[创建 iProva 测试用户](#create-iprova-test-user)** - 在 iProva 中创建 Britta Simon 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
1. **[配置 iProva SSO](#configure-iprova-sso)** - 在应用程序端配置单一登录设置。
1. **[测试 SSO](#test-sso)** - 验证配置是否正常工作。

## <a name="retrieve-configuration-information-from-iprova"></a>从 iProva 检索配置信息

在本部分，我们将从 iProva 检索信息以配置 Azure AD 单一登录。

1. 打开 Web 浏览器并使用以下 URL 模式转到 iProva 中的“SAML2 信息”页： 

    ```https
    https://SUBDOMAIN.iprova.nl/saml2info
    https://SUBDOMAIN.iprova.be/saml2info
    ```

    ![查看 iProva SAML2 信息页](media/iprova-tutorial/iprova-saml2-info.png)

1. 在另一个浏览器选项卡中继续执行后续步骤时，保持浏览器选项卡处于打开状态。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)中，在 **iProva** 应用程序集成页上，找到“管理”部分并选择“单一登录”   。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本 SAML 配置”  部分中，按照以下步骤操作：

    a. 在“登录 URL”框中填写“iProva SAML2 信息”页上的标签“登录 URL”后面显示的值。    此页仍在另一浏览器标签页中打开。

    b. 在“标识符”框中填写“iProva SAML2 信息”页上的标签“EntityID”后面显示的值。    此页仍在另一浏览器标签页中打开。

    c. 在“回复 URL”框中填写“iProva SAML2 信息”页上的标签“回复 URL”后面显示的值。    此页仍在另一浏览器标签页中打开。

1. iProva 应用程序需要特定格式的 SAML 断言，这要求向 SAML 令牌属性配置添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![image](common/default-attributes.png)

1. 除了上述属性，iProva 应用程序还要求在 SAML 响应中传递回更多的属性，如下所示。 这些属性也是预先填充的，但可以根据要求查看它们。

    | 名称 | 源属性| 命名空间  |
    | ---------------| -------- | -----|
    | `samaccountname` | `user.onpremisessamaccountname`| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. 在“使用 SAML 设置单一登录”  页的“SAML 签名证书”  部分中，单击“复制”按钮，以复制“应用联合元数据 URL”  ，并将它保存在计算机上。

    ![证书下载链接](common/copy-metadataurl.png)

## <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分，我们将在 Azure 门户中创建名为 B.Simon 的测试用户。

1. 在 Azure 门户的左侧窗格中，依次选择“Azure Active Directory”、“用户”和“所有用户”    。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”字段中输入 username@companydomain.extension  。 例如，`B.Simon@contoso.com` 。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 单击“创建”。 

## <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 B.Simon 访问 iProva 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“iProva”。 
1. 在应用的概述页中，找到“管理”部分，选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，从“用户”列表中选择“B.Simon”，然后单击屏幕底部的“选择”按钮。   
1. 如果在 SAML 断言中需要任何角色值，请在“选择角色”对话框的列表中为用户选择合适的角色，然后单击屏幕底部的“选择”按钮。  
1. 在“添加分配”对话框中，单击“分配”按钮。  

## <a name="create-iprova-test-user"></a>创建 iProva 测试用户

1. 使用**管理员**帐户登录到 iProva。

2. 打开“转到”菜单  。

3. 选择“应用程序管理”。 

4. 选择“用户和用户组”面板中的“用户”   。

5. 选择 **添加** 。

6. 在“用户名”文本框中输入用户的用户名（如 `B.Simon@contoso.com`）  。

7. 在“全名”框中输入完整名称，例如 **B.Simon**。 

8. 选择“无密码(使用单一登录)”选项  。

9. 在“电子邮件地址”框中，输入用户的电子邮件地址（例如 `B.Simon@contoso.com`）  。

10. 向下滚动到页面底部，选择“完成”  。

## <a name="configure-iprova-sso"></a>配置 iProva SSO

1. 使用**管理员**帐户登录到 iProva。

2. 打开“转到”菜单  。

3. 选择“应用程序管理”。 

4. 在“系统设置”面板中选择“常规”   。

5. 选择“编辑”  。

6. 向下滚动到“访问控制”  。

    ![iProva 访问控制设置](media/iprova-tutorial/iprova-accesscontrol.png)

7. 查找设置“用户使用其网络帐户自动登录”，并将其更改为“是，通过 SAML 进行身份验证”   。 此时会显示其他选项。

8. 选择“设置”。 

9. 选择“**下一页**”。

10. iProva 会询问是要从 URL 下载联合数据，还是从文件上传联合数据。 选择“从 URL”选项  。

    ![下载 Azure AD 元数据](media/iprova-tutorial/iprova-download-metadata.png)

11. 粘贴在“配置 Azure AD 单一登录”部分的最后一步中保存的元数据 URL。

12. 选择箭头按钮，从 Azure AD 下载元数据。

13. 下载完成后，将显示确认消息“已下载有效的联合数据文件”  。

14. 选择“**下一页**”。

15. 暂时跳过“测试登录”选项，并选择“下一步”   。

16. 在“声明使用”下拉框中，选择“windowsaccountname”   。

17. 选择“完成”  。

18. 现在返回“编辑常规设置”屏幕  。 向下滚动到页面底部，选择“确定”以保存配置  。

## <a name="test-sso"></a>测试 SSO

在本部分中，使用访问面板测试 Azure AD 单一登录配置。

单击访问面板中的 iProva 磁贴时，应会自动登录到为其设置了 SSO 的 iProva。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [尝试通过 Azure AD 使用 iProva SSO](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security 中的会话控制是什么？](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [如何通过高级可见性和控制保护 iProva](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)