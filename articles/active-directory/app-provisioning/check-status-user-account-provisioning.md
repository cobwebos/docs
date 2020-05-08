---
title: 向 SaaS 应用程序报告自动用户帐户预配
description: 了解如何检查自动用户帐户预配作业的状态，以及如何排查单个用户的预配问题。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.openlocfilehash: 102a0b60c917d5ee24177ac4b52e97fe72c343e7
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82593873"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>教程：有关自动用户帐户预配的报告

Azure Active Directory （Azure AD）包含一个[用户帐户预配服务](user-provisioning.md)，该服务可帮助自动预配在 SaaS 应用和其他系统中预配用户帐户，以实现端到端的标识生命周期管理。 Azure AD 在[此处](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)为包含用户预配教程的所有应用程序和系统提供预先集成的用户预配连接器。

本文介绍如何在设置预配作业后检查其状态，以及如何排查单个用户和组的预配问题。

## <a name="overview"></a>概述

预配连接器是按照受支持应用程序的[随附文档](../saas-apps/tutorial-list.md)，使用 [Azure 门户](https://portal.azure.com)设置和配置的。 配置并运行预配作业后，可使用以下两种方法之一报告这些作业的状态：

* **Azure 门户**-本文主要介绍如何从[Azure 门户](https://portal.azure.com)检索报表信息，该信息提供了预配摘要报告以及给定应用程序的详细预配审核日志。
* **审核 API** - Azure Active Directory 还提供审核的 API，用于以编程方式检索详细预配审核日志。 请参阅 [Azure Active Directory 审核 API 参考](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit)，获取有关使用此 API 的具体文档。 尽管本文未专门介绍如何使用该 API，但详细说明了审核日志中记录的预配事件类型。

### <a name="definitions"></a>定义

本文使用以下术语，其定义如下：

* **源系统** - Azure AD 预配服务从中同步的用户存储库。 Azure Active Directory 是大多数预先集成的预配连接器的源系统，但有一些例外（示例：Workday 入站同步）。
* **目标系统** - Azure AD 预配服务要同步到的用户存储库。 这通常是一个 SaaS 应用程序（例如： Salesforce、ServiceNow、G Suite、Dropbox for Business），但在某些情况下，可以是本地系统，例如 Active Directory （例如： Workday 入站同步到 Active Directory）。

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>从 Azure 门户获取预配报告

若要获取给定应用程序的预配报表信息，请首先[启动 Azure 门户](https://portal.azure.com)，并**Azure Active Directory** &gt; "**活动**" 部分中的 "**企业应用** &gt; **预配日志（预览版）** "。 还可以浏览到配置了预配的企业应用程序。 例如，如果要在 LinkedIn Elevate 中预配用户，应用程序详细信息的导航路径为：

“Azure Active Directory”>“企业应用程序”>“所有应用程序”>“LinkedIn Elevate”****

在此处，你可以访问预配进度栏和预配日志，如下所述。

## <a name="provisioning-progress-bar"></a>设置进度栏

[预配进度栏](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar)在给定应用程序的 "**预配**" 选项卡中可见。 它位于 "**设置**" 下的 "**当前状态**" 部分，并显示当前初始或增量周期的状态。 本部分还显示：

* 已同步并且当前位于源系统与目标系统之间的预配范围内的用户和/或组总数。
* 上次运行同步的时间。 [初始循环](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)完成后，同步通常每20-40 分钟发生一次。
* [初始周期](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)是否已完成。
* 预配过程是否已被隔离，隔离状态的原因是什么（例如，由于管理员凭据无效，与目标系统通信失败）。

**当前状态**应该是管理员检查设置作业的操作运行状况的第一个地方。

 ![摘要报告](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>设置日志（预览）

预配服务执行的所有活动都记录在 Azure AD[预配日志](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)中。 可以通过在 "**活动**" 部分中选择 " **Azure Active Directory** &gt; **企业应用** &gt; **预配日志（预览版）** " 来访问 Azure 门户中的设置日志。 你可以根据用户的名称或源系统或目标系统中的标识符来搜索设置数据。 有关详细信息，请参阅[预配日志（预览）](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)。 记录的活动事件类型包括：

## <a name="troubleshooting"></a>疑难解答

预配摘要报告和预配日志起着重要作用，有助于管理员解决各种用户帐户设置问题。

有关如何排查自动用户预配问题的基于方案的指导，请参阅[在应用程序中配置和预配用户时出现问题](../app-provisioning/application-provisioning-config-problem.md)。

## <a name="additional-resources"></a>其他资源

* [管理企业应用的用户帐户预配](configure-automatic-user-provisioning-portal.md)
* [Azure Active Directory 的应用程序访问与单一登录是什么？](../manage-apps/what-is-single-sign-on.md)
