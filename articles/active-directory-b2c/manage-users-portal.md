---
title: 创建 & 删除 Azure 门户中 Azure AD B2C 使用者用户帐户
description: 了解如何使用 Azure 门户在 Azure AD B2C 目录中创建和删除使用者用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c8fad8b5bd2dccba36c0c6888712fa806f3cbfc8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78187213"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>使用 Azure 门户在 Azure AD B2C 中创建和删除使用者用户

在某些情况下，你可能想要在 Azure Active Directory B2C （Azure AD B2C）目录中手动创建使用者帐户。 尽管用户注册使用你的某个应用程序时，通常会创建 Azure AD B2C 目录中的使用者帐户，但你可以通过使用 Azure 门户以编程方式创建它们。 本文重点介绍用户创建和删除的 Azure 门户方法。

若要添加或删除用户，必须为你的帐户分配 "*用户管理员*" 或 "*全局管理员*" 角色。

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>用户帐户的类型

如 Azure AD B2C 中的[用户帐户概述](user-overview.md)中所述，可以在 Azure AD B2C 目录中创建三种类型的用户帐户：

* Work
* 来宾
* 使用者

本文重点介绍如何在 Azure 门户中使用**使用者帐户**。 有关创建和删除工作帐户和来宾帐户的信息，请参阅[使用 Azure Active Directory 添加或删除用户](../active-directory/fundamentals/add-users-azure-active-directory.md)。

## <a name="create-a-consumer-user"></a>创建使用者用户

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”。 或者，选择“所有服务”并搜索并选择“Azure AD B2C”。
1. 在“管理”下，选择“用户”。
1. 选择“新建用户”。
1. 选择 "**创建 Azure AD B2C 用户**"。
1. 选择 "**登录方法**"，然后输入新用户的**电子邮件**地址或**用户名**。 你在此处选择的登录方法必须与你为 Azure AD B2C 租户的*本地帐户*标识提供者指定的设置匹配（请参阅**管理**Azure AD B2C 租户中 > **标识提供者**）。
1. 输入用户的**名称**。 这通常是用户的全名（给定和姓氏）。
1. 可有可无如果要延迟用户登录的能力，可以**阻止登录**。 稍后可以通过在 Azure 门户中编辑用户的**配置文件**来启用登录。
1. 选择 "**自动生成密码**" 或**创建密码**。
1. 指定用户的**名字**和**姓氏**。
1. 选择“创建”。

除非已选择 "**阻止登录**"，否则用户现在可以使用您指定的登录方法（电子邮件或用户名）登录。

## <a name="delete-a-consumer-user"></a>删除使用者用户

1. 在 Azure AD B2C 目录中，选择 "**用户**"，然后选择要删除的用户。
1. 选择 "**删除**"，然后选择 **"是"** 以确认删除。

有关在删除后的前30天内还原用户的详细信息，或用于永久删除用户的详细信息，请参阅[使用 Azure Active Directory 还原或删除最近删除的用户](../active-directory/fundamentals/active-directory-users-restore.md)。

## <a name="next-steps"></a>后续步骤

对于自动用户管理方案，例如，将用户从其他标识提供者迁移到 Azure AD B2C 目录，请参阅[Azure AD B2C：用户迁移](user-migration.md)。
