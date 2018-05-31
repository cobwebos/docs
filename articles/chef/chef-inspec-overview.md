---
title: 使用 InSpec 实现 Azure 基础结构的符合性自动化
description: 了解如何使用 InSpec 检测 Azure 部署中的问题
keywords: azure, chef, devops, 虚拟机, 概述, 自动化, inspec
ms.service: virtual-machines-linux
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 05/15/2018
ms.topic: article
ms.openlocfilehash: 4193b7fdb3932cbffa2b56b5d7eee6f3b573bd99
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2018
ms.locfileid: "34259490"
---
# <a name="use-inspec-for-compliance-automation-of-your-azure-infrastructure"></a>使用 InSpec 实现 Azure 基础结构的符合性自动化
[InSpec](https://www.chef.io/inspec/) 是一种免费的开源框架，用于测试和审核应用程序和基础结构。 InSpec 的工作原理是：将系统的实际状态与你在易读且易写的 InSpec 代码中表述的期望状态进行比较。 InSpec 会检测冲突情况并以报告的形式显示发现结果，但由你来控制如何修正。 可以使用 InSpec 来验证在 Azure 中运行的虚拟机的状态。 也可使用 InSpec 来扫描并验证订阅中的资源和资源组的状态。

本文介绍使用 InSpec 来简化 Azure 上的安全性和符合性措施的优点。

## <a name="make-compliance-easy-to-understand-and-assess"></a>使符合性易于理解和访问
可以使用 InSpec 将要求转换成具有版本控制的、可执行的、人类可读的代码。 这样就可以将测试整理成可编写的配置文件，以便根据需要来定义和自定义异常。

## <a name="detect-fleet-wide-issues-and-prioritize-their-remediation"></a>检测群体范围的问题并设置其修复措施的优先级
InSpec 无代理检测模式可以用来快速地大规模评估暴露级别。 可以通过内置的用于影响/严重性评分的元数据来确定进行修正时需要关注的领域。

## <a name="inspect-machines-data-and-new-saas-apis"></a>检查计算机、数据和新的 SaaS API
可以使用 InSpec 云 API 符合性功能对云符合性进行粗浅和细致的断言，并持续进行相关报告。

## <a name="satisfy-audits"></a>满足审核
使用 InSpec 可以随时对审核问题进行响应，不仅仅是按预先确定的时间间隔（例如每季或每年）进行响应。 有了 InSpec，就可以在进入审核周期时了解具体的符合性状态，而不会对审核员的结果感到吃惊。

## <a name="reduce-ambiguity-and-miscommunication-regarding-rules"></a>减轻/减少与规则有关的模糊性和错误理解
文档形式的配置和进程可以有不同的解释。 使用可执行代码时，则不需讨论应该完成哪些评估才适合进行意图明确的有形测试。

## <a name="keep-up-with-rapidly-changing-threat-and-compliance-landscapes"></a>适应瞬息万变的威胁和符合性情况
InSpec 可以做到在同一天编写和发布检测代码，并可编写新的规则来快速响应新的法规。 这意味着，威胁或法规方面的变化不再构成突发事件。

## <a name="next-steps"></a>后续步骤
* [在 Azure 上使用 Chef 创建 Windows 虚拟机](/azure/virtual-machines/windows/chef-automation)