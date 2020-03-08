---
title: 查看 Azure 更新管理更新评估
description: 本文介绍如何查看更新部署的更新评估。
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373060"
---
# <a name="view-azure-update-management-update-assessments"></a>查看 Azure 更新管理更新评估

在 Azure 自动化帐户中，选择 "**更新管理**" 以查看计算机的状态。

此视图提供有关计算机、缺少的更新、更新部署和计划的更新部署的信息。 在 "**符合性**" 列中，可以看到上次评估计算机的时间。 在 "**更新代理准备情况**" 列中，可以查看更新代理的运行状况。 如果出现问题，请选择链接以中转到疑难解答文档，该文档可帮助你解决问题。

若要运行返回有关计算机、更新或部署的信息的日志搜索，请在列表中选择相应的项。 此时将打开“日志搜索”窗格，其中显示了针对所选项的查询。

![更新管理默认视图](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>查看缺少的更新

选择“缺少的更新”可查看计算机中缺少的更新列表。 每个更新都会列出，并且可供选择。 其中显示了有关需要更新、操作系统详细信息和链接的计算机数的信息。 "**日志搜索**" 窗格还显示有关更新的更多详细信息。

![缺少更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新分类

下表列出了更新管理中支持的更新分类，其中包含每个分类的定义。

### <a name="windows"></a>Windows

|分类  |说明  |
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

|分类  |说明  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 本质上不重要或不是安全更新的所有其他更新。        |

对于 Linux，更新管理可以在显示评估数据时区分云中的关键更新和安全更新。 （由于云中的数据扩充，可能会出现这种情况。）对于修补，更新管理依赖于计算机上可用的分类数据。 与其他分发不同，CentOS 在该产品的 RTM 版本中不提供此信息。 如果已将 CentOS 计算机配置为返回以下命令的安全数据，则更新管理可以基于分类进行修补：

```bash
sudo yum -q --security check-update
```

目前，没有支持的方法可在 CentOS 上启用本机分类数据可用性。 目前，只为已自行启用此功能的客户提供了最大努力支持。

若要在 Red Hat Enterprise 版本6上对更新进行分类，需要安装 yum 插件。 在 Red Hat Enterprise Linux 7 上，该插件已经是 yum 本身的一部分，无需安装任何内容。 有关详细信息，请参阅以下 Red Hat[知识库文章](https://access.redhat.com/solutions/10021)。

## <a name="next-steps"></a>后续步骤

查看任何更新评估后，可以按照[管理 Azure vm 的更新和修补程序](automation-tutorial-update-management.md)中的步骤计划更新部署。
