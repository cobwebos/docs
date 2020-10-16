---
title: 与 Azure Active Directory 的 SCIM 同步
description: 实现此同步模式的体系结构指南
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: be8dbd4d8deccd42d6fcc391eab1e57df7514401
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114080"
---
# <a name="scim-synchronization-with-azure-active-directory"></a>与 Azure Active Directory 的 SCIM 同步

用于跨域标识管理的系统 (SCIM) 是一种开放标准协议，用于在标识域和 IT 系统之间自动交换用户身份信息。 SCIM 确保添加到人力资本管理 (HCM) 系统的员工自动在 Azure Active Directory (Azure AD) 或 Windows Server Active Directory 中创建帐户。 用户属性和配置文件在两个系统之间同步，并根据用户状态或角色更改更新删除用户。

SCIM 是两个终结点的标准化定义：/Users 终结点和/Groups 终结点。 它使用常见的 REST 谓词来创建、更新和删除对象。 它还将预定义的架构用于常见属性，如组名称、用户名、名字、姓氏和电子邮件。 提供 SCIM 2.0 REST API 的应用程序可以减少或消除使用专有用户管理 Api 或产品的难点。 例如，任何符合 SCIM 的客户端都可以将 JSON 对象的 HTTP POST 发送到/Users 终结点，以创建新的用户条目。 符合 SCIM 标准的应用不需要采用略有不同的 API 来执行相同的基本操作，而是可以立即使用现有的客户端、工具和代码。 

## <a name="use-when"></a>何时使用： 

你需要从 HCM 系统自动将用户信息预配到 Azure AD 和 Windows Server Active Directory，然后在必要时将系统设定为目标系统。 

![体系结构图](./media/authentication-patterns/scim-auth.png)


## <a name="components-ofsystem"></a>系统组件 

* **HCM 系统**：用于实现人力资本管理过程的应用程序和技术，以及在整个员工生命周期中支持和自动化人力资源流程的实践。 

* **Azure AD 预配服务**：使用 SCIM 2.0 协议进行自动预配。 该服务连接到应用程序的 SCIM 终结点，并使用 SCIM 用户对象架构和 REST Api 自动预配和取消预配用户和组。  

* **Azure AD**：用于管理标识及其权利的生命周期的用户存储库。 

* **目标系统**：具有 SCIM 终结点并与 Azure AD 预配结合使用的应用程序或系统，用于启用自动预配用户和组。  

## <a name="implementscimwith-azure-ad"></a>通过 Azure AD 实现 SCIM 

* [在 Azure AD 中设置的工作原理 ](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works)

* [在 Azure 门户中管理企业应用的用户帐户预配 ](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

* [生成 SCIM 终结点并使用 Azure AD 配置用户预配  ](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groups)

* [Azure AD 预配服务的 SCIM 2.0 协议符合性](https://docs.microsoft.com/azure/active-directory/app-provisioning/use-scim-to-provision-users-and-groupsapplication-provisioning-config-problem-scim-compatibility)

