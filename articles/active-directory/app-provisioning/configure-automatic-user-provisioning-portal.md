---
title: 在 Azure AD 中管理企业应用的用户预配
description: 了解如何通过 Azure Active Directory 管理企业应用的用户帐户预配
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 11/25/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 9c42a83b4f7f3c6b5ff501525a04ebd96c2a692a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88234833"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>在 Azure 门户中管理企业应用的用户帐户预配

本文介绍管理支持它的应用程序的自动用户帐户预配和取消预配的常规步骤。 *用户帐户预配*是一种在应用程序的本地用户配置文件存储中，创建、更新和/或禁用用户帐户记录的操作。 大多数云应用程序和 SaaS 应用程序将用户角色和权限存储在用户自身的本地用户配置文件存储中，并且单一登录和访问工作需要用户本地存储中存在此类用户记录。 若要了解有关用户帐户自动预配的详细信息，请参阅[使用 Azure Active Directory 自动化 SaaS 应用程序用户预配和取消预配](user-provisioning.md)。

> [!IMPORTANT]
> Azure Active Directory (Azure AD) 有一个库，其中包含数千个预先集成的应用程序，这些应用程序通过 Azure AD 启用了自动预配。 首先应在[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](../saas-apps/tutorial-list.md)中找到特定于应用程序的预配设置教程。 你可能会找到有关配置应用程序和 Azure AD 以创建预配连接的分步指导。

## <a name="finding-your-apps-in-the-portal"></a>在门户中查找应用

使用 Azure Active Directory 门户查看和管理在目录中为单一登录配置的所有应用程序。 企业应用是在组织内部署和使用的应用。 请按照以下步骤来查看和管理企业应用程序：

1. 打开 [Azure Active Directory 门户](https://aad.portal.azure.com)。
1. 从左窗格中选择“企业应用程序”。 系统显示所有已配置的应用（包括从库中添加的应用）的列表。
1. 选择任何应用以加载其资源窗格，可在其中查看报告和管理应用设置。
1. 选择“预配”管理所选应用的用户帐户预配设置。

   ![用于管理用户帐户预配设置的“预配”屏幕](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>预配模式

在“预配”窗格中，一开始是“模式”菜单，其中显示了企业应用程序支持的预配模式，可以对这些模式进行配置。 可用选项包括：

* 自动 - 如果 Azure AD 支持基于 API 自动预配或取消预配此应用程序的用户帐户，则会显示此选项。 选择此模式可显示一个界面来帮助管理员：

  * 配置 Azure AD 以连接到应用程序的用户管理 API
  * 创建用于定义用户帐户数据如何在 Azure AD 与应用之间流动的帐户映射和工作流
  * 管理 Azure AD 预配服务

* 手动 - 如果 Azure AD 不支持自动预配此应用程序的用户帐户，则会显示此选项。 在此情况下，必须根据该应用程序提供的用户管理和预配功能（可能包括 SAML 实时预配），通过外部流程对存储在应用程序中的用户帐户记录进行管理。

## <a name="configuring-automatic-user-account-provisioning"></a>配置用户帐户自动预配

选择“自动”选项来指定管理员凭据、映射、开始和停止以及同步的设置。

### <a name="admin-credentials"></a>管理员凭据

展开“管理员凭据”以输入 Azure AD 连接到应用程序的用户管理 API 所需的凭据。 所需输入取决于应用程序。 若要了解凭据类型以及具体应用程序的要求，请参阅[具体应用程序的配置教程](user-provisioning.md)。

选择“测试连接”以便让 Azure AD 尝试使用提供的凭据连接到应用的预配应用，对凭据进行测试。

### <a name="mappings"></a>映射

在预配或更新用户帐户时，展开“映射”以查看和编辑在 Azure AD 和目标应用程序之间转移的用户属性。

在 Azure AD 用户对象和每个 SaaS 应用的用户对象之间存在预先配置的映射组。 某些应用可管理其他类型的对象，例如“组”或“联系人”。 在表中选择一个映射，以在右侧打开映射编辑器，可以在其中查看和自定义映射。

![显示“属性映射”屏幕](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

支持的自定义项包括：

* 启用和禁用特定对象的映射（例如从 Azure AD 用户对象映射到 SaaS 应用的用户对象）。
* 编辑从 Azure AD 用户对象转移到应用的用户对象的属性。 有关属性映射的详细信息，请参阅[了解属性映射类型](customize-application-attributes.md#understanding-attribute-mapping-types)。
* 筛选 Azure AD 对目标应用程序运行的预配操作。 可以限制所运行的操作，而不必让 Azure AD 完全同步对象。

  例如，只选择“更新”，Azure AD 便只会更新应用程序中的现有用户帐户，但不会创建新帐户。 只选择“创建”，Azure 便只会创建新用户帐户，但不会更新现有帐户。 有了此功能，管理员就可以创建不同的映射来创建帐户，并更新工作流。

* 添加新属性映射。 选择“属性映射”窗格底部的“添加新映射”。 填写“编辑属性”窗体，然后选择“确定”以将新映射添加到列表。

### <a name="settings"></a>设置

可以在“预配”屏幕的“设置”区域中，为所选应用程序启动和停止 Azure AD 预配服务。 还可以选择清除预配缓存并重启服务。

如果是第一次为应用程序启用预配功能，则将“预配状态”更改为“开”即可启用该服务。 此更改会导致 Azure AD 预配服务运行初始周期。 它会读取“用户和组”部分所分配的用户，查询其目标应用程序，然后运行在 Azure AD 的“映射”部分定义的预配操作。 在此过程中，预配服务会存储所托管用户帐户的相关缓存数据，因此在执行取消预配操作时，不会影响目标应用程序中从来不在分配范围内的非托管帐户。 初始周期以后，预配服务会自动按四十分钟的时间间隔同步用户和组对象。

将“预配状态”更改为“关”以暂停预配服务。 在这种状态下，Azure 不会创建、更新或删除应用中的任何用户或组对象。 将状态重新更改为“开”，服务会从中断的位置继续。

“清除当前状态并重新开始同步”会触发初始周期。 服务随后会再次评估源系统中的所有用户，并确定它们是否处于预配的范围中。 当应用程序当前处于隔离中或者需要更改属性映射时，这可能会很有用。 请注意，由于需要评估的对象数，初始周期的完成时间比典型增量周期更长。 可在[此处](application-provisioning-when-will-provisioning-finish-specific-user.md)详细了解初始和增量周期的性能。