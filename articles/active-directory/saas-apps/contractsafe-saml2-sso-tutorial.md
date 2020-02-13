---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 ContractSafe Saml2 SSO 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 ContractSafe Saml2 SSO 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9d8c9eba-6a90-4c8f-b387-a6ead4af00af
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ab2dc71f39164988e0d229fc994548a00447986
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77185616"
---
# <a name="tutorial-integrate-azure-active-directory-single-sign-on-sso-with-contractsafe-saml2-sso"></a>教程：Azure Active Directory 与 ContractSafe Saml2 SSO 的单一登录 (SSO) 集成

本教程介绍如何将 ContractSafe Saml2 SSO 与 Azure Active Directory (Azure AD) 集成。 将 ContractSafe Saml2 SSO 与 Azure AD 集成后，可以：

* 在 Azure AD 中控制谁有权访问 ContractSafe Saml2 SSO。
* 让用户使用其 Azure AD 帐户自动登录到 ContractSafe Saml2 SSO。
* 在一个中心位置（Azure 门户）管理帐户。

若要了解服务型软件 (SaaS) 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>必备条件

要开始，需要：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 启用了 SSO 的 ContractSafe Saml2 SSO 订阅。

## <a name="scenario-description"></a>方案描述

本教程在测试环境中配置并测试 Azure AD SSO。 ContractSafe Saml2 SSO 支持 **IDP** 发起的 SSO。

## <a name="add-contractsafe-saml2-sso-from-the-gallery"></a>从库中添加 ContractSafe Saml2 SSO

若要配置 ContractSafe Saml2 SSO 与 Azure AD 的集成，需要从库中将 ContractSafe Saml2 SSO 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务  。
1. 转到“企业应用程序”，并选择“所有应用程序”。  
1. 若要添加新的应用程序，请选择“新建应用程序”  。
1. 在“从库中添加”部分的搜索框中，键入“ContractSafe Saml2 SSO“   。
1. 在结果面板中选择“ContractSafe Saml2 SSO”，然后添加该应用  。 在该应用添加到租户时等待几秒钟。

## <a name="configure-and-test-azure-ad-sso-for-contractsafe-saml2-sso"></a>配置并测试 ContractSafe Saml2 SSO 的 Azure AD SSO

使用名为 **B.Simon** 的测试用户配置并测试 ContractSafe Saml2 SSO 的 Azure AD SSO。 若要运行 SSO，需要在 Azure AD 用户与 ContractSafe Saml2 SSO 相关用户之间建立关联关系。

若要配置并测试 ContractSafe Saml2 SSO 的 Azure AD SSO，请完成以下构建基块：

