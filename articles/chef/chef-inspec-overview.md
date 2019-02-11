---
title: 使用 InSpec 实现 Azure 基础结构的符合性自动化
description: 了解如何使用 InSpec 检测 Azure 部署中的问题
keywords: azure, chef, devops, virtual machines, overview, automate, inspec
ms.service: virtual-machines-linux
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: a5de2ca04a193f97a2a65a043f744abb8e0ea758
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359215"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>使用 InSpec 实现 Azure 基础结构的符合性自动化
[InSpec](https://www.chef.io/inspec/) 是 Chef 的开源语言，用于说明安全性和符合性规则，这些规则可在软件工程师、操作和安全工程师之间共享。 InSpec 的工作原理是：将基础结构的实际状态与你在易读且易写的 InSpec 代码中表述的期望状态进行比较。 InSpec 会检测冲突情况并以报告的形式显示发现结果，但由你来控制如何修正。

可使用 InSpec 来验证订阅中资源和资源组（包括虚拟机、网络配置、Azure Active Directory 设置等）的状态。

本文介绍使用 InSpec 来简化 Azure 上的安全性和符合性措施的优点。

## <a name="make-compliance-easy-to-understand-and-assess"></a>使符合性易于理解和访问
采用电子表格或 Word 文档撰写的符合性文档仍需进一步解释需求。 可以使用 InSpec 将要求转换成具有版本控制的、可执行的、人类可读的代码。 使用代码时，无需讨论应该完成哪些评估才适合进行意图明确的有形测试。

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>检测群体范围的问题并设置其修复措施的优先级
InSpec 的无代理检测模式可以用来快速地大规模评估暴露级别。 可以通过内置的用于影响/严重性评分的元数据来确定进行修正时需要关注的领域。 也可快速写入规则以响应新的漏洞或法规，并立即将其推出。

## <a name="satisfy-audits"></a>满足审核
使用 InSpec 可以随时对审核问题进行响应，不仅仅是按预先确定的时间间隔（例如每季或每年）进行响应。 通过持续运行 InSpec 测试，就可以进入审核周期，了解确切符合性状态和历史记录，而不会对审核员的结果感到惊讶。

## <a name="next-steps"></a>后续步骤

* 在 Azure Cloud Shell 中尝试 InSpec [![启动 Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
