---
title: Azure 证明声明规则语法
description: 有关 Azure 证明策略声明和声明规则的详细信息。
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91285412"
---
# <a name="claim-and-claim-rules"></a>声明和声明规则

若要了解声明规则的语法，必须首先了解证明策略声明。

## <a name="claim"></a>声明

声明是组合在一起以提供相关信息的一组属性。 对于 Azure 证明，声明包含以下属性：

- **类型**：表示声明类型的字符串值。
- **value**：表示声明值的布尔值、整数值或字符串值。
- **valueType**：存储在 value 属性中的信息的数据类型。 支持的类型有 String、Integer 和 Boolean。 如未定义，则默认值将为“String”。
- **issuer**：有关声明的颁发者的信息。 颁发者将为以下类型之一：
  - **AttestationService**：某些声明由 Azure 证明提供给策略作者，证明策略作者可以使用这些声明来创建相应的策略。
  - **AttestationPolicy**：此策略（由管理员定义）本身可以在处理过程中向传入证据添加声明。 本例中的颁发者设置为“AttestationPolicy”。
  - **CustomClaim**：证明者（客户端）还可以向证明证据添加其他声明。 本例中的颁发者设置为“CustomClaim”。

如未定义， 默认值将为“CustomClaim”。

## <a name="claim-rule"></a>声明规则

策略引擎使用传入声明集来计算证明结果。 声明规则是一组条件，用于验证传入声明并执行定义的操作。

```
Conditions list => Action (Claim)
```

声明规则的 Azure 证明评估涉及以下步骤：

- 如果条件列表不存在，请执行包含指定声明的操作 
- 如果存在，请评估条件列表中的条件。
- 如果条件列表的评估结果为 false，则停止。 否则，请继续。

声明规则中的条件用于确定是否需要执行该操作。 条件列表是由“&&”运算符分隔的一系列条件。

条件列表的结构如下：

```
Condition && Condition && ...
```

条件结构如下：

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

此条件列表由针对声明的各种属性的各个条件构成。 条件可以具有可选标识符，该标识符可用于引用满足条件的声明。 此引用可用于其他条件或同一规则的操作。

例如：

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

下面是可用于检查条件的运算符：

| Valuetype | 支持的运算 |
|--|--|
| Integer | ==（等于）、\!=（不等于）、<=（小于或等于）、<（小于）、>=（大于或等于）、>（大于） |
| 字符串 | ==（等于）、\!=（不等于） |
| 布尔 | ==（等于）、\!=（不等于） |

条件列表的评估：

- 如果存在“&&”运算符，则表明仅当列表中的所有条件都评估为 true 时，条件列表才会评估为 true。
- 条件表示对声明集的筛选条件。 如果至少找到了一个满足条件的声明，则条件本身的评估结果为 true。
- 如果声明的每个属性都满足条件中存在的相应声明属性条件，则该声明满足条件所表示的筛选条件。  

下面介绍策略中允许执行的操作集。

| 操作谓词 | 说明 | 适用的策略部分 |
|--|--|--|
| permit() | 传入声明集可用于计算 issuancerules。 不采用任何声明作为参数 | **authorizationrules** |
| deny() | 传入声明集不应用于计算 issuancerules。不采用任何声明作为参数 | **authorizationrules** |
| add(claim) | 向传入声明集添加声明。 向传入声明集添加的任何声明都可用于后续声明规则。 |**authorizationrules**、**issuancerules** |
| issue(claim) | 向传入和传出声明集添加声明 | **issuancerules** |
| issueproperty(claim) | 向传入和属性声明集添加声明 | **issuancerules**

## <a name="next-steps"></a>后续步骤

- [如何创作证明策略并对其签名](author-sign-policy.md)
- [使用 PowerShell 设置 Azure 证明](quickstart-powershell.md)

