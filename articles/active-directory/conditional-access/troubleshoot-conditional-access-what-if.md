---
title: 使用 What If 工具对条件访问进行故障排除 - Azure Active Directory
description: 在何处可以找到应用的条件访问策略和原因
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 08/07/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: eb51e8590e2859a9fe8692f79c5b1238ed5a2098
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88948175"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>使用 What If 工具对条件访问进行故障排除

当试图了解为什么在特定情况下将策略应用于用户或未应用于用户时，或者策略将以已知状态应用时，条件访问中的 [What If 工具](what-if-tool.md)非常有用。

What If 工具位于“Azure 门户” > “Azure Active Directory” > “条件访问” > “What If”   。

![默认状态下的条件访问 What If 工具](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> What If 工具当前不在“仅限报告”模式下评估策略。

## <a name="gathering-information"></a>收集信息

What If 工具只需要一名用户即可开始使用。 

以下附加信息是可选的，但将有助于缩小特定情况的范围。

* 云应用或操作
* IP 地址 
* 国家/地区
* 设备平台
* 客户端应用（预览）
* 设备状态（预览） 
* 登录风险

此信息可从用户、用户设备或 Azure AD 登录日志中收集。

## <a name="generating-results"></a>生成结果

输入上一部分中收集的条件，然后选择“What If”以生成结果列表。 

随时可以选择“重置”清除输入的所有条件并返回默认状态。

## <a name="evaluating-results"></a>评估结果

### <a name="policies-that-will-apply"></a>要应用的策略

此列表将显示给定条件适用的条件访问策略。 此列表将包括适用的授权和会话控制。 例如，需要多重身份验证才能访问特定的应用程序。

### <a name="policies-that-will-not-apply"></a>不会应用的策略

此列表将显示在应用条件的情况下将不适用的条件访问策略。 此列表将包含任何策略和不适用的原因。 示例包括可从策略中排除的用户和组。

## <a name="use-case"></a>用例

许多组织都基于网络位置创建策略，允许受信任的位置并阻止无权访问的位置。

若要验证是否已正确配置，管理员可以使用 What If 工具来模拟从应允许的位置和从应拒绝的位置进行的访问。

[ ![What If 工具显示带有“阻止访问”的结果](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png#lightbox)

在这种情况下，由于 Contoso 已阻止从该位置进行访问，因此该用户在朝鲜旅行期间将被禁止访问任何云应用。

可以扩展此测试以合并其他数据点，从而缩小范围。

## <a name="next-steps"></a>后续步骤

* [什么是条件访问？](overview.md)
* [Azure Active Directory 标识保护是什么？](../identity-protection/overview-identity-protection.md)
* [什么是设备标识？](../devices/overview.md)
* [工作原理：Azure 多重身份验证](../authentication/concept-mfa-howitworks.md)一文