---
title: 使用 What If 工具排查条件访问问题 - Azure Active Directory
description: 在何处查找应用了哪种条件访问策略以及应用的原因
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2020
ms.locfileid: "73175792"
---
# <a name="troubleshooting-conditional-access-using-the-what-if-tool"></a>使用 What If 工具排查条件访问问题

若要尝试了解为何在特定情况下向用户应用或不向用户应用某个策略，或者某个策略是否会在已知状态下应用，可以使用条件访问中的 [What If 工具](what-if-tool.md)。

What If 工具位于：“Azure 门户”   > “Azure Active Directory”   > “条件访问”   >   “What If”。

![默认状态下条件访问的 What If 工具](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-tool.png)

> [!NOTE]
> What If 工具目前不在“仅限报告”模式下评估策略。

## <a name="gathering-information"></a>收集信息

What If 工具只需要**用户**来启动。 

以下额外信息为可选，但有助于缩小特定案例的范围。

* 云应用或操作
* IP 地址 
* 国家/地区
* 设备平台
* 客户端应用（预览）
* 设备状态（预览） 
* 登录风险

此信息可以从用户、用户设备或 Azure AD 登录日志收集。

## <a name="generating-results"></a>生成结果

输入在上一部分收集的条件，然后选择“What If”以生成结果列表。  

可以随时选择“重置”来清除任何条件输入并返回到默认状态。 

## <a name="evaluating-results"></a>评估结果

### <a name="policies-that-will-apply"></a>将应用的策略

此列表将显示在给定条件的情况下，会应用哪些条件访问策略。 列表会包含应用的授予和会话控件。 例如，需要多重身份验证来访问特定应用程序。

### <a name="policies-that-will-not-apply"></a>不应用的策略

此列表将显示在应用条件的情况下，不会应用条件访问策略。 列表会包含某些策略及其不应用的原因。 示例包含可能会从策略中排除的用户和组。

## <a name="use-case"></a>用例

许多组织创建策略的根据是网络位置、允许受信任的位置，以及阻止不应进行访问的位置。

若要验证某项配置是否合适，管理员可以使用 What If 工具从应该允许的位置和应该拒绝的位置进行模拟访问。

![What If 工具，其显示的结果为阻止访问](./media/troubleshoot-conditional-access-what-if/conditional-access-what-if-results.png)

在此实例中，用户在去朝鲜旅行时，系统会阻止用户访问任何云应用，因为 Contoso 已阻止从该位置进行访问。

此测试可以在扩展后纳入其他数据点，以便缩小范围。

## <a name="next-steps"></a>后续步骤

* [什么是条件访问？](overview.md)
* [Azure Active Directory 标识保护是什么？](../identity-protection/overview-v2.md)
* [什么是设备标识？](../devices/overview.md)
* [工作原理：Azure 多重身份验证](../authentication/concept-mfa-howitworks.md)
