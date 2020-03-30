---
title: Azure AD 中企业应用的用户预配管理
description: 了解如何通过 Azure Active Directory 管理企业应用的用户帐户预配
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54f9bd1afeebedf4cbf37d75d9c57f3d8be0f288
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264123"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>在 Azure 门户中管理企业应用的用户帐户预配

本文介绍了管理支持自动用户帐户预配和取消预配的一般步骤。 *用户帐户预配*是一种在应用程序的本地用户配置文件存储中，创建、更新和/或禁用用户帐户记录的操作。 大多数云和 SaaS 应用程序在用户自己的本地用户配置文件存储中存储用户角色和权限，并且单次登录和访问工作*需要*在用户的本地存储中存在此类用户记录。 要了解有关自动用户帐户预配的更多信息，请参阅[使用 Azure 活动目录 自动预配和取消预配到 SaaS 应用程序](user-provisioning.md)。

> [!IMPORTANT]
> Azure 活动目录 （Azure AD） 具有包含数千个预集成应用程序，这些应用程序已启用以自动预配 Azure AD。 您应该首先在[有关如何将 SaaS 应用与 Azure 活动目录集成的教程列表中](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/)找到特定于应用程序的预配设置教程。 您可能会找到用于配置应用和 Azure AD 以创建预配连接的分步指南。

## <a name="finding-your-apps-in-the-portal"></a>在门户中查找应用

使用 Azure 活动目录门户查看和管理为目录中的单一登录配置的所有应用程序。 企业应用是在组织内部署和使用的应用。 按照以下步骤查看和管理企业应用程序：

1. 打开[Azure 活动目录门户](https://aad.portal.azure.com)。
1. 从左侧窗格中选择**企业应用程序**。 将显示所有已配置应用的列表，包括从库中添加的应用。
1. 选择任何应用以加载其资源窗格，您可以在其中查看报表和管理应用设置。
1. 选择 **"预配**"以管理所选应用的用户帐户预配设置。

   ![预配屏幕以管理用户帐户预配设置](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>预配模式

**预配**窗格从 **"模式"** 菜单开始，该菜单显示企业应用程序支持的预配模式，并允许您对其进行配置。 可用选项包括：

* **自动**- 如果 Azure AD 支持自动基于 API 的预配或取消向此应用程序的用户帐户预配，则显示此选项。 选择此模式可显示可帮助管理员的接口：

  * 将 Azure AD 配置为连接到应用程序的用户管理 API
  * 创建帐户映射和工作流，以定义用户帐户数据应在 Azure AD 和应用之间流动的方式
  * 管理 Azure AD 预配服务

* **手动**- 如果 Azure AD 不支持自动预配到此应用程序的用户帐户，则显示此选项。 在这种情况下，应用程序中存储的用户帐户记录必须使用外部进程进行管理，具体取决于该应用程序提供的用户管理和预配功能（其中可能包括 SAML 实时预配）。

## <a name="configuring-automatic-user-account-provisioning"></a>配置用户帐户自动预配

选择 **"自动"** 选项可指定管理员凭据、映射、启动和停止以及同步的设置。

### <a name="admin-credentials"></a>管理员凭据

展开**管理凭据**以输入 Azure AD 连接到应用程序的用户管理 API 所需的凭据。 所需输入取决于应用程序。 若要了解凭据类型以及具体应用程序的要求，请参阅[具体应用程序的配置教程](user-provisioning.md)。

选择 **"测试连接**"可让 Azure AD 尝试使用提供的凭据连接到应用的预配应用，以测试凭据。

### <a name="mappings"></a>映射

展开**映射**，以查看和编辑在预配或更新用户帐户时在 Azure AD 和目标应用程序之间流动的用户属性。

Azure AD 用户对象和每个 SaaS 应用的用户对象之间有一组预先配置的映射。 某些应用可管理其他类型的对象，例如“组”或“联系人”。 在表中选择映射以向右打开映射编辑器，您可以在其中查看和自定义它们。

![显示属性映射屏幕](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

支持的自定义项包括：

* 启用和禁用特定对象的映射（例如从 Azure AD 用户对象映射到 SaaS 应用的用户对象）。
* 编辑从 Azure AD 用户对象转移到应用的用户对象的属性。 有关属性映射的详细信息，请参阅[了解属性映射类型](customize-application-attributes.md#understanding-attribute-mapping-types)。
* 筛选 Azure AD 在目标应用程序上运行的预配操作。 您可以限制运行操作，而不是让 Azure AD 完全同步对象。

  例如，仅选择 **"更新**"和"Azure AD"仅更新应用程序中的现有用户帐户，但不会创建新用户帐户。 仅选择 **"创建**"和"Azure"仅创建新用户帐户，但不更新现有用户帐户。 此功能允许管理员为帐户创建和更新工作流创建不同的映射。

* 添加新属性映射。 在**属性映射**窗格的底部选择 **"添加新映射**"。 填写 **"编辑属性"** 窗体并选择 **"确定"** 以将新映射添加到列表中。

### <a name="settings"></a>设置

您可以在**预配**屏幕的 **"设置"** 区域中启动和停止所选应用程序的 Azure AD 预配服务。 您还可以选择清除预配缓存并重新启动服务。

如果是第一次为应用程序启用预配功能，则将“预配状态”**** 更改为“开”**** 即可启用该服务。 此更改会导致 Azure AD 预配服务运行初始周期。 它读取**在"用户和组**"部分中分配的用户，为其查询目标应用程序，然后运行 Azure AD**映射**部分中定义的预配操作。 在此过程中，预配服务存储有关其管理的用户帐户的缓存数据，因此目标应用程序内从未处于分配范围的非托管帐户不受取消预配操作的影响。 初始周期后，预配服务会在 40 分钟间隔内自动同步用户和组对象。

将**预配状态**更改为 **"关闭**"以暂停预配服务。 在此状态下，Azure 不会创建、更新或删除应用中的任何用户或组对象。 将状态更改回**On，** 服务从关闭的位置开始。

**清除当前状态并重新启动同步**将触发初始周期。 然后，该服务将再次评估源系统中的所有用户，并确定他们是否在预配范围内。 当应用程序当前处于隔离状态或需要更改属性映射时，这非常有用。 请注意，由于需要计算的对象数，初始周期需要比典型的增量周期更长的时间才能完成。 您可以在此处了解有关初始周期和增量周期性能的更多[详细信息](application-provisioning-when-will-provisioning-finish-specific-user.md)。 
