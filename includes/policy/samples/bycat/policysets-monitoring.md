---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 504005d9d0a037640a959ca4861971d0ca90823c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234022"
---
|名称 |说明 |策略 |版本 |
|---|---|---|---|
|[启用用于虚拟机规模集的 Azure Monitor](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |在指定范围（管理组、订阅或资源组）内启用用于虚拟机规模集的 Azure Monitor。 将 Log Analytics 工作区用作参数。 注意：如果规模集 upgradePolicy 设置为“Manual”，则需要通过对规模集调用升级将扩展应用到集中的所有 VM。 在 CLI 中，这将是 az vmss update-instances。 |6 |1.0.1 |
|[启用用于 VM 的 Azure Monitor](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |在指定范围（管理组、订阅或资源组）内为虚拟机 (VM) 启用 Azure Monitor。 将 Log Analytics 工作区用作参数。 |10 |2.0.0 |
