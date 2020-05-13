---
title: Azure 门户中的自动缩放虚拟机规模集
description: 如何在 Azure 门户中为虚拟机规模集创建自动缩放规则
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms:service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/29/2018
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: ea9d243e46aace9030c25222217ac3ad09a31c38
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83124935"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-in-the-azure-portal"></a>在 Azure 门户中自动缩放虚拟机规模集
创建规模集时，可定义想运行的 VM 实例数。 若应用程序需要更改，可自动增加或减少 VM 实例数。 通过自动缩放功能，可随客户需求的改变而进行调整，或在应用的整个生命周期内响应应用程序性能更改。

本文演示如何在 Azure 门户中创建自动缩放规则，以监视规模集中 VM 实例的性能。 这些缩放规则根据性能指标增加或减少 VM 实例数。 也可使用 [Azure PowerShell](tutorial-autoscale-powershell.md) 或 [Azure CLI](tutorial-autoscale-cli.md) 完成这些步骤。


## <a name="prerequisites"></a>必备条件
需要现有虚拟机规模集，才能创建自动缩放规则。 可使用 [Azure 门户](quick-create-portal.md)、[Azure PowerShell](quick-create-powershell.md) 或 [Azure CLI](quick-create-cli.md) 创建规模集。


## <a name="create-a-rule-to-automatically-scale-out"></a>创建规则，以便自动横向扩展
如果应用程序需求提高，规模集中 VM 实例上的负载将会增大。 如果这种负载增大持续稳定，而不只是短暂的需求，那么可以配置自动缩放规则来增加规模集中的 VM 实例数。 创建这些 VM 实例并部署应用程序后，规模集会开始通过负载均衡器将流量分配到这些实例和应用程序。 可以控制要监视的指标（例如 CPU 或磁盘）、应用程序负载必须处于给定阈值内的时间，以及要添加到规模集的 VM 实例数。

1. 打开 Azure 门户，然后在仪表板的左侧菜单中选择“资源组”  。
2. 选择包含其规模集的资源组，然后从资源列表中选择该规模集。
3. 在规模集窗口的左侧菜单中，选择“缩放”  。 选择此按钮“启用自动缩放”  ：

    ![在 Azure 门户中启用自动缩放](media/virtual-machine-scale-sets-autoscale-portal/enable-autoscale.png)

4. 为设置输入名称（例如“自动缩放”），然后选择“添加规则”   选项。

5. 当平均 CPU 负载在十分钟内均超过 70% 时，请创建规则以增加规模集中的 VM 实例数。 触发规则时，VM 实例数增加 20%。 在 VM 实例数较少的规模集中，可将“操作”设置为“增加计数”，然后指定 1 或 2 作为实例计数      。 在有大量 VM 实例的规模集中，增加 10% 或 20% 的 VM 实例可能更合适。

    为规则指定以下设置：
    
    | 参数              | 说明                                                                                                         | 值          |
    |------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
    | 时间聚合      | 定义如何聚合已收集的指标以便分析。                                                | 平均值        |
    | 指标名称           | 监视和应用规模集操作的性能指标。                                                   | CPU 百分比 |
    | 时间粒度统计信息  | 定义如何聚合每个时间粒度中收集的指标，以便进行分析。                             | 平均值        |
    | *“运算符”*             | 用于比较指标数据和阈值的运算符。                                                     | 大于   |
    | *阈值*            | 使自动缩放规则触发操作的百分比。                                                 | 70             |
    | *Duration*             | 比较指标与阈值之前监视的时长。                                   | 10 分钟     |
    | *操作*            | 定义应用规则时应纵向扩展还是缩减规模集，以及扩展或缩减的量                        | 增加百分比 |
    | *实例计数*       | 规则触发时，应更改 VM 实例的百分比。                                            | 20             |
    | 冷却（分钟）   | 为使自动缩放操作有时间生效，再次应用规则前需要等待的时间。 | 5 分钟      |

    以下示例演示在 Azure 门户中创建的规则，这些规则可与以下设置相匹配：

    ![创建自动缩放规则，以增加 VM 实例数](media/virtual-machine-scale-sets-autoscale-portal/rule-increase.png)

6. 若要创建规则，请选择“添加” 


## <a name="create-a-rule-to-automatically-scale-in"></a>创建规则，以便自动横向缩减
在夜间或周末，应用程序需求可能会降低。 如果这种负载降低在一段时间内持续稳定，可以配置自动缩放规则来减少规模集中的 VM 实例数。 这种横向缩减操作可以减少运行规模集所需的成本，因为只运行满足当前需求所需的实例数。

