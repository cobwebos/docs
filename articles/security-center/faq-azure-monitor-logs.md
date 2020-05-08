---
title: Azure 安全中心常见问题解答-有关现有 Log Analytics 代理的问题
description: 此常见问题回答了已使用 Log Analytics 代理并考虑使用 Azure 安全中心的客户提出的问题，该产品可帮助你预防、检测和应对威胁。
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/25/2020
ms.author: memildin
ms.openlocfilehash: 2fe306cf7d17f0789c5e134c3fcad3f8f07a0b80
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612820"
---
# <a name="faq-for-customers-already-using-azure-monitor-logs"></a>已使用 Azure Monitor 日志的客户的常见问题<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>安全中心是否会覆盖 VM 和工作区之间的任何现有连接？

如果 VM 已作为 Azure 扩展安装了 Log Analytics 代理，则安全中心不会覆盖现有的工作区连接。 相反，安全中心会使用现有工作区。 如果 "安全" 或 "SecurityCenterFree" 解决方案已安装在它所报告到的工作区中，则 VM 将受到保护。 

将在“数据收集”屏幕中选择的工作区上安装一个安全中心解决方案（如果尚不存在），而该解决方案只会应用到相关的 VM。 添加解决方案时，默认情况下会自动将它部署到连接到 Log Analytics 工作区的所有 Windows 和 Linux 代理。 [解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)可用于限定解决方案的范围。

> [!TIP]
> 如果 Log Analytics 代理直接安装在 VM 上（而不是作为 Azure 扩展），则安全中心不会安装 Log Analytics 代理，并且安全监视受到限制。

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>安全中心是否在现有 Log Analytics 工作区上安装解决方案？ 计费会产生什么影响？
安全中心确定 VM 已连接到所创建的工作区时，会根据定价层启用此工作区上的解决方案。 由于[解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)，解决方案仅应用于相关的 Azure VM，因此计费保持不变。

- 免费层**** – 安全中心在工作区中安装“SecurityCenterFree”解决方案。 免费层不会产生费用。
- **标准层** – 安全中心在工作区中安装“Security”解决方案。

   ![默认工作区上的解决方案](./media/security-center-platform-migration-faq/solutions.png)

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>环境中已存在工作区，是否可以将其用于收集安全数据？
如果 VM 已作为 Azure 扩展安装了 Log Analytics 代理，则安全中心会使用现有连接的工作区。 如果没有安全中心解决方案，则会在工作区安装，并且由于[解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)此解决方案仅适用于相关的 VM。

当安全中心在虚拟机上安装 Log Analytics 代理时，如果安全中心未指向现有工作区，则使用安全中心创建的默认工作区。

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>工作区已存在安全解决方案。 计费会产生什么影响？
Security & Audit 解决方案用于为 Azure Vm 启用安全中心标准层功能。 如果已在工作区上安装“安全性与审核”解决方案，则安全中心会使用现有解决方案。 计费方面没有任何更改。
