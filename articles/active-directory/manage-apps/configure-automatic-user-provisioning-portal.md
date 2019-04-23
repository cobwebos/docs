---
title: Azure Active Directory 中针对企业应用的用户预配管理 | Microsoft 文档
description: 了解如何通过 Azure Active Directory 管理企业应用的用户帐户预配
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/01/2019
ms.author: celested
ms.reviewer: asmalser
ms.collection: M365-identity-device-management
ms.openlocfilehash: d03ca64f3f3d2f034433f2aaa49f6babb7f9e5b4
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59790520"
---
# <a name="managing-user-account-provisioning-for-enterprise-apps-in-the-azure-portal"></a>在 Azure 门户中管理企业应用的用户帐户预配

本文介绍如何使用 [Azure 门户](https://portal.azure.com)来管理支持它的应用程序的自动用户帐户预配和取消预配。 若要了解有关用户帐户自动预配及其工作原理的详细信息，请参阅 [Azure Active Directory SaaS 应用程序的自动化用户设置和取消设置](user-provisioning.md)。

## <a name="finding-your-apps-in-the-portal"></a>在门户中查找应用

使用 Azure Active Directory 门户查看和管理配置为单一登录的目录中的所有应用程序。 企业应用是在组织内部署和使用的应用。 请按照下列步骤来查看和管理企业应用程序：

1. 打开[Azure Active Directory 门户](https://aad.portal.azure.com)。

1. 选择**企业应用程序**在左窗格中。 显示所有已配置应用的列表，包括那些从库添加的应用。

1. 选择任意应用，以加载其资源窗格中，您可以在其中查看报表和管理应用设置。

1. 选择**预配**来管理用户帐户预配所选应用的设置。

   ![应用程序资源窗格](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning.png)

## <a name="provisioning-modes"></a>预配模式

**预配**窗格开头**模式**菜单，其中显示了预配模式适用于企业应用程序，并让你配置它们。 可用选项包括：

* **自动**-是否 Azure AD 支持自动的基于 API 的预配或取消预配的用户帐户添加到此应用程序，则显示此选项。 选择此模式中将显示一个接口，可帮助管理员：

  * 配置 Azure AD 以连接到应用程序的用户管理 API
  * 创建帐户映射和工作流的定义应如何将用户帐户数据流动之间 Azure AD 和应用程序
  * 管理 Azure AD 预配服务

* **手动**-是否 Azure AD 不支持自动预配用户帐户添加到此应用程序，则显示此选项。 在这种情况下，必须使用外部进程，该应用程序 （其中可以包括 SAML 实时预配） 提供的用户管理和预配功能管理记录存储在应用程序中的用户帐户。

## <a name="configuring-automatic-user-account-provisioning"></a>配置用户帐户自动预配

选择**自动**选项以指定的管理员凭据、 映射、 启动和停止，以及同步设置。

### <a name="admin-credentials"></a>管理员凭据

展开**管理员凭据**输入 Azure AD 连接到应用程序的用户管理 API 所需的凭据。 所需输入取决于应用程序。 若要了解凭据类型以及具体应用程序的要求，请参阅[具体应用程序的配置教程](user-provisioning.md)。

选择**测试连接**来测试的凭据，即可让 Azure AD 尝试连接到该应用程序的预配应用程序使用提供的凭据。

### <a name="mappings"></a>映射

展开**映射**查看和编辑 Azure AD 之间流动的用户属性和目标应用程序预配或更新用户帐户的时间。

没有一组预配置 Azure AD 用户对象和每个 SaaS 应用的用户对象之间的映射。 某些应用可管理其他类型的对象，例如“组”或“联系人”。 若要打开映射编辑器右侧，可以在其中查看并自定义这些表中选择一个映射。

![应用程序资源窗格](./media/configure-automatic-user-provisioning-portal/enterprise-apps-provisioning-mapping.png)

支持的自定义项包括：

* 启用和禁用特定对象的映射（例如从 Azure AD 用户对象映射到 SaaS 应用的用户对象）。
* 编辑从 Azure AD 用户对象转移到应用的用户对象的属性。 有关属性映射的详细信息，请参阅[了解属性映射类型](customize-application-attributes.md#understanding-attribute-mapping-types)。
* 筛选 Azure AD 在目标应用程序运行的预配操作。 而不是让 Azure AD 完全同步的对象，可以限制运行的操作。 

  例如，仅选择**更新**和现有用户帐户的应用程序中，但不会创建新的 Azure AD 仅更新。 仅选择**创建**Azure 仅创建新的用户帐户，但不会更新现有的和。 此功能允许管理员创建帐户创建不同的映射和更新工作流。

* 添加新的属性映射。 选择**添加新映射**底部**属性映射**窗格。 填写**编辑属性**窗体并选择**确定**向列表添加新的映射。 

### <a name="settings"></a>设置

你可以启动和停止 Azure AD 预配服务中所选应用程序**设置**区域中的**预配**屏幕。 您还可以选择清除预配缓存并重新启动服务。

如果是第一次为应用程序启用预配功能，则将“预配状态”更改为“开”即可启用该服务。 此更改会导致 Azure AD 预配服务以运行初始同步。它会读取在分配的用户**用户和组**部分中，查询目标应用程序，并运行在 Azure AD 中定义的预配操作**映射**部分。 在此过程中，预配服务存储管理，用户帐户的相关缓存的数据，因此在目标应用程序中从来不在分配的作用域内的非托管帐户，不会影响取消预配操作。 初始同步以后，预配服务会自动按 10 分钟的时间间隔同步用户和组对象。

更改**预配状态**到**关闭**暂停预配服务。 在此状态下，Azure 不会创建、 更新或删除应用中的任何用户或组对象。 将更改状态回**上**和离开的位置选取该服务。

选择**清除当前状态并重新启动同步**复选框，然后选择**保存**到：

* 停止预配服务
* 转储有关管理 Azure AD 帐户的缓存的数据
* 重新启动服务并运行初始同步

此选项允许管理员重新开始预配部署过程。

### <a name="synchronization-details"></a>同步详细信息

本部分提供有关预配服务，包括预配服务对其管理多少用户和组对象和应用程序中，运行过的第一个和最后一个时间操作的其他详细信息。

提供链接**预配活动报告**，它提供的所有用户的日志并创建、 更新和删除之间的 Azure AD 进行分组和目标应用程序。 链接还提供给**预配错误报表**，其中提供更详细的错误消息的用户和组对象的未能读取、 创建、 更新或删除。
