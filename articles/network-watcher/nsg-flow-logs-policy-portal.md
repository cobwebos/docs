---
title: 快速入门-使用 Azure 策略部署和管理 NSG 流日志
titleSuffix: Azure Network Watcher
description: 本文介绍如何使用内置策略来管理 NSG 流日志的部署
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: damendo
ms.openlocfilehash: a603f55bd5bff7b2ed68623e9c712faaf8ac766f
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876892"
---
# <a name="quickstart-deploy-and-manage-nsg-flow-logs-using-azure-policy"></a>快速入门：使用 Azure 策略部署和管理 NSG 流日志 

## <a name="overview"></a>概述
Azure Policy 可帮助实施组织标准并大规模评估合规性。 Azure Policy 的常见用例包括实施监管来满足资源一致性、法规遵从性、安全性、成本和管理方面的要求。 在本文中，我们将使用 NSG 流日志提供的两个内置策略来管理流日志设置。 第一个策略标记未启用 flow 日志的所有 Nsg。 第二个策略自动部署 Nsg 的流日志，而不启用流日志。 

如果是首次创建 Azure 策略，可以阅读： 
- [Azure 策略概述](https://docs.microsoft.com/azure/governance/policy/overview) 
- [有关创建策略的教程](https://docs.microsoft.com/azure/governance/policy/assign-policy-portal#create-a-policy-assignment)。


## <a name="locate-the-policies"></a>找到策略
1. 中转到 Azure 门户– [portal.azure.com](https://portal.azure.com) 

在顶部搜索栏策略主页中搜索 "策略"，导航到 "Azure 策略" 页 ![](./media/network-watcher-builtin-policy/1_policy-search.png)

2. 转到左侧窗格中的 " **分配** " 选项卡

![“分配”选项卡](./media/network-watcher-builtin-policy/2_assignments-tab.png)

3. 单击 " **分配策略** " 按钮 

![分配策略按钮](./media/network-watcher-builtin-policy/3_assign-policy-button.png)

4. 单击 "策略定义" 下的三个点菜单以查看可用策略

5. 使用类型筛选器并选择 "内置"。 然后搜索 "流日志"

应该会看到 "流日志的两个内置策略" ![ 策略列表](./media/network-watcher-builtin-policy/4_filter-for-flow-log-policies.png)

6. 选择要分配的策略

- *"应为每个网络安全组配置流日志"* 是标记不符合 nsg 的审核策略，即不启用流日志记录的 nsg
- *"部署具有目标网络安全组的流日志资源"* 是包含部署操作的策略，它在没有流日志的所有 nsg 上启用流日志

下面是每个策略的单独说明。  

## <a name="audit-policy"></a>审核策略 

### <a name="how-the-policy-works"></a>策略的工作方式

此策略检查 "networkSecurityGroups" 类型的所有现有 ARM 对象，即查看给定范围内的所有 Nsg，并通过 NSG 的 "流日志" 属性检查是否存在链接流日志。 如果该属性不存在，则会标记 NSG。

若要查看策略的完整定义，可以访问 " [定义" 选项卡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) ，搜索 "流日志" 以查找策略

### <a name="assignment"></a>分配

1. 填写策略详细信息

- 范围：订阅是常见选择，你也可以选择与你相关的管理组或资源组。  
- 策略定义：应选择，如 "查找策略" 部分中所示。
- AssignmentName：选择描述性名称 

2. 单击 "查看 + 创建" 查看分配

此策略不需要任何参数。 分配审核策略时，无需在 "修正" 选项卡中填写详细信息。  

![审核策略检查](./media/network-watcher-builtin-policy/5_1_audit-policy-review.png)

### <a name="results"></a>结果

若要检查结果，请打开 "符合性" 选项卡，然后搜索分配的名称。
策略运行后，应该会看到类似于以下屏幕截图的内容。 如果策略未运行，请等待一段时间。 

![审核策略结果](./media/network-watcher-builtin-policy/7_1_audit-policy-results.png)

## <a name="deploy-if-not-exists-policy"></a>部署-不存在策略 

### <a name="policy-structure"></a>策略结构

此策略检查 "networkSecurityGroups" 类型的所有现有 ARM 对象，即查看给定范围内的所有 Nsg，并通过 NSG 的 "流日志" 属性检查是否存在链接流日志。 如果该属性不存在，则策略将部署流日志。 

若要查看策略的完整定义，可以访问 " [定义" 选项卡](https://ms.portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyMenuBlade/Definitions) ，搜索 "流日志" 以查找策略。 

### <a name="assignment"></a>分配

1. 填写策略详细信息

- 范围：订阅是常见选择，你也可以选择与你相关的管理组或资源组。  
- 策略定义：应选择，如 "查找策略" 部分中所示。
- AssignmentName：选择描述性名称 

2. 添加策略参数 

网络观察程序服务是一种区域服务。 这些参数允许执行部署流日志的策略操作。 
- NSG 区域：策略的目标 Azure 区域
- 存储 ID：存储帐户的完整资源 ID。 注意：此存储帐户应与 NSG 位于同一区域。 
- 网络观察程序 RG：包含网络观察程序资源的资源组的名称。 如果尚未对其进行重命名，则可以输入 "NetworkWatcherRG"，这是默认值。
- 网络观察程序名称：区域网络观察程序服务的名称。 格式： NetworkWatcher_RegionName。 示例： NetworkWatcher_centralus。 请查看完整列表。

![用餐策略参数](./media/network-watcher-builtin-policy/5_2_1_dine-policy-details-alt.png)

3. 添加修正详细信息

- 如果希望策略影响现有资源，请选中 "创建修正任务" 
- 应已选中 "创建托管标识"
- 选择了与以前相同的托管标识位置 
- 你将需要参与者或所有者权限才能使用此策略。 如果你具有这些权限，则不会看到任何错误。

![用餐策略修正](./media/network-watcher-builtin-policy/5_2_2_dine-remediation.png) 

4. 单击 "查看 + 创建" 查看分配，你应该会看到类似于以下屏幕截图的内容。

![用餐策略检查](./media/network-watcher-builtin-policy/5_2_3_dine-review.png) 


### <a name="results"></a>结果

若要检查结果，请打开 "符合性" 选项卡，然后搜索分配的名称。
策略后，你应该会看到类似于以下屏幕截图的内容。 如果策略未运行，请等待一段时间。

![用餐策略结果](./media/network-watcher-builtin-policy/7_2_dine-policy-results.png)  


## <a name="next-steps"></a>后续步骤 

-   使用此 [教程](https://docs.microsoft.com/azure/network-watcher/quickstart-configure-network-security-group-flow-logs-from-arm-template) 进一步了解如何使用 ARM 模板部署流日志和流量分析。
-   了解有关[网络观察](https://docs.microsoft.com/azure/network-watcher/)程序的详细信息
