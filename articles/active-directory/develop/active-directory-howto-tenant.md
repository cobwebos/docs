---
title: 如何获取 Azure AD 租户 | Microsoft Docs
description: 如何获取用于注册和生成应用程序的 Azure Active Directory 租户。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: 1f866d3ee56b0c9a1e7a986d3ac951764b6a1cae
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399508"
---
# <a name="how-to-get-an-azure-active-directory-tenant"></a>如何获取 Azure Active Directory 租户

在 Azure Active Directory (Azure AD) 中， [租户](https://msdn.microsoft.com/library/azure/jj573650.aspx#Anchor_0) 表示组织。 它是组织通过注册 Azure、Microsoft Intune 或 Office 365 等 Microsoft 云服务时或其他方式来与 Microsoft 建立关系时接收并拥有的 Azure AD 服务专用实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户。 

租户包含公司中的用户以及有关这些用户的信息 - 他们的密码、用户配置文件数据、权限，等等。 它还包含与某家组织及其安全性相关的组、应用程序和其他信息。

要允许 Azure AD 用户登录到应用程序，必须在自己的租户中注册应用程序。 创建 Azure AD 租户以及在其中发布应用程序是**完全免费的**（不过，可以选择在租户中购买高级功能）。 实际上，许多开发人员都会针对试验、开发、过渡和测试目的创建多个租户和应用程序。

## <a name="use-an-existing-azure-ad-tenant"></a>使用现有的 Azure AD 租户

许多开发人员已通过绑定到 Azure AD 租户的服务或订阅（例如 Office 365 或 Azure 订阅）获得了租户。 若要检查是否拥有租户，请使用用于管理应用程序的帐户登录到 [Azure 门户](https://portal.azure.com)，并查看右上角，其中显示了帐户信息。 如果你拥有租户，则会自动登录到该租户，并且紧靠在帐户名的下方会显示租户名称。 如果将鼠标指针悬停在 Azure 门户右上角的帐户名上，可看到你的名称、电子邮件、目录和租户 ID (GUID) 以及域。 如果帐户与多个租户相关联，则可以选择帐户名打开一个菜单，并在其中切换租户。 每个租户都有自己的唯一租户 ID。

> [!TIP]
> 如果需要找到租户 ID，可通过多种方法查找该信息。 可以将鼠标指针悬停在帐户名上以获取租户 ID，也可以在 Azure 门户中选择“Azure Active Directory”>“属性”>“目录 ID”。

如果没有任何与帐户关联的现有租户，则帐户名下面会显示一个 GUID；另外，除非[创建一个新租户](#create-a-new-azure-ad-tenant)，否则无法执行注册应用等操作。

## <a name="create-a-new-azure-ad-tenant"></a>创建新的 Azure AD 租户

如果没有 Azure AD 租户或想要创建一个新租户，可以使用 [Azure 门户](https://portal.azure.com)中的[目录创建体验](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory)来实现此目的。 此过程大约需要花费一分钟，结束时，系统会提示你导航到新建的租户。
