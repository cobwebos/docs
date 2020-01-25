---
title: Azure AD에서 SaaS 앱 사용자를 자동으로 프로비저닝 | Microsoft Docs
description: Azure AD를 사용하여 여러 타사 SaaS 애플리케이션에서 사용자 계정을 자동으로 프로비저닝, 프로비저닝 해제, 지속적으로 업데이트하는 방법을 소개합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/25/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: eefdb42cebad2b7f532392254b652742527ed862
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76711467"
---
# <a name="automate-user-provisioning-and-deprovisioning-to-applications-with-azure-active-directory"></a>通过 Azure Active Directory 自动执行用户预配和取消预配

在 Azure Active Directory （Azure AD）中，术语 "**应用设置**" 是指在用户需要访问的云（[SaaS](https://azure.microsoft.com/overview/what-is-saas/)）应用程序中自动创建用户标识和角色。 除了创建用户身份外，自动预配还包括在状态或角色发生更改时维护和删除用户标识。 常见的方案包括将 Azure AD 用户预配到[Dropbox](../saas-apps/dropboxforbusiness-provisioning-tutorial.md)、 [Salesforce](../saas-apps/salesforce-provisioning-tutorial.md)、 [ServiceNow](../saas-apps/servicenow-provisioning-tutorial.md)等应用程序中。

![预配概述关系图](media/user-provisioning/provisioning-overview.png)

此功能可让你：

- **自动预配**：当新用户加入你的团队或组织时，在适当的系统中自动创建新帐户。
- **自动**取消预配：当用户离开团队或组织时，自动停用适当系统中的帐户。
- **同步系统之间的数据：** 确保你的应用和系统中的标识基于目录或人力资源系统中的更改保持最新。
- **预配组：** 将组预配到支持它们的应用程序。
- **控制访问权限：** 监视和审核已预配到应用程序中的人员。
- **无缝部署于棕色现场方案：** 与系统之间的现有标识匹配，并允许轻松集成，即使目标系统中已存在用户也是如此。
- **使用丰富的自定义：** 利用可自定义的属性映射来定义哪些用户数据应从源系统流向目标系统。
- **获取关键事件的警报：** 预配服务针对关键事件提供警报，并允许 Log Analytics 集成，以便你可以在其中定义自定义警报来满足你的业务需求。

## <a name="benefits-of-automatic-provisioning"></a>自动预配的优点

随着现代组织使用的应用程序数量不断增长，IT 管理员可以大规模地进行访问管理。 安全断言标记语言（SAML）或 Open ID Connect （OIDC）等标准允许管理员快速设置单一登录（SSO），但 access 还需要将用户预配到应用程序中。 对于很多管理员而言，设置意味着每周手动创建每个用户帐户或上传 CSV 文件，但这些过程非常耗时、昂贵且容易出错。 已采用 SAML 实时（JIT）等解决方案来自动进行预配，但企业还需要一个解决方案，以便在用户离开组织或不再需要基于角色更改访问某些应用时对其进行取消设置。

使用自动预配的一些常见动机包括：

- 最大程度地提高预配过程的效率和准确性。
- 节省与托管和维护定制开发的预配解决方案和脚本相关的成本。
- 通过在用户离开组织时立即从关键 SaaS 应用中删除用户的标识来保护组织的安全。
- 轻松将大量用户导入特定的 SaaS 应用程序或系统。
- 使用一组策略来确定已预配的用户以及可登录到应用程序的用户。

Azure AD 用户预配可帮助解决这些难题。 若要了解有关客户如何使用 Azure AD 用户预配的详细信息，可以阅读[ASOS 案例研究](https://aka.ms/asoscasestudy)。 以下视频概述了 Azure AD 中的用户预配：

> [!VIDEO https://www.youtube.com/embed/_ZjARPpI6NI]

## <a name="what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning"></a>Azure AD 자동 사용자 프로비전에서 사용할 수 있는 애플리케이션과 시스템은 무엇입니까?

Azure AD 功能为许多常用 SaaS 应用和人力资源系统预集成的支持，以及对实现[SCIM 2.0 标准](https://techcommunity.microsoft.com/t5/Identity-Standards-Blog/Provisioning-with-SCIM-getting-started/ba-p/880010)的特定部分的应用的一般性支持。

* **预先集成的应用程序（库 SaaS 应用）** 。 可以在[用户预配的应用程序教程列表](../saas-apps/tutorial-list.md)中找到 Azure AD 支持预集成预配连接器的所有应用程序。 库中列出的预先集成的应用程序通常使用基于 SCIM 2.0 的用户管理 Api 进行设置。 

   ![Salesforce 徽标](media/user-provisioning/gallery-app-logos.png)

   如果要请求新的预配应用程序，可以[请求将应用程序与应用程序库集成](../develop/howto-app-gallery-listing.md)。 对于用户设置请求，我们要求应用程序具有与 SCIM 兼容的终结点。 请请求应用程序供应商遵循 SCIM 标准，以便可以将应用程序快速加入我们的平台。

* **支持 SCIM 2.0 的应用程序**。 有关如何一般连接实现基于 SCIM 2.0 的用户管理 Api 的应用程序的信息，请参阅[生成 SCIM 终结点和配置用户设置](use-scim-to-provision-users-and-groups.md)。

## <a name="what-is-scim"></a>什么是 SCIM？

为了帮助自动进行预配和取消预配，应用公开了专有的用户和组 Api。 不过，尝试在多个应用程序中管理用户的任何人都将会告诉你，每个应用都尝试执行相同的简单操作，例如创建或更新用户、将用户添加到组或取消预配用户。 不过，所有这些简单的操作都是以不同的方式实现的，使用不同的终结点路径、指定用户信息的不同方法和不同的架构来表示信息的每个元素。

为了解决这些难题，SCIM 规范提供了一个常见的用户架构，可帮助用户进入、传出和绕过应用。 SCIM 正在成为预配的实际标准，并且与 SAML 或 OpenID Connect 等联合标准结合使用时，为管理员提供了一个用于访问管理的端到端基于标准的解决方案。

有关使用 SCIM 自动将用户和组预配和取消预配到应用程序的详细指南，请参阅[构建 SCIM 终结点和配置用户设置](use-scim-to-provision-users-and-groups.md)。

## <a name="manual-vs-automatic-provisioning"></a>수동 및 자동 프로비저닝

Azure AD 库中的应用程序支持以下两种预配模式之一：

* **手动**设置意味着该应用尚无自动 Azure AD 预配连接器。 用户帐户必须手动创建，例如通过将用户直接添加到应用的管理门户中，或使用用户帐户详细信息上传电子表格。 请查阅应用提供的文档，或联系应用开发人员来确定可用的机制。

* **자동**이란 Azure AD 프로비전 커넥터가 이 애플리케이션에 대해 개발되었음을 의미합니다. 你应遵循特定于设置应用程序预配的安装教程。 앱 자습서는 [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)에서 찾을 수 있습니다.

在 Azure AD 库中，支持自动预配的应用程序由**预配**图标指定。 切换到新的库预览体验来查看这些图标（在 "**添加应用程序" 页**顶部的标题中，选择 "**单击此处以试用新的和改进的应用程序库**" 的链接）。

![应用程序库中的预配图标](media/user-provisioning/browse-gallery.png)

向**企业应用**添加应用程序后，应用程序支持的预配模式也会显示在 "**设置**" 选项卡上。

## <a name="how-do-i-set-up-automatic-provisioning-to-an-application"></a>애플리케이션에 자동 프로비전을 설정하려면 어떻게 합니까?

对于库中列出的预先集成的应用程序，可以使用分步指南设置自动预配。 请参阅[集成库应用的教程列表](../saas-apps/tutorial-list.md)。 以下视频演示了如何设置 SalesForce 的自动用户预配。

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

对于支持 SCIM 2.0 的其他应用程序，请按照[创建 SCIM 终结点和配置用户设置一](use-scim-to-provision-users-and-groups.md)文中的步骤进行操作。


## <a name="related-articles"></a>관련 문서

- [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)
- [为用户预配自定义属性映射](customize-application-attributes.md)
- [为属性映射编写表达式](functions-for-customizing-application-data.md)
- [用户预配的作用域筛选器](define-conditional-rules-for-provisioning-user-accounts.md)
- [生成 SCIM 终结点并配置用户预配](use-scim-to-provision-users-and-groups.md)
- [Azure AD 동기화 API 개요](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
