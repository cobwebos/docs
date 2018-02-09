---
title: "教程：使用 Azure Active Directory 为 Pingboard 配置自动用户预配 | Microsoft Docs"
description: "了解如何将 Azure Active Directory 配置为自动将用户帐户预配到 Pingboard 和取消其预配。"
services: active-directory
documentationcenter: 
author: asmalser-msft
writer: asmalser-msft
manager: sakula
ms.assetid: 0b38ee73-168b-42cb-bd8b-9c5e5126d648
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.openlocfilehash: f74e890cf716cfd4bc7b41fcc4aa244969cafde5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2018
---
# <a name="tutorial-configure-pingboard-for-automatic-user-provisioning"></a>教程：为 Pingboard 配置自动用户预配

本教程旨在介绍将用户帐户从 Azure Active Directory 自动预配到 Pingboard 和取消其预配时所要执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定您已具有以下各项：

*   一个 Azure Active Directory 租户
*   Pingboard 租户[专业版帐户](https://pingboard.com/pricing) 
*   Pingboard 中具有管理员权限的用户帐户 

> [!NOTE] 
> Azure Active Directory 预配集成依赖于帐户可用的 Pingboard API (`https://your_domain.pingboard.com/scim/v2`)。

## <a name="assigning-users-to-pingboard"></a>将用户分配到 Pingboard

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用程序的访问权限。 在自动用户帐户预配的上下文中，只同步已分配到 Azure Active Directory 中的应用程序的用户。 

在配置和启用预配服务前，需确定 Azure Active Directory 中的哪些用户表示需要访问 Pingboard 应用的用户。 确定后，可以按照此处的说明将这些用户分配到 Pingboard 应用：

[向企业应用分配用户](active-directory-coreapps-assign-user-azure-portal.md)

### <a name="important-tips-for-assigning-users-to-pingboard"></a>将用户分配到 Pingboard 的重要提示

*   建议将单个 Azure Active Directory 用户分配到 Pingboard 以测试预配配置。 其他用户可以稍后分配。

## <a name="configuring-user-provisioning-to-pingboard"></a>向 Pingboard 配置用户预配 

本部分介绍了如何将 Azure Active Directory 连接到 Pingboard 用户帐户预配 API 并配置预配服务，以便在 Pingboard 中根据 Azure Active Directory 中的用户分配创建、更新和禁用分配的用户帐户。

> [!TIP]
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Pingboard 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-automatic-user-account-provisioning-to-pingboard-in-azure-active-directory"></a>若要在 Azure Active Directory 中为 Pingboard 配置自动用户帐户预配，请执行以下操作：

1)  在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

2) 如果已为 Pingboard 配置单一登录，请使用搜索字段搜索 Pingboard 实例。 否则，请选择“添加”，然后在应用程序库中搜索“Pingboard”。 从搜索结果中选择“Pingboard”，并将其添加到应用程序列表。

3)  选择 Pingboard 实例，并选择“预配”选项卡。

4)  将“预配模式”设置为“自动”。

![Pingboard 预配](./media/active-directory-saas-pingboard-provisioning-tutorial/pingboardazureprovisioning.png)
    
5) 在“管理员凭据”部分中，执行以下步骤：

* 在“租户 URL”文本框中，输入 `https://your_domain.pingboard.com/scim/v2` 并将 your_domain 替换为实际的域
* 使用管理员帐户登录到 [Pingboard](https://pingboard.com/)。
* 单击“外接程序”>“集成”>“Azure Active Directory”。
* 单击“配置”选项卡并选择“从 Azure 启用用户预配”。
* 复制“OAuth 持有者令牌”字段值，并将其输入到“机密令牌”文本框中。

6) 在 Azure 门户中，单击“测试连接”，确保 Azure Active Directory 可以连接到 Pingboard 应用。 如果连接失败，请确保 Pingboard 帐户具有管理员权限，并重试“测试连接”步骤。

7) 在“通知电子邮件”字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中下面的复选框。

8) 单击“ **保存**”。 

9) 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Pingboard”。

10) 在“属性映射”部分中，查看要从 Azure Active Directory 同步到 Pingboard 的用户属性。 选为“匹配”属性的特性用于匹配 Pingboard 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。 有关详细信息，请参阅[自定义用户预配属性映射](active-directory-saas-customizing-attribute-mappings.md)。

11) 若要为 Pingboard 启用 Azure Active Directory 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

12) 单击“保存”，开始对分配到 Pingboard 的用户执行初始同步。

初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 20 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动报告的链接，这些报告描述了预配服务对 Pingboard 应用执行的所有操作。

若要详细了解如何读取 Azure Active Directory 预配日志，请参阅[有关自动用户帐户预配的报告](active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](active-directory-enterprise-apps-manage-provisioning.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](active-directory-appssoaccess-whatis.md)
* [配置单一登录](active-directory-saas-pingboard-tutorial.md)
