---
title: 限制 Azure 自动化更改跟踪和清单部署范围
description: 本文介绍如何使用作用域配置来限制更改跟踪和清单部署的范围。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 49655d11858086b16099a1864fd4d2dc5988f02a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84117427"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>限制更改跟踪和清单部署范围

本文介绍如何使用[更改跟踪和清单](change-tracking.md)功能将更改部署到 vm 时使用作用域配置。 有关详细信息，请参阅[以 Azure Monitor （预览版）中的监视解决方案为目标](https://docs.microsoft.com/azure/azure-monitor/insights/solution-targeting)。 

## <a name="about-scope-configurations"></a>关于作用域配置

作用域配置是一个或多个已保存搜索的组（查询），用于将更改跟踪和清单的作用域限制为特定的计算机。 作用域配置在 "Log Analytics" 工作区中使用，以面向要启用的计算机。 将计算机添加到功能的更改时，还会将计算机添加到工作区中的已保存搜索。

## <a name="set-the-scope-limit"></a>设置作用域限制

限制更改跟踪和清单部署的作用域：

1. 在自动化帐户中，选择 "**相关资源**" 下的 "**链接的工作区**"。

2. 单击 "**前往工作区**"。

3. 选择 "**工作区数据源**" 下的 "**作用域配置（预览）** "。

4. 选择 `MicrosoftDefaultScopeConfig-ChangeTracking` 作用域配置右侧的省略号，然后单击 "**编辑**"。 

5. 在“编辑”窗格中，选择“选择计算机组”。 “计算机组”窗格将显示用来创建作用域配置的已保存搜索。 更改跟踪和清单使用的已保存搜索如下：

    |名称     |类别  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. 选择已保存的搜索，查看和编辑用于填充组的查询。 下图显示了查询及其结果：

    ![保存的搜索](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>后续步骤

* 要使用更改跟踪和库存，请参阅[管理更改跟踪和库存](change-tracking-file-contents.md)。
* 要排查常见功能问题，请参阅[排查更改跟踪和库存问题](troubleshoot/change-tracking.md)。