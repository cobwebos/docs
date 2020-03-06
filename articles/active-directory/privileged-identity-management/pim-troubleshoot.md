---
title: 排查 Privileged Identity Management Azure Active Directory 的问题 |Microsoft Docs
description: 了解如何对 Azure AD Privileged Identity Management （PIM）中的角色进行系统错误的故障排除。
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/18/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 474f2634e6f7ddc1840548c39ae86cb54c3bf08e
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78299663"
---
# <a name="troubleshoot-a-problem-with-privileged-identity-management"></a>排查 Privileged Identity Management 的问题

Azure Active Directory （Azure AD）中的 Privileged Identity Management （PIM）是否有问题？ 下面的信息可帮助你将一切复原。

## <a name="access-to-azure-resources-denied"></a>拒绝访问 Azure 资源

### <a name="problem"></a>Problem

作为 Azure 资源的活动所有者或用户访问管理员，你可以查看 Privileged Identity Management 中的资源，但不能执行任何操作，例如进行符合条件的分配或查看资源的角色分配列表概述页。 其中任何一个操作都会导致授权错误。

### <a name="cause"></a>原因

如果不小心从订阅中删除 PIM 服务主体的 "用户访问管理员" 角色，则会出现此问题。 为了使 Privileged Identity Management 服务能够访问 Azure 资源，应始终为 MS PIM 服务主体分配 Azure 订阅的 "[用户访问管理员" 角色](../../role-based-access-control/built-in-roles.md#user-access-administrator)。

### <a name="resolution"></a>解决方法

在订阅级别将用户访问管理员角色分配给特权标识管理服务主体名称（MS – PIM）。 此分配应允许特权标识管理服务访问 Azure 资源。 根据你的要求，可以在管理组级别或订阅级别分配角色。 有关服务主体的详细信息，请参阅[将应用程序分配到角色](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application)。

## <a name="next-steps"></a>后续步骤

- [使用 Privileged Identity Management 的许可要求](subscription-requirements.md)
- [确保 Azure AD 中混合部署和云部署的特权访问安全性](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [部署 Privileged Identity Management](pim-deployment-plan.md)
