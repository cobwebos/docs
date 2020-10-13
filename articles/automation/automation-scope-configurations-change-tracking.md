---
title: 限制 Azure 自动化更改跟踪和清单部署范围
description: 本文介绍如何使用作用域配置来限制更改跟踪和清单部署的范围。
services: automation
ms.date: 03/04/2020
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 353f29d9b4c6599226619d40e4378e21618bcad4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "86185902"
---
# <a name="limit-change-tracking-and-inventory-deployment-scope"></a>限制更改跟踪和清单部署范围

本文介绍如何使用 [更改跟踪和清单](change-tracking.md) 功能将更改部署到 vm 时使用作用域配置。 有关详细信息，请参阅[在 Azure Monitor 中设定监视解决方案的目标（预览版）](../azure-monitor/insights/solution-targeting.md)。 

## <a name="about-scope-configurations"></a>关于范围配置

作用域配置是一个或多个已保存搜索的组， (查询) 用于将更改跟踪和清单的作用域限制为特定的计算机。 更新管理在 Log Analytics 工作区中使用范围配置来确定要启用的计算机。 将计算机添加到功能的更改时，还会将计算机添加到工作区中的已保存搜索。

## <a name="set-the-scope-limit"></a>设置范围限制

限制更改跟踪和清单部署的作用域：

1. 在自动化帐户中，选择“相关资源”下的“链接的工作区” 。

2. 单击 " **前往工作区**"。

3. 在“工作区数据源”下，选择“范围配置(预览)” 。

4. 选择  `MicrosoftDefaultScopeConfig-ChangeTracking` 作用域配置右侧的省略号，然后单击 " **编辑**"。 

5. 在“编辑”窗格中，选择“选择计算机组”。 “计算机组”窗格将显示用来创建作用域配置的已保存搜索。 更改跟踪和清单使用的已保存搜索如下：

    |名称     |类别  |Alias  |
    |---------|---------|---------|
    |MicrosoftDefaultComputerGroup     |  ChangeTracking       | ChangeTracking__MicrosoftDefaultComputerGroup        |

6. 选择已保存的搜索来查看和编辑用于填充组的查询。 下图显示了查询及其结果：

    ![保存的搜索](media/automation-scope-configurations-change-tracking/logsearch.png)

## <a name="next-steps"></a>后续步骤

* 要使用更改跟踪和库存，请参阅[管理更改跟踪和库存](change-tracking-file-contents.md)。
* 要排查常见功能问题，请参阅[排查更改跟踪和库存问题](troubleshoot/change-tracking.md)。
