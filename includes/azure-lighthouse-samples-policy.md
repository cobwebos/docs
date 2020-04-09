---
title: include 文件
description: include 文件
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 1c0be8ed8c176fe32b8fe7fd420d65727c5288d2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/09/2020
ms.locfileid: "80986621"
---
这些示例演示如何将 Azure Policy 用于已为 Azure 委派资源管理而载入的订阅。

| **模板** | **说明** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | 分配一个策略，该策略为委派的订阅添加或删除标记（使用 modify 效果）。 有关详细信息，请参阅[部署可以在委派的订阅中修正的策略](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | 分配一个将用于审核委派分配的策略。 |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | 分配一个策略，该策略对委托订阅中的 Azure Key Vault 资源启用诊断（使用 deployIfNotExists 效果）。 有关详细信息，请参阅[部署可以在委派的订阅中修正的策略](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | 分配几个策略，以便对委派的订阅启用诊断，并将所有 Windows VM 和 Linux VM 连接到按策略创建的 Log Analytics 工作区。 有关详细信息，请参阅[部署可以在委派的订阅中修正的策略](../articles/lighthouse/how-to/deploy-policy-remediation.md)。 |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | 将计划（多个相关的策略定义）应用于委派的订阅。 |

