---
title: 关闭非托管目录中的工作或学校帐户-Azure Active Directory |Microsoft Docs
description: 如何在非托管 Azure Active Directory 中关闭工作或学校帐户。
services: active-directory
author: rolyon
manager: mtillman
ms.service: active-directory
ms.subservice: users-groups-roles
ms.topic: article
ms.workload: identity
ms.date: 05/20/2019
ms.author: rolyon
ms.reviewer: ''
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0da4c6b1c1434dae564ab4876a3ab3f341a87097
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891951"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>关闭非托管目录中的工作或学校帐户

如果你是非托管 Azure Active Directory (Azure AD) 组织中的用户, 并且不再需要使用该组织提供的应用或保持与它的关联, 你可以随时关闭帐户。 非托管目录没有全局管理员。 非托管目录中的用户可以自行关闭自己的帐户, 而无需联系管理员。

非托管目录中的用户通常是在自助注册期间创建的。 例如, 组织中的信息工作者可以注册免费服务。 有关自助注册的详细信息, 请参阅[什么是 Azure Active Directory 的自助注册？](directory-self-service-signup.md)。

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>开始之前

你应确认以下各项, 然后才能关闭你的帐户:

* 请确保您是非托管 Azure AD 目录的用户。 如果你属于托管目录, 则无法关闭你的帐户。 如果你属于托管目录并想要关闭你的帐户, 则必须与你的管理员联系。 有关如何确定是否属于非托管目录的信息, 请参阅[从非托管租户中删除用户](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)。

* 保存任何要保留的数据。 有关如何提交导出请求的信息, 请参阅[为非托管租户访问和导出系统生成的日志](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)。

> [!WARNING]
> 关闭帐户是不可逆的。 当你关闭帐户时, 将删除所有个人数据。 你将无法再访问你的帐户和与你的帐户关联的数据。

## <a name="close-your-account"></a>关闭你的帐户

若要关闭非托管的工作或学校帐户, 请执行以下步骤:

1. 使用你要关闭的帐户登录以[关闭你的帐户](https://go.microsoft.com/fwlink/?linkid=873123)。

1. 在**我的数据请求**中, 选择 "**关闭帐户**"。

    ![我的数据请求-关闭帐户](./media/users-close-account/close-account.png)

1. 查看确认消息, 然后选择 **"是"** 。

    ![我的数据请求-确认关闭](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>后续步骤

- [什么是 Azure Active Directory 的自助注册？](directory-self-service-signup.md)
- [从非托管租户中删除用户](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [针对非托管租户访问和导出系统生成的日志](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
