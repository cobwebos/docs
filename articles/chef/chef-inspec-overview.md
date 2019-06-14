---
title: 使用 InSpec 实现 Azure 基础结构的符合性自动化
description: 了解如何使用 InSpec 检测 Azure 部署中的问题
keywords: azure, chef, devops, virtual machines, overview, automate, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 03/19/2019
ms.topic: article
ms.openlocfilehash: bdfa30b48c79a8910d503bb9e54a42c30e5adba6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60629792"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>使用 InSpec 实现 Azure 基础结构的符合性自动化

[InSpec](https://www.chef.io/inspec/) 是 Chef 的开源语言，用于说明安全性和符合性规则，这些规则可在软件工程师、操作和安全工程师之间共享。 InSpec 的工作原理是：将基础结构的实际状态与你在易读且易写的 InSpec 代码中表述的期望状态进行比较。 InSpec 会检测冲突情况并以报告的形式显示发现结果，但由你来控制如何修正。

可以使用 InSpec 来验证资源和订阅，包括虚拟机、 网络配置和 Azure Active Directory 设置的详细信息中的资源组的状态。

本文介绍使用 InSpec 来简化 Azure 上的安全性和符合性措施的优点。

## <a name="make-compliance-easy-to-understand-and-assess"></a>使符合性易于理解和访问

编写在电子表格或 Word 文档中的符合性文档会使要求面临解释。 可以使用 InSpec 将要求转换成具有版本控制的、可执行的、人类可读的代码。 使用代码时，无需讨论应该完成哪些评估才适合进行意图明确的有形测试。

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>检测群体范围的问题并设置其修复措施的优先级

InSpec 的无代理检测模式可以用来快速地大规模评估暴露级别。 可以通过内置的用于影响/严重性评分的元数据来确定进行修正时需要关注的领域。 也可快速写入规则以响应新的漏洞或法规，并立即将其推出。

## <a name="audit-azure-virtual-machines-with-policy-guest-configuration"></a>审核策略来宾配置与 Azure 虚拟机

Azure 直接支持使用 Chef InSpec 定义要审核通过 Azure 虚拟机[Azure 策略来宾配置](/azure/governance/policy/concepts/guest-configuration)。 来宾配置 Linux 虚拟机提供的 Chef InSpec 定义和报表返回到 Azure 策略的符合性的计算结果。 这些审核的结果，还可通过 Azure Monitor 日志; 报告正在启用警报和其他自动化方案。

## <a name="satisfy-audits"></a>满足审核

使用 InSpec 可以随时对审核问题进行响应，不仅仅是按预先确定的时间间隔（例如每季或每年）进行响应。 通过持续运行 InSpec 测试，就可以进入审核周期，了解确切符合性状态和历史记录，而不会对审核员的结果感到惊讶。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [请尝试在 Azure Cloud Shell 中 InSpec](https://shell.azure.com)