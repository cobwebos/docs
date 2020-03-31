---
title: 关闭非托管 Azure AD 目录中的工作或学校帐户
description: 如何在非托管 Azure 活动目录中关闭工作或学校帐户。
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
ms.openlocfilehash: 3c101c0ef7932151e675c5c514ac558e6e0f94b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73815717"
---
# <a name="close-your-work-or-school-account-in-an-unmanaged-directory"></a>关闭非托管目录中的工作或学校帐户

如果您是非托管 Azure 活动目录 （Azure AD） 组织中的用户，并且不再需要使用该组织中的应用或维护与它的任何关联，则可以随时关闭帐户。 非托管目录没有全局管理员。 非托管目录中的用户可以自行关闭其帐户，而无需与管理员联系。

非托管目录中的用户通常在自助服务注册期间创建。 例如，组织中的信息工作者注册了免费服务。 有关自助服务注册的详细信息，请参阅[Azure 活动目录的自助服务注册是什么？](directory-self-service-signup.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="before-you-begin"></a>开始之前

在关闭帐户之前，应确认以下项目：

* 请确保您是非托管 Azure AD 目录的用户。 如果您属于托管目录，则无法关闭帐户。 如果您属于托管目录并希望关闭您的帐户，则必须与管理员联系。 有关如何确定是否属于非托管目录的信息，请参阅[从非托管租户中删除用户](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)。

* 保存要保留的任何数据。 有关如何提交导出请求的信息，请参阅[访问和导出非托管租户的系统生成的日志](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)。

> [!WARNING]
> 关闭您的帐户是不可逆的。 当您关闭帐户时，所有个人数据都将被删除。 您将不再有权访问您的帐户和与您的帐户关联的数据。

## <a name="close-your-account"></a>关闭你的帐户

要关闭非托管工作或学校帐户，请按照以下步骤操作：

1. 使用要关闭的帐户登录以[关闭您的帐户](https://go.microsoft.com/fwlink/?linkid=873123)。

1. 在 **"我的数据请求"** 上，选择 **"关闭帐户**"。

    ![我的数据请求 - 关闭帐户](./media/users-close-account/close-account.png)

1. 查看确认消息，然后选择 **"是**"。

    ![我的数据请求 - 确认关闭](./media/users-close-account/confirm-close.png)

## <a name="next-steps"></a>后续步骤

- [什么是 Azure 活动目录的自助服务注册？](directory-self-service-signup.md)
- [从非托管租户删除用户](https://docs.microsoft.com/flow/gdpr-dsr-delete#delete-the-user-from-unmanaged-tenant)
- [访问和导出非托管租户的系统生成日志](https://docs.microsoft.com/power-platform/admin/powerapps-gdpr-dsr-guide-systemlogs#accessing-and-exporting-system-generated-logs-for-unmanaged-tenants)