1. 再次选择“添加规则”  。
2. 创建规则，在平均 CPU 负载在十分钟内下降至 30% 以下时减少规模集中的 VM 实例数。 触发规则后，VM 实例数减少 20%。

    使用与上一规则相同的方法。 为规则调整以下设置：
    
    | 参数              | 说明                                                                                                          | 值          |
    |------------------------|----------------------------------------------------------------------------------------------------------------------|----------------|
    | *“运算符”*             | 用于比较指标数据和阈值的运算符。                                                      | 小于   |
    | *阈值*            | 使自动缩放规则触发操作的百分比。                                                 | 30             |
    | *操作*            | 定义应用规则时应纵向扩展还是缩减规模集，以及扩展或缩减的量                         | 降低百分比 |
    | *实例计数*       | 规则触发时，应更改 VM 实例的百分比。                                             | 20             |

3. 若要创建规则，请选择“添加” 


## <a name="define-autoscale-instance-limits"></a>定义自动缩放实例限制
自动缩放文件必须定义最小、最大和默认的 VM 实例数。 应用自动缩放规则时，这些实例限制可确保不会横向扩展到最大实例数以外，或横向缩减到最小实例数以内。

1. 设置以下实例限制：

    | 最小值 | 最大值 | 默认|
    |---------|---------|--------|
    | 2       | 10      | 2      |

2. 若要应用自动缩放规则和实例限制，请选择“保存”  。


## <a name="monitor-number-of-instances-in-a-scale-set"></a>监视规模集中的实例数
若要查看 VM 实例的数量和状态，请在规模集窗口的左侧菜单中选择“实例”  。 状态指示是随规模集自动横向扩展而创建 VM 实例，还是随规模集自动缩减而删除 VM 实例   。

![查看规模集 VM 实例的列表](media/virtual-machine-scale-sets-autoscale-portal/view-instances.png)


## <a name="autoscale-based-on-a-schedule"></a>按计划自动缩放
前述示例通过 CPU 使用情况等基本主机指标自动横向扩展或缩减规模集。 还可以根据计划创建自动缩放规则。 通过这些基于计划的规则，可在应用程序需求按预期增加（如核心工作时间）之前自动横向扩展 VM 实例数，在预期需求减少时（例如周末）自动横向缩减实例数。

1. 在规模集窗口的左侧菜单中，选择“缩放”  。 若要删除以前示例中创建的现有自动缩放规则，请选择回收站图标。

    ![删除现有的自动缩放规则](media/virtual-machine-scale-sets-autoscale-portal/delete-rules.png)

2. 选择“添加缩放条件”  。 选择规则名称旁的铅笔图标，并为其提供一个名称，例如“在每个工作日横向扩展”  。

    ![重命名默认的自动缩放规则](media/virtual-machine-scale-sets-autoscale-portal/rename-rule.png)

3. 选择“单选按钮”，缩放到特定的实例计数  。
4. 若要纵向扩展实例数，请输入“10”作为实例计数  。
5. 为计划类型选择“在特定日期重复”   。
6. 选择所有工作日，即星期一到星期五。
7. 选择适当的时区，然后将“开始时间”指定为“09:00”   。
8. 再次选择“添加缩放条件”  。 重复此过程，创建名为“在夜间横向缩减”的计划，该计划在每天 18:00 开始将规模集缩减到 3 个实例    。
9. 若要应用基于计划的自动缩放规则，请选择“保存”  。

    ![创建按计划进行缩放的自动缩放规则](media/virtual-machine-scale-sets-autoscale-portal/schedule-autoscale.PNG)

若要查看如何应用自动缩放规则，请通过顶部的缩放窗口选择“运行历史记录”   。 图和事件列表显示触发自动缩放规则的时间和规模集中增加或减少的 VM 实例数。


## <a name="next-steps"></a>后续步骤
本文详细介绍了如何使用自动缩放规则来进行横向缩放，即增加或减少规模集中的 VM 实例数**。 还可进行纵向缩放，即增大或减小 VM 实例的大小**。 有关详细信息，请参阅[虚拟机规模集垂直自动缩放](virtual-machine-scale-sets-vertical-scale-reprovision.md)。

有关如何管理 VM 实例的信息，请参阅[使用 Azure PowerShell 管理虚拟机规模集](virtual-machine-scale-sets-windows-manage.md)。

若要了解如何在触发自动缩放规则时生成警报，请参阅[在 Azure Monitor 中使用自动缩放操作发送电子邮件和 Webhook 警报通知](../azure-monitor/platform/autoscale-webhook-email.md)。 还可以[在 Azure Monitor 中使用审核日志发送电子邮件和 Webhook 警报通知](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md)。
