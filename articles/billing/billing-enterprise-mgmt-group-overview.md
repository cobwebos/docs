---
title: "使用 Azure 管理组来组织资源 - Azure | Microsoft Docs"
description: "了解管理组以及如何使用它们。"
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 482191ac-147e-4eb6-9655-c40c13846672
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: rithorn
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: 18541c68b02ae1b59ae4a6a85122dff614c9978c
ms.contentlocale: zh-cn
ms.lasthandoff: 09/25/2017

---


# <a name="organize-your-resources-with-azure-management-groups"></a>使用 Azure 管理组组织资源 

如果你有多个订阅，则可以将它们组织成名为“管理组”的容器，以帮助你跨订阅管理访问、策略、成本和合规性。 例如，你可以将策略应用于限制可以创建哪些资源类型的管理组。

> [!Note]
> 此功能目前处于个人预览状态。 [在此处注册](https://aka.ms/MGPreviewSignup)，让你的注册加入预览。   
 


管理组可以组织成层次结构。 显示的结构是可以存在的管理组层次结构的示例表示形式：


![层次结构树](media/billing-enterprise-mgmt-groups/tree.png)



## <a name="how-management-groups-are-related-to-your-azure-enterprise-enrollment"></a>管理组如何与 Azure 企业注册相关

管理团队简介是将[企业门户](https://ea.azure.com)功能转换到 [Azure 门户](https://portal.azure.com)的较大型过程中的一个步骤。

已创建管理组结构，因为它是在企业门户中定义的。 由注册、部门和帐户组成的整个层次结构映射到相应的管理组。 

以下是当前 EA 结构映射到管理组层次结构的方式。 

![层次结构树](media/billing-enterprise-mgmt-groups/tree2.png)

下表显示了来自企业门户的用户映射到管理组层次结构的方式。

|    EA 角色                                       |    映射的管理组节点上的角色    |    管理组节点上的权限                                                          |
|--------------------------------------------------|--------------------------------------------------|----------------------------------------------------------------------------------------------------|
|    EA 管理员                              |    资源策略参与者                   |    可以在注册节点及其下查看成本、管理资源策略以及查看层次结构    |
|    EA 管理员处于只读模式            |    计费读者                                |    可以在注册节点及其下读取成本和查看层次结构                              |
|    部门管理员                      |    计费读者                                |    可以在部门节点及其下读取成本和查看层次结构                                 |
|    部门管理员处于只读模式    |    计费读者                                |    可以在部门节点及其下读取成本和查看层次结构                                 |
|    帐户所有者                                 |    资源策略参与者                   |    可以在帐户节点及其下查看成本、管理资源策略，并查看层次结构       |




## <a name="view-management-groups-in-the-azure-portal"></a>在 Azure 门户中查看管理组

要在预览版中查看注册、部门或帐户，请使用欢迎电子邮件中的链接登录 Azure 门户。   

![层次结构](media/billing-enterprise-mgmt-groups/hierarchy.png)

### <a name="viewing-costs"></a>查看成本 
在管理组的详细信息屏幕上，你将看到本月当前累计成本。 这些成本基于使用情况，且不考虑预付金额、超额用量、已包含数量、调整和税。 对于与注册相对应的管理组，成本部分显示剩余的承诺量。  

![注册详细信息](media/billing-enterprise-mgmt-groups/enrollment.png)

 “单独计费的成本”是单独变更的月度累计，如不违背注册约定的商城、超额和其他费用。  有关成本细分的详细信息，请参阅[企业门户](https://ea.azure.com)。 

### <a name="enabling-access-to-costs"></a>启用成本访问
如果你未看到成本，请参阅我们的[排查企业成本查看问题](https://aka.ms/enableazurecosts)文档获取帮助。  

### <a name="delays-between-the-enterprise-portal-and-azure-portal"></a>企业门户与 Azure 门户之间的延迟 
* 预览期间，与企业门户中的值相比，Azure 门户中显示的数量可能会存在延迟。 此问题是临时的，并且正在处理。
* 在更新反映在 Azure 门户中之前，企业门户中的更新设置会延迟几分钟。 

## <a name="management-groups-have-a-relationship-with-your-directory"></a>管理组与目录存在关系   
与订阅一样，管理组与 Azure AD 也存在信任关系。 管理组层次结构信任单个目录以验证用户。 与管理组层次结构相关联的所有管理员必须属于同一目录。 

当注册层次结构映射到管理组时，将与单个目录建立信任关系。 在可能的情况下，选择与注册的用户帐户相关联的现有目录。 在某些情况下，会创建一个新目录，并将所有现有注册用户都邀请到该目录。 与注册订阅相关的目录不受影响。 因此，可能会在与订阅不同的目录中创建层次结构。 [详细了解](billing-enterprise-mgmt-grp-find.md)此过程如何影响层次结构及其订阅之间的导航体验。

## <a name="administering-your-management-groups"></a>管理你的管理组
Azure 门户中的管理组处于预览状态，并且在该初始版本中是只读的。 若要进行任何更新，请转到企业门户。 更新将自动反映在 Azure 门户中。 有关编辑和添加的帮助信息，请参阅企业门户中的文档。   

## <a name="policy-management"></a>策略管理
资源管理器可创建自定义策略来管理资源。 通过管理组，可以在层次结构中的任何级别分配策略，并且资源将继承这些策略。  [了解详细信息](https://go.microsoft.com/fwlink/?linkid=858942)

> [!Note]
> 无法跨目录强制实施策略。 



