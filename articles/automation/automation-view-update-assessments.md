---
title: 查看 Azure 更新管理更新评估
description: 本文介绍如何查看更新部署的更新评估。
services: automation
ms.subservice: update-management
ms.date: 01/21/2020
ms.topic: conceptual
ms.openlocfilehash: 58d3cf6261456c09195ad6dafaeb781b55d9e5ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278410"
---
# <a name="view-azure-update-management-update-assessments"></a>查看 Azure 更新管理更新评估

在 Azure 自动化帐户中，选择 **"更新管理"** 以查看计算机的状态。

此视图提供有关计算机、缺少的更新、更新部署和计划的更新部署的信息。 在 **"遵守"** 列中，您可以看到上次评估计算机的时间。 在 **"更新代理准备"** 列中，您可以看到更新代理的运行状况。 如果有问题，请选择链接以转到可用于疑难解答的文档，这些文档可以帮助您更正问题。

要运行返回有关计算机、更新或部署的信息的日志搜索，请在列表中选择相应的项。 此时将打开“日志搜索”窗格，其中显示了针对所选项的查询。****

![更新管理默认视图](media/automation-update-management/update-management-view.png)

## <a name="view-missing-updates"></a>查看缺少的更新

选择“缺少的更新”可查看计算机中缺少的更新列表。**** 每个更新都会列出，并且可供选择。 将显示有关需要更新的计算机数量、操作系统详细信息以及详细信息链接的信息。 **"日志搜索"** 窗格还显示有关更新的更多详细信息。

![缺少更新](./media/automation-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="update-classifications"></a>更新分类

下表列出了更新管理中支持的更新分类，并为每个分类提供了定义。

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

### <a name="linux"></a><a name="linux-2"></a>Linux

|分类  |描述  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 所有其他不在性质上不重要的更新或不是安全更新的更新。        |

对于 Linux，更新管理可以在显示评估数据的同时区分云中的关键更新和安全更新。 （由于云中的数据丰富，因此可以进行这种粒度。对于修补，更新管理依赖于计算机上可用的分类数据。 与其他发行版不同，CentOS 在产品的 RTM 版本中没有此信息。 如果配置了 CentOS 计算机以返回以下命令的安全数据，则更新管理可以基于分类进行修补：

```bash
sudo yum -q --security check-update
```

目前没有支持在 CentOS 上启用本机分类数据可用性的方法。 此时，仅向自行启用此功能的客户提供尽力而为的支持。

要对红帽企业版 6 上的更新进行分类，您需要安装 yum-安全插件。 在红帽企业Linux 7上，插件已经是yum本身的一部分，没有必要安装任何东西。 有关详细信息，请参阅以下红帽[知识文章](https://access.redhat.com/solutions/10021)。

## <a name="next-steps"></a>后续步骤

查看任何更新评估后，可以按照[Azure VM 的管理更新和修补程序](automation-tutorial-update-management.md)的步骤来安排更新部署。
