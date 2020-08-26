---
title: 限制 Azure 自动化更新管理部署范围
description: 本文介绍如何使用作用域配置来限制更新管理部署的范围。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: aafd284122ca61ba2b668186095b88003be2775c
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449902"
---
# <a name="limit-update-management-deployment-scope"></a>限制更新管理部署范围

本文介绍如何在使用[更新管理](update-mgmt-overview.md)功能向 vm 部署更新和修补程序时使用作用域配置。 有关详细信息，请参阅[以 Azure Monitor （预览版）中的监视解决方案为目标](../../azure-monitor/insights/solution-targeting.md)。

## <a name="about-scope-configurations"></a>关于作用域配置

作用域配置是一个或多个已保存搜索的组（查询），用于将更新管理的作用域限制为特定的计算机。 作用域配置在 "Log Analytics" 工作区中使用，以面向要启用的计算机。 添加计算机以接收更新管理中的更新时，还会将计算机添加到工作区中的已保存搜索。

## <a name="set-the-scope-limit"></a>设置作用域限制

限制更新管理部署的作用域：

1. 在自动化帐户中，选择 "**相关资源**" 下的 "**链接的工作区**"。

2. 选择“转到工作区”。

3. 选择 "**工作区数据源**" 下的 "**作用域配置（预览）** "。

4. 选择 `MicrosoftDefaultScopeConfig-Updates` 作用域配置右侧的省略号，然后选择 "**编辑**"。

5. 在编辑窗格中，展开 "**选择计算机组**"。 “计算机组”窗格将显示用来创建作用域配置的已保存搜索。 更新管理使用的已保存的搜索是：

    |名称     |类别  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

6. 选择已保存的搜索，查看和编辑用于填充组的查询。 下图显示了查询及其结果：

    [![保存的搜索](./media/update-mgmt-scope-configuration/logsearch.png)](./media/update-mgmt-scope-configuration/logsearch-expanded.png#lightbox)

## <a name="next-steps"></a>后续步骤

可以[查询 Azure Monitor 日志](update-mgmt-query-logs.md)来分析更新评估、部署和其他相关的管理任务。 它包含预定义的查询来帮助您入门。
