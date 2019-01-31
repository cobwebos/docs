---
title: 创建 Azure Active Directory 租户 | Microsoft Docs
description: 了解如何创建用于注册和生成应用程序的 Azure AD 租户。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 3e4cd8859f376fa4fbb9069b145d893483c3fc14
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101254"
---
# <a name="quickstart-set-up-a-dev-environment"></a>快速入门：设置开发环境

Microsoft 标识平台可让开发人员生成面向各种自定义 Microsoft 365 环境和标识的应用程序。 要开始使用 Microsoft 标识平台，你将需要访问环境（也称为 Azure AD 租户），该环境可以注册和管理应用、可以访问 Microsoft 365 数据并部署自定义条件访问和租户限制。

租户是组织的表示形式。 它是 Azure AD 专用实例，组织或应用开发人员与 Microsoft 建立关系时（例如注册 Azure、Microsoft Intune 或 Microsoft 365）会收到该实例。

每个 Azure AD 租户都与其他 Azure AD 租户不同并单独存在，而且使用自己的工作和学校标识、消费者标识（如果是 Azure AD B2C 租户）以及应用注册进行表示。 租户内部的应用注册只允许从租户或所有租户的帐户中进行身份验证。

## <a name="determining-environment-type"></a>确定环境类型

有两种可以创建的环境类型。 确定所需的内容仅基于你的应用将进行身份验证的用户类型。

* 工作和学校（Azure AD 帐户）或 Microsoft 帐户（如 outlook.com 和 live.com）
* 社交和本地帐户（Azure AD B2C）

本快速入门分为两种方案，具体取决于所要生成的应用类型。 如需获取更多针对标识类型的帮助，请参阅[关于 Microsoft 标识平台](about-microsoft-identity-platform.md)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>工作和学校帐户或个人 Microsoft 帐户

### <a name="use-an-existing-tenant"></a>使用现有租户

许多开发人员已通过绑定到 Azure AD 租户的服务或订阅（例如 Microsoft 365 或 Azure 订阅）获得了租户。

1. 要检查租户，请使用要用于管理应用程序的帐户登录 [Azure 门户](https://portal.azure.com)。
1. 查看右上角。 如果你有一个租户，则会自动登录到该租户，并且帐户名的正下方会显示租户名称。
   * 将鼠标指针悬停在 Azure 门户右上角的帐户名上，可以查看你的姓名、电子邮件、目录和租户 ID (GUID) 以及域。
   * 如果帐户与多个租户相关联，则可以选择帐户名打开一个菜单，并在其中切换租户。 每个租户都有自己的唯一租户 ID。

> [!TIP]
> 如果需要查找租户 ID，可执行以下操作：
* 将鼠标指针悬停在帐户名上以获取目录/租户 ID，或
* 在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”

如果没有任何与帐户关联的现有租户，则帐户名下面会显示一个 GUID；另外，除非按照下一节的步骤操作，否则无法执行注册应用等操作。

### <a name="create-a-new-azure-ad-tenant"></a>创建新的 Azure AD 租户

如果还没有 Azure AD 租户或想要为开发创建新租户，请遵循[目录创建体验](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)。 必须提供以下信息才能创建新租户：

- 组织名称
- **初始域** - 这将包括在 *.onmicrosoft.com 中。 稍后你可以更详细地自定义域。 
- 国家或地区

## <a name="social-and-local-accounts"></a>社交和本地帐户

要开始生成登录社交和本地帐户的应用，你将需要创建 Azure AD B2C 租户。 请从[创建 Azure AD B2C 租户](../../active-directory-b2c/tutorial-create-tenant.md)开始。

## <a name="next-steps"></a>后续步骤

* 尝试编写快速入门代码并开始对用户进行身份验证。 
* 有关更多深入的代码示例，请参阅文档的“教程”部分。
* 想要将应用部署到云？ 请查看[将容器部署到 Azure](https://docs.microsoft.com/azure/index#pivot=products&panel=containers)。 
