---
title: Azure 安全中心常见问题解答-有关现有 Mma 的问题
description: 此常见问题回答了已使用 Microsoft Monitoring Agent 的客户的问题，并考虑了 Azure 安全中心，这是一个可帮助你预防、检测和响应威胁的产品。
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
ms.openlocfilehash: 528ff47be2b18cb7d9b938e988383a9e81be67fb
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599504"
---
# 已使用 Azure Monitor 日志的客户的常见问题<a name="existingloganalyticscust"></a>

## <a name="does-security-center-override-any-existing-connections-between-vms-and-workspaces"></a>安全中心是否会覆盖 VM 和工作区之间的任何现有连接？

如果 VM 已将 Microsoft Monitoring Agent 作为 Azure 扩展进行安装，则安全中心不会覆盖现有工作区连接。 相反，安全中心会使用现有工作区。 如果 "安全" 或 "SecurityCenterFree" 解决方案已安装在它所报告到的工作区中，则 VM 将受到保护。 

安全中心解决方案安装在 "数据收集" 屏幕中所选的工作区（如果尚未存在），并且该解决方案仅应用于相关的 Vm。 添加解决方案时，默认情况下会自动将它部署到连接到 Log Analytics 工作区的所有 Windows 和 Linux 代理。 [解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)可用于限定解决方案的范围。

如果 Microsoft Monitoring Agent 是直接安装到 VM 上（而不是作为 Azure 扩展进行安装），那么安全中心不会安装 Microsoft Monitoring Agent，并且安全监视也会受限。

## <a name="does-security-center-install-solutions-on-my-existing-log-analytics-workspaces-what-are-the-billing-implications"></a>安全中心是否在现有 Log Analytics 工作区上安装解决方案？ 计费会产生什么影响？
安全中心确定 VM 已连接到所创建的工作区时，会根据定价层启用此工作区上的解决方案。 由于[解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)，解决方案仅应用于相关的 Azure VM，因此计费保持不变。

- 免费层 – 安全中心在工作区中安装“SecurityCenterFree”解决方案。 免费层不会计费。
- **标准层** – 安全中心在工作区中安装“Security”解决方案。

   ![默认工作区上的解决方案][1]

## <a name="i-already-have-workspaces-in-my-environment-can-i-use-them-to-collect-security-data"></a>环境中已存在工作区，是否可以将其用于收集安全数据？
如果 VM 已将 Microsoft Monitoring Agent 作为 Azure 扩展进行安装，则安全中心会使用现有连接的工作区。 如果没有安全中心解决方案，则会在工作区安装，并且由于[解决方案目标](../operations-management-suite/operations-management-suite-solution-targeting.md)此解决方案仅适用于相关的 VM。

安全中心在 VM 上安装 Microsoft Monitoring Agent 时，会使用安全中心创建的默认工作区。

## <a name="i-already-have-security-solution-on-my-workspaces-what-are-the-billing-implications"></a>工作区已存在安全解决方案。 计费会产生什么影响？
“安全性与审核”解决方案用于启用 Azure VM 的安全中心标准层功能。 如果已在工作区上安装“安全性与审核”解决方案，则安全中心会使用现有解决方案。 计费方面没有任何更改。


<!--Image references-->
[1]: ./media/security-center-platform-migration-faq/solutions.png