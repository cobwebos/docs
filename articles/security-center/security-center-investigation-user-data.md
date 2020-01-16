---
title: 管理在 Azure 安全中心调查中找到的用户数据
description: " 了解如何管理在 Azure 安全中心调查功能中找到的用户数据。 "
services: operations-management-suite
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/20/2018
ms.author: memildin
ms.openlocfilehash: 9e4c6577f0b8b18aff343ac54b31ff292632f5d0
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979242"
---
# <a name="manage-user-data-found-in-an-azure-security-center-investigation"></a>管理在 Azure 安全中心调查中找到的用户数据
本文提供有关如何管理在 Azure 安全中心调查功能中找到的用户数据的信息。 调查数据存储在[Azure Monitor 日志](../log-analytics/log-analytics-overview.md)中，并在安全中心公开。 管理用户数据包括导出或删除数据的能力。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

## <a name="searching-for-and-identifying-personal-data"></a>搜索并标识个人数据
在 Azure 门户中，可以使用安全中心的[调查功能](../security-center/security-center-investigation.md)搜索个人数据。 调查功能在“安全警报”下提供。

调查功能在“实体”选项卡下显示所有实体、用户信息和数据。

## <a name="securing-and-controlling-access-to-personal-information"></a>保护和控制对个人信息的访问
分配了读者、所有者、参与者或帐户管理员角色的安全中心用户可以在该工具中访问客户数据。

若要详细了解读者、所有者和参与者角色，请参阅[针对 Azure 基于角色的访问控制的内置角色](../role-based-access-control/built-in-roles.md)。 若要了解有关帐户管理员角色的详细信息，请参阅 [Azure 订阅管理员](../cost-management-billing/manage/add-change-subscription-administrator.md)。

## <a name="deleting-personal-data"></a>删除个人数据
分配了所有者、参与者或帐户管理员角色的安全中心用户可以删除调查信息。

若要删除调查，可以将 `DELETE` 请求提交到 Azure 资源管理器 REST API：

```HTTP
DELETE
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents/{incidentName}
```

可以通过使用 `GET` 请求列出所有事件找到 `incidentName` 输入：

```HTTP
GET
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/features/security/incidents
```

## <a name="exporting-personal-data"></a>导出个人数据
分配了所有者、参与者或帐户管理员角色的安全中心用户可以导出调查信息。 若要导出调查信息，请转到“实体”选项卡复制和粘贴相关信息。

## <a name="next-steps"></a>后续步骤
有关管理用户数据的详细信息，请参阅[管理 Azure 安全中心的用户数据](security-center-privacy.md)。
若要了解有关在 Azure Monitor 日志中删除私有数据的详细信息，请参阅[如何导出和删除私有数据](../azure-monitor/platform/personal-data-mgmt.md#how-to-export-and-delete-private-data)。
