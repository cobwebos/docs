---
title: 使用 Azure Monitor 创建、查看和管理指标警报
description: 了解如何使用 Azure 门户或 CLI 来创建、查看和管理指标警报规则。
author: harelbr
ms.author: harelbr
ms.topic: conceptual
ms.date: 02/16/2020
ms.subservice: alerts
ms.openlocfilehash: 5452f67a9da9a71a72c9387bc3e62d6735929984
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664705"
---
# <a name="create-view-and-manage-metric-alerts-using-azure-monitor"></a>使用 Azure Monitor 创建、查看和管理指标警报

Azure Monitor 中的指标警报提供了一种方法，以便在某个指标超过阈值时获得通知。 指标警报适用于一系列多维平台指标、自定义指标、Application Insights 标准指标和自定义指标。 在本文中，我们将介绍如何通过 Azure 门户和 Azure CLI 创建、查看和管理指标警报规则。 你还可以使用 Azure 资源管理器模板创建指标警报规则，这些模板在[单独的文章](alerts-metric-create-templates.md)中进行了介绍。

可以在[指标警报概述](alerts-metric-overview.md)中详细了解指标警报的工作原理。

## <a name="create-with-azure-portal"></a>使用 Azure 门户进行创建

以下过程说明如何在 Azure 门户中创建指标警报规则：

1. 在 [Azure 门户](https://portal.azure.com)中单击“监视”。 “监视”边栏选项卡将所有监视设置和数据合并到一个视图中。

2. 依次单击“警报”、“+ 新建警报规则”。

    > [!TIP]
    > 大多数资源边栏选项卡的资源菜单中的“监视”下面也包含“警报”，同样可从中创建警报。

3. 在加载的上下文窗格中单击“选择目标”，选择要发出警报的目标资源。 使用“订阅”和“资源类型”下拉列表查找要监视的资源。 也可以使用搜索栏查找资源。

4. 如果选定的资源具有可创建警报的指标，则右下方的“可用信号”将包含这些指标。 可在[此文](../../azure-monitor/platform/alerts-metric-near-real-time.md#metrics-and-dimensions-supported)中查看指标警报支持的资源类型的完整列表。

5. 选择目标资源后，单击“添加条件”。

6. 此时会显示资源支持的信号列表，请选择要为其创建警报的指标。

7. 您将看到过去六个小时的指标图表。 使用 "**图表周期**" 下拉列表选择查看指标的更长历史记录。

8. 如果指标具有维度，则会看到显示的维度表。 为每个维度选择一个或多个值。
    - 显示的维度值基于过去三天的指标数据。
    - 如果未显示所需的维度值，请单击 "+" 以添加自定义值。
    - 还可以针对任一维度**选择 \*** 。 **选择 \*** 会将选择范围动态调整为某个维度的所有当前和未来值。

    指标警报规则将评估所选值的所有组合的条件。 [详细了解如何针对多维指标发出警报](alerts-metric-overview.md)。

9. 选择**阈值**类型、**运算符**和**聚合类型**。 这将确定指标警报规则将评估的逻辑。
    - 如果使用的是**静态**阈值，请继续定义**阈值**。 度量值图表可帮助确定合理的阈值。
    - 如果使用的是**动态**阈值，请继续定义**阈值敏感度**。 指标图表将显示基于最新数据计算得出的阈值。 [详细了解动态阈值条件类型和敏感度选项](alerts-dynamic-thresholds.md)。

10. 还可以通过调整**聚合粒度**和**计算频率**来优化条件。 

11. 单击“完成”。

12. （可选）若要监视复杂的预警规则，请添加另一个条件。 目前，用户可以将包含动态阈值条件的预警规则用作单一条件。

13. 填写警报**详细信息**，如**警报规则名称**、**描述**和**严重性**。

14. 通过选择现有操作组或创建新的操作组，将一个操作组添加到警报中。

15. 单击“完成”保存指标警报规则。

> [!NOTE]
> 通过门户创建的指标警报规则将在目标资源所在的同一个资源组中创建。

## <a name="view-and-manage-with-azure-portal"></a>使用 Azure 门户查看和管理

可以使用“警报”下的“管理规则”边栏选项卡查看和管理指标警报规则。 以下过程说明如何查看指标警报规则，以及编辑其中的某个规则。

1. 在 Azure 门户中导航到“监视”

2. 单击“警报”和“管理规则”

3. 在“管理规则”边栏选项卡中，可以查看不同订阅中的所有警报规则。 可以使用**资源组**、**资源类型**和**资源**进一步筛选规则。 如果只想查看指标警报，请选择“指标”作为“信号类型”。

    > [!TIP]
    > 在“管理规则”边栏选项卡中，可以选择多个警报规则并启用/禁用它们。 需要将某些目标资源置于维护模式时，此功能可能十分有用

4. 单击要编辑的指标警报规则的名称

5. 在“编辑规则”中，单击要编辑的“警报条件”。 可根据需要更改指标、阈值条件和其他字段

    > [!NOTE]
    > 创建指标警报后，无法编辑“目标资源”和“警报规则名称”。

6. 单击“完成”保存所做的编辑。

## <a name="with-azure-cli"></a>使用 Azure CLI

前面的部分介绍了如何使用 Azure 门户创建、查看和管理指标警报规则。 本部分将介绍如何使用跨平台 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) 实现相同的结果。 使用 Azure CLI 的最快捷方式是通过 [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest)。 对于本文，我们将使用 Cloud Shell。

1. 请参阅 Azure 门户，单击**Cloud Shell**。

2. 在提示符下，可以结合 ``--help`` 选项使用命令来详细了解相应的命令及其用法。 例如，以下命令显示了可用于创建、查看和管理指标警报的命令的列表

    ```azurecli
    az monitor metrics alert --help
    ```

3. 可以创建一个简单的指标预警规则来监视 VM 上的平均 CPU 百分比是否大于 90

    ```azurecli
    az monitor metrics alert create -n {nameofthealert} -g {ResourceGroup} --scopes {VirtualMachineResourceID} --condition "avg Percentage CPU > 90" --description {descriptionofthealert}
    ```

4. 可以使用以下命令查看资源组中的所有指标警报

    ```azurecli
    az monitor metrics alert list  -g {ResourceGroup}
    ```

5. 可以使用规则的名称或资源 ID 查看特定指标警报规则的详细信息。

    ```azurecli
    az monitor metrics alert show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor metrics alert show --ids {RuleResourceId}
    ```

6. 可以使用以下命令禁用指标警报规则。

    ```azurecli
    az monitor metrics alert update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

7. 可以使用以下命令删除指标警报规则。

    ```azurecli
    az monitor metrics alert delete -g {ResourceGroup} -n {AlertRuleName}
    ```

## <a name="next-steps"></a>后续步骤

- [使用 Azure 资源管理器模板创建指标警报](../../azure-monitor/platform/alerts-enable-template.md)。
- [了解指标警报的工作原理](alerts-metric-overview.md)。
- [了解指标警报与动态阈值条件的工作原理](alerts-dynamic-thresholds.md)。
- [了解指标警报的 Webhook 架构](../../azure-monitor/platform/alerts-metric-near-real-time.md#payload-schema)