1. [配置 Azure AD SSO](#configure-azure-ad-sso)，使用户能够使用此功能。
   * [创建 Azure AD 测试用户](#create-an-azure-ad-test-user) - 使用 **B.Simon** 帐户测试 Azure AD SSO。
   * [分配 Azure AD 测试用户](#assign-the-azure-ad-test-user) - 使 **B.Simon** 能够使用 Azure AD SSO。

1. [配置 ContractSafe Saml2 SSO](#configure-contractsafe-saml2-sso) - 在应用程序端配置 SSO 设置。
   * [创建 ContractSafe Saml2 SSO 测试用户](#create-a-contractsafe-saml2-sso-test-user) - 在 ContractSafe Saml2 SSO 中创建 **B.Simon** 的对应用户，并将其链接到该用户的 Azure AD 表示形式。
2. [测试 SSO](#test-sso)，验证配置是否正常工作。

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO：

1. 在 [Azure 门户](https://portal.azure.com/)的“ContractSafe Saml2 SSO”应用程序集成页上，找到“管理”部分，选择“单一登录”    。
1. 在“选择单一登录方法”页上选择“SAML”   。
1. 在“使用 SAML 设置单一登录”页上，选择“基本 SAML 配置”对应的编辑（笔）图标以编辑设置   。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“使用 SAML 设置单一登录”  页上，在对应的字段中输入以下值：

    a. 在“标识符”  文本框中，使用以下格式输入 URL：`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    b. 在“回复 URL”  文本框中，使用以下格式输入 URL：`https://app.contractsafe.com/saml2_auth/<UNIQUEID>/acs/`

    > [!NOTE]
    > 这些不是实际值。 请使用实际标识符和回复 URL 更新这些值。 请联系 [ContractSafe Saml2 SSO 客户端支持团队](mailto:support@contractsafe.com)来获取这些值。 还可参考 Azure 门户中的“基本 SAML 配置”部分显示的格式。 

1. ContractSafe Saml2 SSO 需要特定格式的 SAML 断言，因此，你需要在 SAML 令牌属性配置中添加自定义属性映射。 以下屏幕截图显示了默认属性的列表。

    ![常用默认属性](common/default-attributes.png)

1. 除了默认属性以外，ContractSafe Saml2 SSO 应用程序还要求在 SAML 响应中传回其他几个属性。 这些属性是预先填充的，但你可以根据需要查看它们。 以下列表显示了其他属性。

    | 名称 | 源属性|
    | ---------------| --------------- |
    | emailname | user.userprincipalname |
    | 电子邮件 | user.onpremisesuserprincipalname |

1. 在“设置 SAML 单一登录”页的“SAML 签名证书”部分中，找到“联合元数据 XML”    。 选择“下载”以下载证书，并将其保存在计算机上。 

    ![证书下载链接](common/metadataxml.png)

1. 在“设置 ContractSafe Saml2 SSO”部分中，根据你的需要复制相应的 URL  。

    ![复制配置 URL](common/copy-configuration-urls.png)

## <a name="create-an-azure-ad-test-user"></a>创建 Azure AD 测试用户

在本部分中，将在 Azure 门户中创建名为 **B.Simon** 的测试用户。

1. 在 Azure 门户的左窗格中，选择“Azure Active Directory”。  选择“用户”，然后选择“所有用户”   。
1. 选择屏幕顶部的“新建用户”  。
1. 在“用户”属性中执行以下步骤  ：
   1. 在“名称”  字段中，输入 `B.Simon`。  
   1. 在“用户名”  字段中，以 `username@companydomain.extension` 格式输入电子邮件地址。 示例为 `B.Simon@contoso.com`。
   1. 选中“显示密码”复选框，然后记下“密码”框中显示的值。  
   1. 选择“创建”  。

## <a name="assign-the-azure-ad-test-user"></a>分配 Azure AD 测试用户

在本部分中，将通过授予 **B.Simon** 访问 ContractSafe Saml2 SSO 的权限，允许其使用 Azure 单一登录。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。  
1. 在应用程序列表中，选择“ContractSafe Saml2 SSO”  。
1. 在应用的概述页中，找到“管理”部分，然后选择“用户和组”   。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。   

   ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框的“用户”列表中，选择“B.Simon”。    然后选择屏幕底部的“选择”按钮。 
1. 如果希望在 SAML 断言中使用任何角色值，请在“选择角色”对话框中，从列表中为用户选择相应的角色。  然后，选择屏幕底部的“选择”按钮。 
1. 在“添加分配”对话框中，选择“分配”按钮。  

## <a name="configure-contractsafe-saml2-sso"></a>配置 ContractSafe Saml2 SSO

若要在 **ContractSafe Saml2 SSO** 端配置单一登录，需要将下载的“联合元数据 XML”以及从 Azure 门户复制的相应 URL 发送给 [ContractSafe Saml2 SSO 支持团队](mailto:support@contractsafe.com)。  该团队负责在两端正确设置 SAML SSO 连接。

## <a name="create-a-contractsafe-saml2-sso-test-user"></a>创建 ContractSafe Saml2 SSO 测试用户

在 ContractSafe Saml2 SSO 中创建名为 B.Simon 的用户。 与 [ContractSafe Saml2 SSO 支持团队](mailto:support@contractsafe.com)合作，将用户添加到 ContractSafe Saml2 SSO 平台。 使用 SSO 前，必须先创建并激活用户。

## <a name="test-sso"></a>测试 SSO

使用访问面板测试 Azure AD SSO 配置。 选择访问面板中的 ContractSafe Saml2 SSO 磁贴时，应会自动登录到为其设置了 SSO 的 ContractSafe Saml2 SSO。 有关访问面板的详细信息，请参阅 [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)（访问面板简介）。

## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory 的应用程序访问与单一登录是什么？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 ContractSafe Saml2 SSO](https://aad.portal.azure.com/)
