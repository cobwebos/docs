---
title: 教程：Azure Active Directory 单一登录 (SSO) 与 Maxient Conduct Manager Software 集成 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Maxient Conduct Manager Software 之间配置单一登录。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2019
ms.author: jeedes
ms.openlocfilehash: dd6872fd8dca3f29b61c6f1dffb5f219abac5cb8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88518882"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-maxient-conduct-manager-software"></a>教程：Azure Active Directory 单一登录 (SSO) 与 Maxient Conduct Manager Software 集成

本教程介绍如何将 Maxient Conduct Manager Software 与 Azure Active Directory (Azure AD) 集成。 将 Maxient Conduct Manager Software 与 Azure AD 集成后，可以：

* 利用 Azure AD 向 Maxient Conduct Manager Software 验证用户身份
* 让用户使用其 Azure AD 帐户自动登录到 Maxient Conduct Manager Software。


若要了解有关 SaaS 应用与 Azure AD 集成的详细信息，请参阅 [Azure Active Directory 的应用程序访问与单一登录是什么](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)。

## <a name="prerequisites"></a>先决条件

若要开始操作，需备齐以下项目：

* 一个 Azure AD 订阅。 如果没有订阅，可以获取一个[免费帐户](https://azure.microsoft.com/free/)。
* 已启用 Maxient Conduct Manager Software 单一登录 (SSO) 的订阅。

## <a name="scenario-description"></a>方案描述

在本教程中，你将配置 Azure AD 以用于 Maxient Conduct Manager Software。


* Maxient Conduct Manager Software 支持 SP 和 IDP 发起的 SSO

> [!NOTE]
> 此应用程序的标识符是一个固定字符串值，因此只能在一个租户中配置一个实例。

## <a name="adding-maxient-conduct-manager-software-from-the-gallery"></a>从库中添加 Maxient Conduct Manager Software

若要配置 Maxient Conduct Manager Software 与 Azure AD 的集成，需要从库中将 Maxient Conduct Manager Software 添加到托管 SaaS 应用列表。

1. 使用工作或学校帐户或个人 Microsoft 帐户登录到 [Azure 门户](https://portal.azure.com)。
1. 在左侧导航窗格中，选择“Azure Active Directory”服务。
1. 导航到“企业应用程序”，选择“所有应用程序” 。
1. 若要添加新的应用程序，请选择“新建应用程序”。
1. 在“从库中添加”部分的搜索框中，键入“Maxient Conduct Manager Software” 。
1. 在结果面板中选择“Maxient Conduct Manager Software”，然后添加该应用。 在该应用添加到租户时等待几秒钟。


## <a name="configure-and-test-azure-ad-single-sign-on-for-maxient-conduct-manager-software"></a>配置并测试 Maxient Conduct Manager Software 的 Azure AD 单一登录

配置并测试 Maxient Conduct Manager Software 的 Azure AD SSO。 若要使 SSO 有效，需要在 Azure AD 与 Maxient Conduct Manager Software 之间建立连接。

若要配置并测试 Maxient Conduct Manager Software 的 Azure AD SSO，请完成以下构建基块：

1. **[配置 Azure AD SSO](#configure-azure-ad-sso)** - 使用户能够通过 Maxient Conduct Manager Software 进行身份验证
    1. **[指定所有用户都使用 Maxient](#assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software)** - 让机构中的每个人都能够进行身份验证。
1. **[使用 Maxient 测试 Azure AD 设置](#test-with-maxient)** - 验证配置是否有效以及是否发布了正确的属性

## <a name="configure-azure-ad-sso"></a>配置 Azure AD SSO

按照下列步骤在 Azure 门户中启用 Azure AD SSO。

1. 在 [Azure 门户](https://portal.azure.com/)的“Maxient Conduct Manager Software”应用程序集成页上，找到“管理”部分，选择“单一登录”  。
1. 在“选择单一登录方法”页上选择“SAML” 。
1. 在“使用 SAML 设置单一登录”页上，单击“基本 SAML 配置”的编辑/笔形图标以编辑设置 。

   ![编辑基本 SAML 配置](common/edit-urls.png)

1. 在“基本  **SAML** 配置”部分，应用程序已预配置为采用“ **IDP**”发起模式，并且已在 Azure 中预先填充了所需的 URL。   用户需要单击“ **保存**”按钮来保存配置。 

1. 如果要在 SP 发起的模式下配置应用程序，请单击“设置其他 URL”，并执行以下步骤：

    在“登录 URL”文本框中，使用以下模式键入 URL：`https://cm.maxient.com/<SCHOOLCODE>`

    > [!NOTE]
    > 此值不是真实值。 请使用实际登录 URL 更新此值。 请与 Maxient 实施/支持代表协作，以获取此值。

1. 在“使用 SAML 设置单一登录”页的“SAML 签名证书”部分中，单击“复制”按钮，以复制“应用联合元数据 URL”，并将它保存在计算机上。  需要向 Maxient 实施/支持代表提供此 URL。

    ![证书下载链接](common/copy-metadataurl.png)

### <a name="assign-all-users-to-be-able-to-authenticate-for-the-maxient-conduct-manager-software"></a>指定所有用户都能够向 Maxient Conduct Manager Software 验证身份

在本部分中，你将向所有帐户授予访问权限，以便其使用 Azure 系统向 Maxient Conduct Manager Software 验证身份。  请务必注意，必须执行此步骤，否则 Maxient 无法正常运行。  Maxient 利用 Azure AD 系统来验证用户身份。 为用户授权时，将根据他们要执行的特定功能在 Maxient 系统中进行授权。 Maxient 不会使用你目录中的属性来做出这些决定。

1. 在 Azure 门户中，依次选择“企业应用程序”、“所有应用程序”。 
1. 在应用程序列表中，选择“Maxient Conduct Manager Software”。
1. 在应用的概述页中，找到“管理”部分，选择“用户和组” 。

   ![“用户和组”链接](common/users-groups-blade.png)

1. 选择“添加用户”，然后在“添加分配”对话框中选择“用户和组”。  

    ![“添加用户”链接](common/add-assign-user.png)

1. 在“用户和组”对话框中，选择所有用户（或适当的组），并指定他们能够向 Maxient 验证身份。

## <a name="test-with-maxient"></a>使用 Maxient 进行测试 

如果尚未向 Maxient 实施/支持代表开具支持工单，请向 [support@maxient.com](mailto:support@maxient.com) 发送主题为“基于校园的身份验证/Azure 设置 - \<\<School Name\>\>”的电子邮件。 在电子邮件正文中，请提供应用联合元数据 URL。 Maxient 工作人员将通过测试链接进行响应，以验证是否发布了正确的属性。  
    
## <a name="additional-resources"></a>其他资源

- [有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [什么是 Azure Active Directory 中的条件访问？](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [通过 Azure AD 试用 Maxient Conduct Manager Software](https://aad.portal.azure.com/)

