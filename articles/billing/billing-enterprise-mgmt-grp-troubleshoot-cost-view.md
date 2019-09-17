---
title: 排查 Azure 企业成本查看问题
description: 了解如何解决 Azure 门户中可能遇到的有关组织成本查看的任何问题。
author: bandersmsft
manager: amberb
ms.service: billing
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/01/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 83f7f424b265582a3830c02973cbbb9962ddfbfb
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/11/2019
ms.locfileid: "67491265"
---
# <a name="troubleshoot-enterprise-cost-views"></a>排查企业成本查看问题

在企业注册中，存在几种可能导致注册中的用户无法查看成本的设置。  这些设置由注册管理员管理。 如果注册不是直接通过 Microsoft 购买的，则由合作伙伴管理。  本文可以帮助你了解具体设置以及它们对注册的影响。 这些设置独立于基于 Azure 角色的访问控制 (RBAC) 角色。

## <a name="enable-access-to-costs"></a>启用成本访问

你是否看到消息“未经授权”，或“在注册中已禁用成本查看”  。 在寻找成本信息？
![显示订阅当前成本字段中“未经授权”的屏幕截图。](media/billing-enterprise-mgmt-groups/unauthorized.png)

这可能是以下原因之一造成的：

1. 通过企业合作伙伴购买的 Azure，而该合作伙伴尚未发布定价信息。 请与合作伙伴联系，以在[企业门户](https://ea.azure.com)中更新定价设置。
2. 如果你是 EA Direct 客户，则有以下几种可能性：
    * 你是帐户所有者且你的注册管理员已禁用“AO 查看费用”设置  。  
    * 你是部门管理员且你的注册管理员已禁用“DA 查看费用”设置  。
    * 联系你的注册管理员以获取访问权限。 注册管理员可以在[企业门户](https://ea.azure.com/manage/enrollment)中更新设置。

      ![显示 Enterprise Portal 设置以供查看费用的屏幕截图。](media/billing-enterprise-mgmt-groups/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>资产不可用

如果在尝试访问订阅或管理组时收到错误消息指出“此资产不可用”  ，则不具有正确角色来查看此项目。  

![显示“资产不可用”消息的屏幕截图。](media/billing-enterprise-mgmt-groups/asset-not-found.png)

让 Azure 订阅或管理组管理员提供访问权限。 有关详细信息，请参阅[使用 RBAC 和 Azure 门户管理访问权限](../role-based-access-control/role-assignments-portal.md)。

## <a name="next-steps"></a>后续步骤
- 如有任何疑问或需要帮助，请[创建支持请求](https://go.microsoft.com/fwlink/?linkid=2083458)。
