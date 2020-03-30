---
title: Azure AD 中的自动 SaaS 应用用户预配 | Microsoft Docs
description: 介绍可以如何使用 Azure AD 进行自动化设置、取消设置，并不断跨多个第三方 SaaS 应用程序更新用户帐户。
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
ms.openlocfilehash: 5e828fd9c2561007c332db67bfd0b20dda9b845f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454527"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>使用 Azure 活动目录自动调配用户并取消预配到应用程序

在 Azure 活动目录 （Azure AD） 中，术语**应用预配**是指在用户需要访问的云[（SaaS](https://azure.microsoft.com/overview/what-is-saas/)） 应用程序中自动创建用户标识和角色。 除了创建用户标识外，自动预配还包括在状态或角色发生更改时维护和删除用户标识。 常见方案包括将 Azure AD 用户预配到 [Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)、[Salesforce](../saas-apps/salesforce-provisioning-tutorial.md)、[ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md) 等应用程序中。

![预配概述图](./media/user-provisioning/provisioning-overview.png)

此功能允许您：

- **自动预配**：在新人加入您的团队或组织时，在正确的系统中自动创建新帐户。
- **自动取消预配：** 当人们离开团队或组织时，自动停用正确的系统中的帐户。
- **在系统之间同步数据：** 确保应用和系统中的标识根据目录或人力资源系统的更改保持最新。
- **预配组：** 将组预配到支持它们的应用程序。
- **管理访问：** 监视和审核已预配到应用程序中的人员。
- **在棕色字段方案中无缝部署：** 匹配系统之间的现有标识，并允许轻松集成，即使目标系统中已存在用户也是如此。
- **使用丰富的自定义：** 利用可自定义的属性映射，这些映射定义用户数据应从源系统流向目标系统。
- **获取关键事件的警报：** 预配服务为关键事件提供警报，并允许进行日志分析集成，您可以在其中定义自定义警报以满足业务需求。

## <a name="benefits-of-automatic-provisioning"></a>自动预配的好处

随着现代组织中使用的应用程序数量持续增长，IT 管理员的任务是大规模进行访问管理。 安全断言标记语言 （SAML） 或打开 ID 连接 （OIDC） 等标准允许管理员快速设置单一登录 （SSO），但访问还要求将用户预配到应用中。 对于许多管理员，预配意味着每周手动创建每个用户帐户或上传 CSV 文件，但这些过程既耗时、昂贵且容易出错。 采用 SAML 即时 （JIT） 等解决方案来自动调配，但企业还需要一种解决方案，在用户离开组织或不再需要基于角色更改访问某些应用时取消预配。

使用自动预配的一些常见动机包括：

- 最大限度地提高资源调配流程的效率和准确性。
- 节省与托管和维护自定义开发的预配解决方案和脚本相关的成本。
- 通过在用户离开组织时立即从关键 SaaS 应用中删除其身份，保护组织。
- 轻松将大量用户导入特定的 SaaS 应用程序或系统。
- 具有一组策略来确定预配谁以及谁可以登录到应用。

Azure AD 用户预配可帮助应对这些挑战。 要了解有关客户如何使用 Azure AD 用户预配的情况，请阅读[ASOS 案例研究](https://aka.ms/asoscasestudy)。 以下视频概述了 Azure AD 中的用户预配：

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>可在哪些应用程序和系统中使用 Azure AD 自动用户预配？

Azure AD 具有对许多流行的 SaaS 应用和人力资源系统的预集成支持，以及对实现[SCIM 2.0 标准](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)特定部分的应用的通用支持。

* **预集成应用程序（库 SaaS 应用程序）。** 您可以在用于[用户预配的应用程序教程列表中](../saas-apps/tutorial-list.md)找到 Azure AD 支持预集成预配连接器的所有应用程序。 库中列出的预集成应用程序通常使用基于 SCIM 2.0 的用户管理 API 进行预配。 

   ![销售队伍徽标](./media/user-provisioning/gallery-app-logos.png)

   如果您想请求新的应用程序进行预配，您可以[请求将应用程序与我们的应用程序库集成](../develop/howto-app-gallery-listing.md)。 对于用户预配请求，我们要求应用程序具有符合 SCIM 的终结点。 请要求应用程序供应商遵循 SCIM 标准，以便我们可以快速将应用程序登机到我们的平台。

* **支持 SCIM 2.0**的应用程序。 有关如何通用连接实现基于 SCIM 2.0 的用户管理 API 的应用程序的信息，请参阅生成[SCIM 终结点并配置用户预配](use-scim-to-provision-users-and-groups.md)。

## <a name="what-is-system-for-cross-domain-identity-management-scim"></a>什么是跨域身份管理系统 （SCIM）？

为了帮助自动预配和取消预配，应用公开专有用户和组 API。 但是，尝试在多个应用中管理用户的任何人将告诉您，每个应用都尝试执行相同的简单操作，例如创建或更新用户、将用户添加到组或取消预配用户。 但是，所有这些简单操作的实现方式稍有不同，使用不同的终结点路径、指定用户信息的不同方法以及表示每个信息元素的不同架构。

为了应对这些挑战，SCIM 规范提供了一个通用用户架构，可帮助用户进入、退出应用和周围应用。 SCIM 正在成为预配的实际标准，当与 SAML 或 OpenID Connect 等联合标准结合使用时，为管理员提供了基于端到端标准的访问管理解决方案。

有关开发 SCIM 终结点以自动预配和取消向应用程序预配用户和组的详细信息，请参阅[生成 SCIM 终结点并配置用户预配](use-scim-to-provision-users-and-groups.md)。 对于库中的预集成应用程序（Slack、Azure 数据块、雪花等），可以跳过开发人员文档并使用[此处](../saas-apps/tutorial-list.md)提供的教程。

## <a name="manual-vs-automatic-provisioning"></a>手动预配与自动预配

Azure AD 库中的应用程序支持两种预配模式之一：

* **手动**预配意味着尚未为应用提供自动 Azure AD 预配连接器。 用户帐户必须手动创建，例如，通过将用户直接添加到应用的管理门户，或上传包含用户帐户详细信息的电子表格。 请参阅应用提供的文档，或联系应用开发人员以确定可用的机制。

* **自动化**意味着已为此应用程序开发了 Azure AD 预配连接器。 您应该遵循特定于设置应用程序的预配的设置教程。 可以在[有关如何将 SaaS 应用与 Azure Active Directory 集成的教程列表](../saas-apps/tutorial-list.md)找到这些应用教程。

在 Azure AD 库中，支持自动预配的应用程序由**预配**图标指定。 切换到新的库预览体验以查看这些图标（在 **"添加应用程序"页面**顶部的横幅中，选择显示"**单击此处尝试新的和改进的应用程序库**"的链接）。

![应用程序库中的预配图标](./media/user-provisioning/browse-gallery.png)

将应用程序添加到**企业应用**后，应用程序支持的预配模式也会显示在**预配**选项卡上。

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>如何设置为自动预配到应用程序？

对于库中列出的预集成应用程序，可用于设置自动预配的分步指南。 请参阅[集成库应用教程列表](../saas-apps/tutorial-list.md)。 以下视频演示如何为 SalesForce 设置自动用户预配。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

对于支持 SCIM 2.0 的其他应用程序，请按照文章"[构建 SCIM 终结点"中的步骤进行配置用户预配](use-scim-to-provision-users-and-groups.md)。


## <a name="related-articles"></a>相关文章

- [有关如何集成 SaaS 应用的教程列表](../saas-apps/tutorial-list.md)
- [自定义用户预配的属性映射](customize-application-attributes.md)
- [为属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)
- [用户预配的范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)
- [构建 SCIM 终结点并配置用户预配](use-scim-to-provision-users-and-groups.md)
- [Azure AD 同步 API 概述](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
