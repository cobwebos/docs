---
title: Azure Active Directory 中针对企业应用的用户预配管理 | Microsoft 文档
description: 了解如何通过 Azure Active Directory 管理企业应用的用户帐户预配
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/26/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 926db432e413c3fc1360f68598e952830d212cb9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "36331459"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>在 Azure 门户中管理企业应用的用户帐户预配
本文介绍如何使用 [Azure 门户](https://portal.azure.com)为支持用户帐户自动预配和取消预配功能的应用程序（尤其是已从 [Azure Active Directory 应用程序库](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)的“特色”类别添加的应用程序）管理该功能。 若要了解有关用户帐户自动预配及其工作原理的详细信息，请参阅 [Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置](../active-directory-saas-app-provisioning.md)。

## <a name="finding-your-apps-in-the-portal"></a>在门户中查找应用
所有由目录管理员在目录中使用 [Azure Active Directory 应用程序库](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery)进行过单一登录配置的应用程序均可在 [Azure 门户](https://portal.azure.com)中查看和管理。 这些应用程序可在门户的“所有服务”&gt;“企业应用程序”部分中找到。 企业应用是在组织内部署和使用的应用。

![“企业应用程序”窗格](./media/configure-automatic-user-provisioning-portal/enterprise-apps-pane.png)

选择左侧的“所有应用程序”链接会显示包含所有已配置应用的列表，包括那些已从库中添加的应用。 选择某个应用时，会加载该应用的资源窗格，可在其中查看该应用的报告，以及管理各种设置。

选择左侧的“预配”即可管理用户帐户预配设置。

![应用程序资源窗格](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>预配模式
在“预配”窗格中，一开始是“模式”菜单，其中显示了企业应用程序支持的预配模式，可以对这些模式进行配置。 可用选项包括：

* **自动** - 如果 Azure AD 支持按 API 自动预配和/或取消预配此应用程序的用户帐户，则会显示此选项。 选择此模式会显示一个界面，该界面引导管理员配置可连接到应用程序的用户管理 API 的 Azure AD、创建用于定义用户帐户数据如何在 Azure AD 和应用之间流动的帐户映射和工作流，以及管理 Azure AD 预配服务。
* **手动** - 如果 Azure AD 不支持自动预配此应用程序的用户帐户，则会显示此选项。 此选项意味着，必须根据该应用程序提供的用户管理和预配功能（可能包括 SAML 实时预配），通过外部流程对存储在应用程序中的用户帐户记录进行管理。

## <a name="configuring-automatic-user-account-provisioning"></a>配置用户帐户自动预配
选择“自动”选项会显示一个分为四部分的屏幕：

### <a name="admin-credentials"></a>管理员凭据
在此部分输入的凭据是 Azure AD 连接到应用程序的用户管理 API 所必需的。 所需输入取决于应用程序。 若要了解凭据类型以及具体应用程序的要求，请参阅[具体应用程序的配置教程](../active-directory-saas-app-provisioning.md)。

选择“测试连接”按钮后，即可让 Azure AD 尝试使用提供的凭据连接到该应用的预配应用，对凭据进行测试。

### <a name="mappings"></a>映射
对用户帐户进行预配或更新以后，管理员即可在此部分查看和编辑在 Azure AD 和目标应用程序之间转移的用户属性。

在 Azure AD 用户对象和每个 SaaS 应用的用户对象之间存在预先配置的映射组。 某些应用可管理其他类型的对象，例如“组”或“联系人”。 选择表中的一个此类映射会在右侧显示映射编辑器，可在该编辑器中查看和自定义此类映射。

![应用程序资源窗格](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

支持的自定义项包括：

* 启用和禁用特定对象的映射（例如从 Azure AD 用户对象映射到 SaaS 应用的用户对象）。
* 编辑从 Azure AD 用户对象转移到应用的用户对象的属性。 有关属性映射的详细信息，请参阅[了解属性映射类型](../active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types)。
* 筛选 Azure AD 对目标应用程序执行的预配操作。 可以限制所执行的操作，而不必让 Azure AD 完全同步对象。 例如，如果只选择“更新”，Azure AD 就只会更新应用程序中的现有用户帐户，而不会创建新的。 如果只选择“创建”，Azure 就只会创建新用户帐户，而不会更新现有的。 有了此功能，管理员就可以创建不同的映射来创建帐户，并更新工作流。

### <a name="settings"></a>设置
管理员可以在此部分启动和停止所选应用程序的 Azure AD 预配服务，以及选择性地清除预配缓存并重新启动该服务。

如果是第一次为应用程序启用预配功能，则将“预配状态”更改为“开”即可启用该服务。 此更改会导致 Azure AD 预配服务执行初始同步，在同步过程中读取“用户和组”部分所分配的用户，查询其目标应用程序，并执行在 Azure AD 的“映射”部分定义的预配操作。 在此过程中，预配服务会存储所托管用户帐户的相关缓存数据，因此在执行取消预配操作时，不会影响目标应用程序中从来不在分配范围内的非托管帐户。 初始同步以后，预配服务会自动按 10 分钟的时间间隔同步用户和组对象。

将“预配状态”更改为“关”会直接暂停预配服务。 在这种状态下，Azure 不会创建、更新或删除应用中的任何用户或组对象。 将状态改回“开”会导致服务恢复。

选中“清除当前状态并重新启动同步”复选框后再进行保存，系统就会停止预配服务，对 Azure AD 所托管帐户的相关缓存数据进行转储，然后重新启动相关服务并再次执行初始同步。 管理员可以通过此选项再次启动预配部署过程。

### <a name="synchronization-details"></a>同步详细信息
此部分更详细地说明了预配服务的操作，包括针对应用程序运行的第一次预配服务和最后一次预配服务，以及所托管的用户和组对象数。

提供了“预配活动报告”链接，该报告记录了在 Azure AD 和目标应用程序之间创建、更新和删除的所有用户和组；此外还提供了“预配错误报告”链接，该报告提供的错误消息更详细地描述了无法读取、创建、更新或删除的用户和组对象。 

## <a name="feedback"></a>反馈

敬请不时地向我们发送反馈！ 欢迎在[反馈论坛](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal)的**管理员门户**版块中发表有关改进的意见和想法。  工程团队热衷于不断推出炫酷的新功能，遵从用户的指导来塑造和定义后续版本。

