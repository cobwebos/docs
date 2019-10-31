---
title: 使用 What If 工具对条件访问进行故障排除-Azure Active Directory
description: 在何处可以找到应用的条件访问策略和原因
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 07/03/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 661afc08c76c6cde61b02a29a55b4a8bec932e21
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175792"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>使用 What If 工具排查条件访问问题

在特定情况下，如果在特定情况下，或者策略将应用于已知状态，则条件访问中的[What If 工具](what-if-tool.md)功能非常强大。

What If 工具位于**Azure 门户** > **Azure Active Directory** >  > **条件访问**What If **。**

![默认状态下的条件性访问 What If 工具](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> What If 工具当前不在仅报告模式下评估策略。

## <a name="gathering-information"></a>正在收集信息

What If 工具仅要求**用户**开始使用。 

以下附加信息是可选的，但将有助于缩小特定情况的范围。

* 云应用或操作
* IP 地址 
* 国家/地区
* 设备平台
* 客户端应用（预览）
* 设备状态（预览） 
* 登录风险

此信息可从用户、其设备或 Azure AD 登录日志中收集。

## <a name="generating-results"></a>生成结果

输入在上一部分收集的条件，然后选择 " **What If** " 生成结果列表。 

随时可以选择 "**重置**" 以清除任何条件输入并返回到默认状态。

## <a name="evaluating-results"></a>评估结果

### <a name="policies-that-will-apply"></a>要应用的策略

此列表将显示给定条件适用的条件性访问策略。 此列表将包括适用的 grant 和 session 控件。 例如，需要多重身份验证才能访问特定的应用程序。

### <a name="policies-that-will-not-apply"></a>将不应用的策略

此列表将显示条件访问策略，如果应用条件，这些策略将不适用。 此列表将包含任何策略和不适用的原因。 例如，可以从策略中排除的用户和组。

## <a name="use-case"></a>用例

许多组织基于网络位置创建策略，允许受信任的位置和阻止访问不应发生的位置。

若要验证是否已正确配置，管理员可以使用 What If 工具，从应该允许的位置和应拒绝的位置模拟访问权限。

![What If 工具显示带有阻止访问的结果](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

在这种情况下，用户将被阻止访问到韩国的任何云应用，因为 Contoso 阻止了该位置的访问。

此测试可以扩展以合并其他数据点以缩小范围。

## <a name="next-steps"></a>后续步骤

* [什么是条件性访问？](overview.md)
* [什么是 Azure Active Directory Identity Protection？](../identity-protection/overview-v2.md)
* [什么是设备标识？](../devices/overview.md)
* [工作原理：Azure 多重身份验证](../authentication/concept-mfa-howitworks.md)
