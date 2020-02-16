---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 05f9b653910dcbe39b937c711d0045fd3ee33d4e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192533"
---
|名称 |说明 |作用 |版本 |
|---|---|---|---|
|[将标记添加到资源组](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/AddTag_ResourceGroup_Modify.json) |创建或更新任何缺少此标记的资源组时添加指定的标记和值。 可以通过触发修正任务来修正现有资源组。 如果存在具有不同值的标记，则不会更改该资源组。 |modify |1.0.0 |
|[将标记添加到资源](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/AddTag_Modify.json) |创建或更新任何缺少此标记的资源时添加指定的标记和值。 可以通过触发修正任务来修正现有资源。 如果存在具有不同值的标记，则不会更改该资源组。 而不会修改资源组上的标记。 |modify |1.0.0 |
|[在资源组中添加或替换标记](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/AddOrReplaceTag_ResourceGroup_Modify.json) |创建或更新任何资源组时添加或替换指定的标记和值。 可以通过触发修正任务来修正现有资源组。 |modify |1.0.0 |
|[在资源中添加或替换标记](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/AddOrReplaceTag_Modify.json) |创建或更新任何资源时添加或替换指定的标记和值。 可以通过触发修正任务来修正现有资源。 而不会修改资源组上的标记。 |modify |1.0.0 |
|[追加标记及其默认值](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/ApplyTag_Append.json) |创建或更新任何缺少此标记的资源时追加指定的标记和值。 在更改这些资源之前，请不要修改应用此策略之前创建的资源的标记。 不要应用到资源组。 可以使用新的“modify”效果策略来支持对现有资源中的标记进行修正 (请参阅 https://aka.ms/modifydoc) 。 |append |1.0.0 |
|[将标记及其默认值追加到资源组](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/ResourceGroupApplyTag_Append.json) |创建或更新任何缺少此标记的资源组时追加指定的标记和值。 在更改这些资源组之前，请不要修改应用此策略之前创建的资源组的标记。 可以使用新的“modify”效果策略来支持对现有资源中的标记进行修正 (请参阅 https://aka.ms/modifydoc) 。 |append |1.0.0 |
|[从资源组追加标记及其值](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/InheritTag_Append.json) |创建或更新任何缺少此标记的资源时，从资源组追加指定的标记及其值。 在更改这些资源之前，请不要修改应用此策略之前创建的资源的标记。 可以使用新的“modify”效果策略来支持对现有资源中的标记进行修正 (请参阅 https://aka.ms/modifydoc) 。 |append |1.0.0 |
|[从资源组继承标记](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/InheritTag_AddOrReplace_Modify.json) |创建或更新任何资源时，添加或替换父资源组中指定的标记和值。 可以通过触发修正任务来修正现有资源。 |modify |1.0.0 |
|[从资源组继承标记（如果缺少此标记）](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/InheritTag_Add_Modify.json) |创建或更新任何缺少此标记的资源时，从父资源组添加指定的标记及其值。 可以通过触发修正任务来修正现有资源。 如果存在具有不同值的标记，则不会更改该资源组。 |modify |1.0.0 |
|[需要指定的标记](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/RequireTag_Deny.json) |强制要求存在某个标记。 不要应用到资源组。 |deny |1.0.0 |
|[要求资源组中存在指定的标记](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/ResourceGroupRequireTag_Deny.json) |强制要求资源组中存在某个标记。 |deny |1.0.0 |
|[需要标记及其值](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/RequireTagAndValue_Deny.json) |强制执行所需的标记及其值。 不要应用到资源组。 |deny |1.0.0 |
|[要求资源组中存在标记及其值](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Tags/ResourceGroupRequireTagAndValue_Deny.json) |强制要求资源组中存在所需的标记及其值。 |deny |1.0.0 |
