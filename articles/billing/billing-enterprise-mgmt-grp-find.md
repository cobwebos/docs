---
title: "查找 Azure 订阅或管理组 — Azure | Microsoft Docs"
description: "如何在多个目录间导航以查看管理组和订阅"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: df4d85d0556a62311c112f24431b54d042333c7f
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---

# <a name="find-an-azure-subscription-or-management-group"></a>查找 Azure 订阅或管理组

如果在 Azure 中找不到某个订阅或管理组，可能是在错误的目录中进行了查找。 当一个帐户存在于多个 Azure Active Directory 时，可能发生这种情况。 每个 [Active Directory 都是独立的](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-licensing-directory-independence)，目录之间不继承访问权限。      

![切换目录菜单](media/billing-enterprise-mgmt-groups/mgempty.png)



## <a name="switch-directories"></a>切换目录 
在 Azure 门户中可以轻松切换目录。
1.  登录到 [Azure 门户](https://portal.azure.com)。
2.  在屏幕右上角选择你的姓名。 
3.  菜单底部列出有权访问的所有目录。
4.  选择要访问的目录的名称。 

![切换目录菜单](media/billing-enterprise-mgmt-groups/switch-directory.png)

## <a name="asset-is-unavailable"></a>资产不可用？ 
如果在尝试访问订阅或管理组时收到错误消息“此资产不可用”，则不具有正确角色来查看此项目。  

![asset-not-found](media/billing-enterprise-mgmt-groups/asset-not-found.png)

联系订阅或管理组的管理员以获得访问权限。  
* 对于订阅，请参考 [Azure 基于角色的访问控制 (RBAC)](https://docs.microsoft.com/en-us/azure/active-directory/role-based-access-control-configure) 文档，有助于了解需要哪个角色。
* 对于管理组，RBAC 访问不可用，但即将推出。 请联系企业门户管理员以分配访问权限。   

## <a name="improve-your-experience-with-management-groups-and-subscriptions-in-the-same-directory"></a>通过将管理组和订阅放在同一目录来改善体验 
可以将订阅或管理组转移到所选目录中，使所有内容位于同一位置。  将订阅和管理组整合到同一目录后，就不必切换目录，还允许继承策略。  


### <a name="transfer-your-subscriptions"></a>转移订阅 
可以将订阅移动到与管理组关联的目录。 通过让注册管理员将订阅转移到目标目录中的某个帐户，可以实现此移动。 若要了解详细信息，请登录[企业门户](https://ea.azure.com/helpdocs/changeAccountOwnerForASubscription)。


 







