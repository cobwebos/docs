---
title: 迁移条件访问策略 - Azure Active Directory
description: 了解在 Azure 门户中迁移经典策略所要掌握的知识。
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/04/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1811d5b9ae4d3e34b48e1cdc156438f2bad98d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90601648"
---
# <a name="conditional-access-classic-policy-migration"></a>条件访问经典策略迁移

Azure Active Directory 使用条件访问作为一种工具来统合信号、做出决策，以及实施组织策略。 条件访问是新的标识驱动控制平面的核心。 尽管此功能的用途仍未变化，但新版 Azure 门户在条件访问的工作原理方面引入了重大改进。

考虑迁移未在 Azure 门户中创建的策略，因为：

- 现在可以解决以前无法处理的方案。
- 可以通过合并来减少需要管理的策略数。
- 可以在一个中心位置管理所有条件访问策略。
- Azure 经典门户即将停用。

本文介绍在将现有条件访问策略迁移到新框架时所要了解的知识。

## <a name="classic-policies"></a>经典策略

在 [Azure 门户](https://portal.azure.com)中，可以在“Azure Active Directory”   > “安全性”   > “条件访问”  下找到条件访问策略。 组织还可能有不使用此页创建的较旧的条件访问策略。 这些策略称为“经典策略”。  经典策略是在以下位置创建的条件访问策略：

- Azure 经典门户
- Intune 经典门户
- Intune 应用保护门户

在“条件访问”页上，可以通过单击“管理”部分中的“[**经典策略**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/ClassicPolicies)”来访问经典策略。   

![Azure AD 中的条件访问，显示经典策略视图](./media/policy-migration/71.png)

“经典策略”视图提供了一个选项用于执行以下操作： 

- 筛选经典策略。
- 禁用经典策略。
- 查看经典策略的设置，将其禁用。

   ![经典策略详细信息，包括现有策略配置](./media/policy-migration/74.png)

> [!WARNING]
> 经典策略在禁用后将无法重新启用。

经典策略的详细信息视图用于记录设置、修改包含或排除的组以及禁用策略。

![策略详细信息 - 要包括或排除的组](./media/policy-migration/75.png)

通过更改选定的组或排除特定的组，可以在针对所有包含的用户和组禁用某个策略之前，针对少量测试用户来测试禁用该策略所产生的影响。
 
## <a name="migration-considerations"></a>迁移注意事项

在本文中，Azure AD 条件访问策略也称为“新策略”。 
在禁用或删除经典策略之前，这些策略与新策略仍可同时运行。 

在策略整合的上下文中，必须注意以下几个方面：

- 尽管经典策略与特定的云应用相关联，但你可以根据需要在新策略中选择任意数量的云应用。
- 云应用的经典策略和新策略控制要求满足所有控制条件 (*AND*)。 
- 在新策略中，可以：
   - 根据方案的需要组合多个条件。 
   - 选择多个授予要求作为访问控制，并使用逻辑 *OR*（要求满足选定控制条件中的一个）或逻辑 *AND*（要求满足所有选定控制条件）来合并这些要求。

### <a name="exchange-online"></a>Exchange Online

如果你想要迁移包含**Exchange Active Sync**作为客户端应用条件的**Exchange online**经典策略，你可能无法将其合并到一个新策略。 

例如，如果希望支持所有客户端应用类型，则无法进行这种整合。 在将 **Exchange Active Sync** 用作客户端应用条件的新策略中，无法选择其他客户端应用。

![选择客户端应用的条件访问](./media/policy-migration/64.png)

如果经典策略包含多个条件，则也无法整合到一个新策略。 配置为将 **Exchange Active Sync** 用作客户端应用条件的新策略不支持其他条件：   

![Exchange ActiveSync 不支持所选条件](./media/policy-migration/08.png)

如果某个新策略配置为将 **Exchange Active Sync** 用作客户端应用条件，需确保未配置其他所有条件。 

![条件访问条件](./media/policy-migration/16.png)
 
用于 Exchange Online 的基于应用的经典策略，其中包括作为客户端应用条件的**Exchange Active Sync** ，它允许支持的和**不受****支持**的设备平台。 尽管无法在相关的新策略中配置单个设备平台，但可以将支持状态限制为[受支持的设备平台](concept-conditional-access-conditions.md#device-platforms)。 

![条件访问选择 Exchange ActiveSync](./media/policy-migration/65.png)

可以整合多个将 **Exchange Active Sync** 包含为客户端应用条件的经典策略，前提是这些策略：

- 只将 **Exchange Active Sync** 用作条件 
- 针对授予访问权限配置了多项要求

一种常见方案是整合：

- Azure 经典门户中基于设备的经典策略 
- Intune 应用保护门户中基于应用的经典策略 
 
在这种情况下，可将经典策略整合到一个已选择上述两项要求的新策略。

![条件访问授予控件](./media/policy-migration/62.png)

### <a name="device-platforms"></a>设备平台

包含基于应用的控制条件的经典策略中已预先将 iOS 和 Android 配置为设备平台条件。 

在新策略中，需要选择希望单独支持的[设备平台](concept-conditional-access-conditions.md#device-platforms)。

![条件访问设备平台选择](./media/policy-migration/41.png)

## <a name="next-steps"></a>后续步骤

- [在仅限报告模式下进行条件访问来确定新策略决定的影响。](concept-conditional-access-report-only.md)
- 若要了解如何配置条件访问策略，请参阅[条件访问常见策略](concept-conditional-access-policy-common.md)。
- 如果已准备好为环境配置条件访问策略，请参阅文章 [如何：在 Azure Active Directory 中规划条件访问部署](plan-conditional-access.md)。 
