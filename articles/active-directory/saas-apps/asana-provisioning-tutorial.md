---
title: 教程：使用 Azure Active Directory 为 Asana 配置自动用户预配 | Microsoft Docs
description: 了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Asana 和取消其预配。
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: 9ab2b22840d162fc0194151c5eec02675aad92dc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427646"
---
# <a name="tutorial-configure-asana-for-automatic-user-provisioning"></a>教程：为 Asana 配置自动用户预配

本教程的目的是说明从 Azure Active Directory (Azure AD) 自动将用户帐户预配到 Asana 和取消其预配所需在 Asana 和 Azure AD 中执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

*   Azure AD 租户
*   启用了[企业](https://www.asana.com/pricing)计划或更佳计划的 Asana 租户 
*   在 Asana 中具有管理员权限的用户帐户 

> [!NOTE] 
> Azure AD 预配集成依赖于可用于 Asana 的 [Asana API](https://asana.com/developers/api-reference/users)。

## <a name="assign-users-to-asana"></a>将用户分配到 Asana

Azure AD 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已分配到 Azure AD 中的应用程序的用户和组。 

配置和启用预配服务前，需确定 Azure AD 中的哪些用户或组需访问 Google Apps 应用。 确定后，可以按照此处的说明将这些用户分配到 Asana 应用：

[向企业应用分配用户](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-asana"></a>将用户分配到 Asana 的重要提示

建议将单个 Azure AD 用户分配到 Asana 以测试预配配置。 其他用户可以稍后分配。

## <a name="configure-user-provisioning-to-asana"></a>为 Asana 配置用户预配 

本部分将指导你完成将 Azure AD 连接到 Workplace by Facebook 的用户帐户预配 API。 还可根据 Azure AD 中的用户和组分配，配置预配服务，以创建、更新和禁用 Google Apps 中分配的用户帐户。

> [!TIP]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Asana 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-account-provisioning-to-asana-in-azure-ad"></a>若要在 Azure AD 中为 Asana 配置自动用户帐户预配，请执行以下操作：

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory” > “企业应用” > “所有应用程序”部分。

1. 如果已为 Asana 配置单一登录，请使用搜索字段搜索 Asana 实例。 否则，请选择“添加”并在应用程序库中搜索“Asana”。 从搜索结果中选择“Asana”，并将其添加到应用程序列表。

1. 选择 Asana 实例，然后选择“预配”选项卡。

1. 将“预配模式”设置为“自动”。

    ![Asana 预配](./media/asana-provisioning-tutorial/asanaazureprovisioning.png)

1. 在“管理员凭据”部分中，按照以下说明生成令牌并将其输入到“机密令牌”文本框中：

    a. 使用管理员帐户登录到 [Asana](https://app.asana.com)。

    b. 单击顶部栏中的个人资料照片，然后选择当前的组织名称设置。

    c. 导航到“服务帐户”选项卡。

    d. 单击“添加服务帐户”。

    e. 根据需要更新“姓名”、“关于”和个人资料照片。 复制中的令牌**令牌**，并选择在**保存更改**。

1. 在 Azure 门户中，选择“测试连接”确保 Azure AD 可连接到 Asana 应用。 如果连接失败，请确保 Asana 帐户具有管理员权限，并重试“测试连接”步骤。

1. 在“通知电子邮件”字段中输入应接收预配错误通知的人员或组的电子邮件地址。 然后选中复选框。

1. 选择“保存”。 

1. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Asana”。

1. 在“属性映射”部分中，查看将从 Azure AD 同步到 Asana 的用户属性。 选为“匹配”属性的特性用于匹配 Asana 中的用户帐户以执行更新操作。 选择“保存”，提交所有更改。 有关详细信息，请参阅[自定义用户预配属性映射](../active-directory-saas-customizing-attribute-mappings.md)。

1. 若要为 Asana 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”。

1. 选择“保存”。 

这会对“用户”部分中分配到 Asana 的任何用户启动初始同步。 初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动日志的链接。 这些审核日志描述了预配服务对 Asana 应用执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)
* [配置单一登录](asana-tutorial.md)
