---
title: 教程：使用 Azure Active Directory 为 Netsuite 配置自动用户预配 | Microsoft Docs
description: 了解如何在 Azure Active Directory 和 Netsuite 之间配置单一登录。
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 8a6d3994-ee33-4a6f-b0a2-9d0389467f16
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.openlocfilehash: 3dac2b1c90f6555abc71a52d75f8d58958d978c7
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2018
ms.locfileid: "39449462"
---
# <a name="tutorial-configuring-netsuite-for-automatic-user-provisioning"></a>教程：为 Netsuite 配置自动用户预配

本教程旨在介绍为从 Azure AD 自动将用户帐户预配到 Netsuite 以及取消其预配而需要在 Netsuite 和 Azure 中执行的步骤。

## <a name="prerequisites"></a>先决条件

在本教程中概述的方案假定已有以下各项：

*   Azure Active Directory 租户。
*   已启用 Netsuite 单一登录的订阅。
*   具有团队管理员权限的 Netsuite 用户帐户。

## <a name="assigning-users-to-netsuite"></a>将用户分配到 Netsuite

Azure Active Directory 使用称为“分配”的概念来确定哪些用户应收到对所选应用的访问权限。 在自动用户帐户预配的上下文中，只同步已“分配”到 Azure AD 中应用程序的用户和组。

在配置和启用预配服务前，需确定 Azure AD 中的哪些用户和/或组表示需要访问 Netsuite 应用的用户。 确定后，可按照此处的说明将这些用户分配到 Netsuite 应用：

[向企业应用分配用户或组](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-netsuite"></a>将用户分配到 Netsuite 的重要提示

*   建议将单个 Azure AD 用户分配到 Netsuite 以测试预配配置。 其他用户和/或组可以稍后分配。

*   将用户分配到 Netsuite 时，必须选择有效的用户角色。 “默认访问权限”角色不可用于预配。

## <a name="enable-user-provisioning"></a>启用用户预配

本部分将指导完成以下操作：将 Azure AD 连接到 Netsuite 的用户帐户预配 API，配置预配服务以便基于 Azure AD 中的用户和组分配在 Netsuite 中创建、更新和禁用所分配的用户帐户。

> [!TIP] 
> 还可选择按照 [Azure 门户](https://portal.azure.com)中提供的说明为 Netsuite 启用基于 SAML 的单一登录。 可以独立于自动预配配置单一登录，尽管这两个功能互相补充。

### <a name="to-configure-user-account-provisioning"></a>配置用户帐户预配：

本部分的目的是概述如何对 Netsuite 启用 Active Directory 用户帐户的用户预配。

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到“Azure Active Directory”>“企业应用”>“所有应用程序”部分。

1. 如果已为 Netsuite 配置单一登录，请使用搜索字段搜索 Netsuite 实例。 否则，请选择“添加”并在应用程序库中搜索“Netsuite”。 从搜索结果中选择 Netsuite，并将其添加到应用程序列表。

1. 选择 Netsuite 实例，并选择“预配”选项卡。

1. 将“预配模式”设置为“自动”。 

    ![预配](./media/netsuite-provisioning-tutorial/provisioning.png)

1. 在“管理员凭据”部分中，提供以下配置设置：
   
    a. 在“管理员用户名”文本框中，键入在 Netsuite.com 中已分配“系统管理员”配置文件的 Netsuite 帐户名称。
   
    b. 在“管理员密码”文本框中，键入此帐户的密码。
      
1. 在 Azure 门户中，单击“测试连接”，确保 Azure AD 可以连接到 Netsuite 应用。

1. 在“通知电子邮件”字段中输入应收到预配错误通知的用户或组的电子邮件地址，并选中复选框。

1. 单击“保存”。

1. 在“映射”部分下，选择“将 Azure Active Directory 用户同步到 Netsuite”。

1. 在“属性映射”部分，查看从 Azure AD 同步到 Netsuite 的用户属性。 请注意，选为“匹配”属性的属性将用于匹配 Netsuite 中的用户帐户以执行更新操作。 选择“保存”按钮以提交任何更改。

1. 要为 Netsuite 启用 Azure AD 预配服务，请在“设置”部分中将“预配状态”更改为“启用”

1. 单击“保存”。

这会开始将“用户和组”部分中分配的任何用户和/或组初始同步到 Netsuite。 请注意，初始同步执行的时间比后续同步长，只要服务正在运行，大约每隔 40 分钟就会进行一次同步。 可使用“同步详细信息”部分监视进度并跟踪指向预配活动日志的链接，这些日志描述了预配服务对 Netsuite 应用执行的所有操作。

若要详细了解如何读取 Azure AD 预配日志，请参阅[有关自动用户帐户预配的报告](../active-directory-saas-provisioning-reporting.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](tutorial-list.md)
* [什么是使用 Azure Active Directory 的应用程序访问和单一登录？](../manage-apps/what-is-single-sign-on.md)
* [配置单一登录](netsuite-tutorial.md)