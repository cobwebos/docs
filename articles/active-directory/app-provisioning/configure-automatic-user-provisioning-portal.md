---
title: Azure AD 中的企业应用的用户预配管理
description: 了解如何通过 Azure Active Directory 管理企业应用的用户帐户预配
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 02f3198a9c3d35a0031fdd65ceefbb72b0f8bb31
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593805"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>在 Azure 门户中管理企业应用的用户帐户预配

本文介绍管理自动用户帐户预配和取消预配（适用于支持它的应用程序）的常规步骤。 *用户帐户预配*是一种在应用程序的本地用户配置文件存储中，创建、更新和/或禁用用户帐户记录的操作。 大多数云和 SaaS 应用程序将用户角色和权限存储在用户自己的本地用户配置文件存储区中，并且用户本地存储中存在此类用户记录是单一登录和访问工作*所必需*的。 若要了解有关自动用户帐户预配的详细信息，请参阅[通过 Azure Active Directory 自动执行用户预配和取消预配到 SaaS 应用程序](user-provisioning.md)。

> [!IMPORTANT]
> Azure Active Directory （Azure AD）有一个库，其中包含数千个预先集成的应用程序，这些应用程序通过 Azure AD 启用了自动预配。 首先应在[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)中找到应用程序特定的预配安装教程。 你可能会找到有关配置应用程序和 Azure AD 以创建预配连接的分步指导。

## <a name="finding-your-apps-in-the-portal"></a>在门户中查找应用

使用 Azure Active Directory 门户查看和管理在目录中为单一登录配置的所有应用程序。 企业应用是在组织内部署和使用的应用。 请按照以下步骤来查看和管理企业应用程序：

1. 打开[Azure Active Directory 门户](https://aad.portal.azure.com)。
1. 从左窗格中选择 "**企业应用程序**"。 将显示所有已配置应用的列表，包括从库中添加的应用。
1. 选择任何应用程序以加载其资源窗格，在其中可以查看报表和管理应用设置。
1. 选择 "**设置**" 以管理所选应用的用户帐户预配设置。

   ![用于管理用户帐户预配设置的 "设置" 屏幕](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>预配模式

"**设置**" 窗格以**模式**菜单开头，其中显示了企业应用程序支持的预配模式，并允许你对其进行配置。 可用选项包括：

* **自动**-如果 Azure AD 支持基于 API 自动预配或取消预配此应用程序的用户帐户，则会显示此选项。 选择此模式以显示一个可帮助管理员：

  * 配置 Azure AD 以连接到应用程序的用户管理 API
  * 创建用于定义用户帐户数据如何在 Azure AD 和应用之间流动的帐户映射和工作流
  * 管理 Azure AD 预配服务

* **手动**-如果 Azure AD 不支持向此应用程序自动预配用户帐户，则会显示此选项。 在这种情况下，存储在应用程序中的用户帐户记录必须使用外部进程进行管理，具体取决于该应用程序提供的用户管理和设置功能（可以包括 SAML 实时预配）。

## <a name="configuring-automatic-user-account-provisioning"></a>配置用户帐户自动预配

选择 "**自动**" 选项来指定管理员凭据、映射、启动和停止以及同步的设置。

### <a name="admin-credentials"></a>管理员凭据

展开 "**管理员凭据**" 以输入 Azure AD 连接到应用程序的用户管理 API 所需的凭据。 所需输入取决于应用程序。 若要了解凭据类型以及具体应用程序的要求，请参阅[具体应用程序的配置教程](user-provisioning.md)。

选择 "**测试连接**"，通过 Azure AD 尝试使用提供的凭据连接到应用的预配应用来测试凭据。

### <a name="mappings"></a>映射

展开 "**映射**" 可查看和编辑设置或更新用户帐户时在 Azure AD 和目标应用程序之间流动的用户属性。

Azure AD 用户对象与每个 SaaS 应用的用户对象之间存在预先配置的映射集。 某些应用可管理其他类型的对象，例如“组”或“联系人”。 在表中选择一个映射，以便在右侧打开映射编辑器，您可以在其中查看和自定义该编辑器。

![显示 "属性映射" 屏幕](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

支持的自定义项包括：

* 启用和禁用特定对象的映射（例如从 Azure AD 用户对象映射到 SaaS 应用的用户对象）。
* 编辑从 Azure AD 用户对象转移到应用的用户对象的属性。 有关属性映射的详细信息，请参阅[了解属性映射类型](customize-application-attributes.md#understanding-attribute-mapping-types)。
* 筛选 Azure AD 在目标应用程序上运行的设置操作。 可以限制运行的操作，而不是让 Azure AD 完全同步对象。

  例如，仅选择 "**更新**" 和 "Azure AD 仅更新应用程序中的现有用户帐户，但不创建新用户帐户。 仅选择 "**创建**"，Azure 仅创建新用户帐户，但不更新现有用户帐户。 此功能使管理员可以创建用于创建帐户和更新工作流的不同映射。

* 添加新的属性映射。 选择 "**属性映射**" 窗格底部的 "**添加新映射**"。 填写**编辑属性**窗体，然后选择 **"确定"** 以将新的映射添加到列表。

### <a name="settings"></a>设置

可以**在 "设置" 屏幕的**"**设置**" 区域中，为所选的应用程序启动和停止 Azure AD 预配服务。 你还可以选择清除预配缓存并重新启动该服务。

如果是第一次为应用程序启用预配功能，则将“预配状态”**** 更改为“开”**** 即可启用该服务。 此更改会导致 Azure AD 预配服务运行初始周期。 它会读取 "**用户和组**" 部分中分配的用户，查询目标应用程序，然后运行在 Azure AD**映射**"部分中定义的设置操作。 在此过程中，预配服务将存储有关它所管理的用户帐户的缓存数据，因此，取消预配操作不会影响目标应用程序中从不在分配范围内的非托管帐户。 初始循环后，预配服务会按40分钟的间隔自动同步用户和组对象。

将**预配状态**更改为 "**关闭**" 以暂停预配服务。 在此状态下，Azure 不会创建、更新或删除应用程序中的任何用户或组对象。 将状态改回 "**开**"，然后服务从中断的位置继续。

**清除当前状态，重新启动同步**会触发初始周期。 然后，该服务将再次评估源系统中的所有用户，并确定它们是否处于预配的作用域中。 当应用程序当前处于隔离中或者需要更改属性映射时，这会很有用。 请注意，由于需要计算对象的数量，初始周期的完成时间比典型增量周期长。 可在[此处](application-provisioning-when-will-provisioning-finish-specific-user.md)详细了解初始和增量循环的性能。 
