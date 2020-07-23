---
title: 限制 Azure 自动化更新管理部署范围
description: 本文介绍如何使用作用域配置来限制更新管理部署的范围。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 8770762fa2d2ae6bc0584d75397829298a62e8c0
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2020
ms.locfileid: "86185885"
---
# <a name="limit-update-management-deployment-scope"></a>限制更新管理部署范围

本文介绍如何在使用[更新管理](automation-update-management.md)功能向 vm 部署更新和修补程序时使用作用域配置。 有关详细信息，请参阅将[监视解决方案定位到 Azure Monitor (预览版) ](../azure-monitor/insights/solution-targeting.md)。 

## <a name="about-scope-configurations"></a>关于作用域配置

作用域配置是一个或多个已保存搜索的组， (查询) 用于将更新管理范围限制为特定的计算机。 作用域配置在 "Log Analytics" 工作区中使用，以面向要启用的计算机。 添加计算机以接收更新管理中的更新时，还会将计算机添加到工作区中的已保存搜索。

## <a name="set-the-scope-limit"></a>设置作用域限制

限制更新管理部署的作用域：

1. 在自动化帐户中，选择 "**相关资源**" 下的 "**链接的工作区**"。

2. 单击 "**前往工作区**"。

3. 在 "**工作区数据源**" 下** (预览) 选择 "作用域配置**"。

4. 选择 `MicrosoftDefaultScopeConfig-Updates` 作用域配置右侧的省略号，然后单击 "**编辑**"。 

5. 在编辑窗格中，展开 "**选择计算机组**"。 “计算机组”窗格将显示用来创建作用域配置的已保存搜索。 更新管理使用的已保存的搜索是：

    |名称     |类别  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     | 更新        | Updates__MicrosoftDefaultComputerGroup         |

6. 选择已保存的搜索，查看和编辑用于填充组的查询。 下图显示了查询及其结果：

    ![保存的搜索](media/automation-scope-configurations-update-management/logsearch.png)

## <a name="next-steps"></a>后续步骤

* 要使用此功能，请参阅[管理 Azure VM 的更新和修补程序](automation-tutorial-update-management.md)。
* 若要对功能错误进行故障排除，请参阅[排查更新管理问题](troubleshoot/update-management.md)。
* 若要对 Windows 更新代理错误进行故障排除，请参阅[排查 Windows 更新代理问题](troubleshoot/update-agent-issues.md)。
* 若要对 Linux 更新代理错误进行故障排除，请参阅[排查 Linux 更新代理问题](troubleshoot/update-agent-issues-linux.md)。
