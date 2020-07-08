---
title: 将 Azure Monitor 日志更新部署迁移到 Azure 门户
description: 本文介绍了如何将 Azure Monitor 日志更新部署迁移到 Azure 门户。
services: automation
ms.subservice: update-management
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 9bd6a7ff943b5f3750ce8aaeada32010b88272c2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.contentlocale: zh-CN
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745625"
---
# <a name="migrate-azure-monitor-logs-update-deployments-to-azure-portal"></a>将 Azure Monitor 日志更新部署迁移到 Azure 门户

Operations Management Suite (OMS) 门户已被[弃用](../azure-monitor/platform/oms-portal-transition.md)。 以前在 OMS 门户中提供的用于更新管理的所有功能目前在 Azure 门户中通过 Azure Monitor 日志来提供。 本文提供了迁移到 Azure 门户所需的信息。

## <a name="key-information"></a>重要信息

* 现有部署将继续发挥作用。 在 Azure 中重新创建部署后，可以删除旧部署。
* 过去在 OMS 中提供的所有现有功能现已在 Azure 中提供。 若要详细了解更新管理，请参阅[更新管理概述](automation-update-management.md)。

## <a name="access-the-azure-portal"></a>访问 Azure 门户

1. 在工作区中，单击“在 Azure 中打开”。 

    ![在 Azure 中打开 - Log Analytics](media/migrate-oms-update-deployments/link-to-azure-portal.png)

2. 在 Azure 门户中，单击“自动化帐户”

    ![Azure Monitor 日志](media/migrate-oms-update-deployments/log-analytics.png)

3. 在你的自动化帐户中，单击“更新管理”。

    ![更新管理](media/migrate-oms-update-deployments/azure-automation.png)

4. 在 Azure 门户中，在“所有服务”下选择“自动化帐户”。  

5. 在“管理工具”下选择相应的自动化帐户，然后单击“更新管理”。 

## <a name="recreate-existing-deployments"></a>重新创建现有部署

在 OMS 门户中创建的所有更新部署有一个[保存的搜索结果](../azure-monitor/platform/computer-groups.md)，也称为计算机组，其名称与现有更新部署的名称相同。 保存的搜索结果包含在更新部署中计划的计算机列表。

![更新管理](media/migrate-oms-update-deployments/oms-deployment.png)

若要使用此现有的保存搜索结果，请执行以下步骤：

1. 若要创建新的更新部署，请转到 Azure 门户，选择所用的自动化帐户，然后单击“更新管理”。 单击“计划更新部署”。

    ![计划更新部署](media/migrate-oms-update-deployments/schedule-update-deployment.png)

2. 此时会打开“新建更新部署”窗格。 为下表中介绍的属性输入值，然后单击“创建”：

3. 对于“要更新的计算机”，请选择 OMS 部署所用的已保存搜索。

    | properties | 说明 |
    | --- | --- |
    |名称 |用于标识更新部署的唯一名称。 |
    |操作系统| 选择 **Linux** 或 **Windows**。|
    |要更新的计算机 |选择一个已保存的搜索、已导入的组或者从下拉列表中选择“计算机”并选择各个计算机。 如果选择“计算机”，则计算机的就绪状态将在“更新代理商准备情况”列中显示 。</br> 要了解在 Azure Monitor 日志中创建计算机组的不同方法，请参阅 [Azure Monitor 日志中的计算机组](../azure-monitor/platform/computer-groups.md) |
    |更新分类|选择所需的所有更新分类。 CentOS 不能现成地支持此功能。|
    |要排除的更新|输入要排除的更新。 对于 Windows，输入不带 **KB** 前缀的知识库文章。 对于 Linux，输入包名称或使用通配符。  |
    |计划设置|选择启动时间，对于“定期”，然后“一次”或“重复”。  | 
    | 维护时段 |为更新设置的分钟数。 该值不能小于 30 分钟，也不能大于 6 小时。 |
    | 重新启动控制| 确定应如何处理重新启动。</br>可用选项包括：</br>需要时重新启动(默认)</br>永远重启</br>永不重启</br>仅重启 - 不安装更新|

4. 单击“计划的更新部署”以查看新建更新部署的状态。

    ![新建更新部署](media/migrate-oms-update-deployments/new-update-deployment.png)

5. 如前所述，通过 Azure 门户配置新部署后，可以在 Azure 门户中删除现有部署。

## <a name="next-steps"></a>后续步骤

若要详细了解 Azure 自动化中的更新管理，请参阅[更新管理概述](automation-update-management.md)。
