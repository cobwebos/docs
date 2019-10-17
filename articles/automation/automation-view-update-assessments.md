---
title: 查看 Azure 更新管理更新评估
description: 本文介绍如何查看更新部署的更新评估
services: automation
ms.service: automation
ms.subservice: update-management
author: bobbytreed
ms.author: robreed
ms.date: 05/17/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e88622ede6437086b86a33081d6ec9b9ea50ef65
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72377715"
---
# <a name="view-azure-update-management-update-assessments"></a>查看 Azure 更新管理更新评估

在自动化帐户中，选择“更新管理”来查看计算机的状态。

此视图提供有关计算机、缺少的更新、更新部署和计划的更新部署的信息。 在“符合性”列中，可以看到计算机的最后评估时间。 在“更新代理准备”列中，可以看到更新代理的运行状况。 如果有问题，请选择相应链接来转到疑难解答文档，以帮助你了解可以采用什么步骤来更正问题。

若要运行日志搜索来返回有关计算机、更新或部署的信息，请在列表中选择相应的项。 此时将打开“日志搜索”窗格，其中显示了针对所选项的查询。

![更新管理默认视图](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>查看缺少的更新

选择“缺少的更新”可查看计算机中缺少的更新列表。 每个更新都会列出，并且可供选择。 将显示有关需要更新的计算机数和操作系统的信息，以及指向更多信息的链接。 “日志搜索”窗格显示有关更新的更多详细信息。

![缺少更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新分类

下表列出了更新管理中的更新分类，以及每个分类的定义。

### <a name="windows"></a>Windows

|分类  |描述  |
|---------|---------|
|关键更新     | 解决关键、非安全相关错误的特定问题的更新。        |
|安全更新     | 产品特定、安全相关问题的更新。        |
|更新汇总     | 一起打包以便于部署的一组累积修补程序。        |
|功能包     | 在产品版本以外发布的新产品功能。        |
|服务包     | 应用于应用程序的一组累积修补程序。        |
|定义更新     | 对病毒或其他定义文件的更新。        |
|工具     | 可帮助完成一个或多个任务的实用工具或功能。        |
|更新     | 对当前已安装的应用程序或文件的更新。        |

### <a name="linux-2"></a>Linux

|分类  |描述  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 本质上不是关键更新或不是安全更新的所有其他更新。        |

对于 Linux，由于云中的数据扩充，更新管理可以区分云中的关键更新和安全更新，同时显示评估数据。 为了进行修补，更新管理依赖于计算机上提供的分类数据。 与其他发行版不同，CentOS 没有现成的此信息。 如果你已将 CentOS 计算机配置为以某种方式返回以下命令的安全数据，则更新管理将能够基于分类进行修补。

```bash
sudo yum -q --security check-update
```

当前没有受支持的方法可用来在 CentOS 上提供原生分类数据。 目前，只能尽力为可能自己实现了此功能的客户提供支持。

## <a name="next-steps"></a>后续步骤

查看任何更新评估后，可以按照[管理 Azure vm 的更新和修补程序](automation-tutorial-update-management.md)中的步骤计划更新部署。