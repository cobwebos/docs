---
title: 在 Azure 门户中创建和删除 Azure AD B2C 使用者用户帐户
description: 了解如何使用 Azure 门户在 Azure AD B2C 目录中创建和删除使用者用户。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/09/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5d6d6169f8662c9b973fb7f624a590322f62b0b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387518"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>使用 Azure 门户在 Azure AD B2C 中创建和删除使用者用户

某些情况下，可能需要在 Azure Active Directory B2C (Azure AD B2C) 目录中手动创建所有者帐户。 虽然最常见的情况下，Azure AD B2C 目录中的使用者帐户是在用户注册使用你的应用程序之一时创建的，但你可以通过编程方式和 Azure 门户创建它们。 本文重点介绍如何通过 Azure 门户方法来创建和删除用户。

若要添加或删除用户，必须为帐户分配“用户管理员”或“全局管理员”角色。  

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="types-of-user-accounts"></a>用户帐户的类型

根据 [Azure AD B2C 中的用户帐户概述](user-overview.md)中的介绍，有三种类型的用户帐户可以在 Azure AD B2C 目录中创建：

* Work
* 来宾
* 使用者

本文重点介绍如何在 Azure 门户中使用**使用者帐户**。 若要了解如何创建和删除工作帐户和来宾帐户，请参阅[使用 Azure Active Directory 添加或删除用户](../active-directory/fundamentals/add-users-azure-active-directory.md)。

## <a name="create-a-consumer-user"></a>创建使用者用户

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择“目录 + 订阅”  筛选器，然后选择包含Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择“Azure AD B2C”  。 或者，选择“所有服务”  并搜索并选择“Azure AD B2C”  。
1. 在“管理”下，选择“用户”   。
1. 选择“新建用户”。 
1. 选择“创建 Azure AD B2C 用户”。 
1. 选择“登录方法”，  然后输入新用户的**电子邮件**地址或**用户名**。 在此处选择的登录方法必须与你为 Azure AD B2C 租户的“本地帐户”标识提供者指定的设置匹配  （请参阅 Azure AD B2C 租户中的“管理”   >   “标识提供者”）。
1. 输入该用户的“名称”。  该名称通常是用户的完整名称（姓和名）。
1. （可选）如果希望延迟用户登录，可以选择“阻止登录”。  可以稍后在 Azure 门户中通过编辑用户的“配置文件”  来启用登录。
1. 选择“自动生成密码”或“让我创建密码”。  
1. 指定用户的**名字**和**姓氏**。
1. 选择“创建”  。

除非你已选择“阻止登录”，否则用户现在就可以使用你指定的登录方法（电子邮件或用户名）登录。 

## <a name="delete-a-consumer-user"></a>删除使用者用户

1. 在 Azure AD B2C 目录中选择“用户”，然后选择要删除的用户。 
1. 选择“删除”，然后选择“是”，确认进行删除。  

若要详细了解如何在删除后的头 30 天内还原用户或永久删除用户，请参阅[使用 Azure Active Directory 还原或删除最近删除的用户](../active-directory/fundamentals/active-directory-users-restore.md)。

## <a name="next-steps"></a>后续步骤

有关自动用户管理方案（例如，将用户从其他标识提供者迁移到 Azure AD B2C 目录），请参阅 [Azure AD B2C:用户迁移](user-migration.md)。
