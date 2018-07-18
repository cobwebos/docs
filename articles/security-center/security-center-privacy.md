---
title: 在 Azure 安全中心内管理用户数据 | Microsoft Docs
description: " 了解如何在 Azure 安全中心内管理用户数据。 "
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: terrylan
ms.openlocfilehash: 2495bae5c63cdafe049ec39f71ab53106c5f2df7
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34659172"
---
# <a name="manage-user-data-in-azure-security-center"></a>在 Azure 安全中心内管理用户数据
本文提供了有关如何在 Azure 安全中心内管理用户数据的信息。 管理用户数据包括访问、删除或导出数据的能力。

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

分配了读者、所有者、参与者或帐户管理员角色的安全中心用户可以在该工具中访问客户数据。 若要详细了解读者、所有者和参与者角色，请参阅[针对 Azure 基于角色的访问控制的内置角色](../role-based-access-control/built-in-roles.md)。 若要了解有关帐户管理员角色的详细信息，请参阅 [Azure 订阅管理员](../billing/billing-add-change-azure-subscription-administrator.md)。

## <a name="searching-for-and-identifying-personal-data"></a>搜索并标识个人数据
安全中心用户可以通过 Azure 门户查看其个人数据。 安全中心仅存储安全联系人详细信息，例如电子邮件地址和电话号码。 有关详细信息，请参阅[在 Azure 安全中心内提供安全联系人详细信息](security-center-provide-security-contact-details.md)。

在 Azure 门户中，用户可以使用安全中心的实时 VM 访问功能查看允许的 IP 配置。 有关详细信息，请参阅[使用恰时功能管理虚拟机访问](security-center-just-in-time.md)。

在 Azure 门户中，用户可以查看安全中心提供的安全警报，包括 IP 地址和攻击者详细信息。 有关详细信息，请参阅[管理和响应 Azure 安全中心内的安全警报](security-center-managing-and-responding-alerts.md)。

## <a name="classifying-personal-data"></a>对个人数据进行分类
不需要对在安全中心的安全联系人功能中发现的个人数据进行分类。 保存的数据是电子邮件地址（或多个电子邮件地址）和电话号码。 [联系人数据](security-center-provide-security-contact-details.md)由安全中心进行验证。

不需要对由安全中心的[实时](security-center-just-in-time.md)功能保存的 IP 地址和端口号进行分类。

只有分配有管理员角色的用户可以通过在安全中心内[查看警报](security-center-managing-and-responding-alerts.md)来对个人数据进行分类。

## <a name="securing-and-controlling-access-to-personal-data"></a>保护和控制对个人数据的访问
分配有读者、所有者、参与者或帐户管理员角色的安全中心用户可以访问[安全联系人数据](security-center-provide-security-contact-details.md)。

分配有读者、所有者、参与者或帐户管理员角色的安全中心用户可以访问其[实时](security-center-just-in-time.md)策略。

分配有读者、所有者、参与者或帐户管理员角色的安全中心用户可以查看其[警报](security-center-managing-and-responding-alerts.md)。

## <a name="updating-personal-data"></a>更新个人数据
分配有所有者、参与者或帐户管理员角色的安全中心用户可以通过 Azure 门户更新[安全联系人数据](security-center-provide-security-contact-details.md)。

分配有所有者、参与者或帐户管理员角色的安全中心用户可以更新其[实时策略](security-center-just-in-time.md)。

帐户管理员不能编辑警报事件。 [警报事件](security-center-managing-and-responding-alerts.md)被视为安全数据并且是只读的。

## <a name="deleting-personal-data"></a>删除个人数据
分配有所有者、参与者或帐户管理员角色的安全中心用户可以通过 Azure 门户删除[安全联系人数据](security-center-provide-security-contact-details.md)。

分配有所有者、参与者或帐户管理员角色的安全中心用户可以通过 Azure 门户删除[实时策略](security-center-just-in-time.md)。

安全中心用户不能删除警报事件。 由于安全需求，[警报事件](security-center-managing-and-responding-alerts.md)被视为只读数据。

## <a name="exporting-personal-data"></a>导出个人数据
分配有读者、所有者、参与者或帐户管理员角色的安全中心用户可以通过以下方式导出[安全联系人数据](security-center-provide-security-contact-details.md)：

- 从 Azure 门户中执行复制
- 执行 Azure REST API 调用、GET HTTP：
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
```

分配有帐户管理员角色的安全中心用户可以通过以下方式导出包含 IP 地址的[实时策略](security-center-just-in-time.md)：

- 从 Azure 门户中执行复制
- 执行 Azure REST API 调用、GET HTTP：
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
```

帐户管理员可以通过以下方式导出警报详细信息：

- 从 Azure 门户中执行复制
- 执行 Azure REST API 调用、GET HTTP：
```HTTP
GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
```

有关详细信息，请参阅[获取安全警报（获取集合）](https://msdn.microsoft.com/library/mt704050.aspx)。

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>限制在未经同意的情况下将个人数据用于分析或营销
安全中心用户可以选择通过删除其[安全联系人数据](security-center-provide-security-contact-details.md)来选择退出。

[实时数据](security-center-just-in-time.md)被视为非身份数据，并且保留 30 天。

[警报数据](security-center-managing-and-responding-alerts.md)被视为安全数据，并且保留两年。

## <a name="auditing-and-reporting"></a>审核和报告
安全联系人、实时数据和警报更新的审核日志保留在 [Azure 活动日志](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md)中。

## <a name="next-steps"></a>后续步骤
有关管理用户数据的详细信息，请参阅[管理在 Azure 安全中心调查中发现的用户数据](security-center-investigation-user-data.md)。
