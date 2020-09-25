---
title: 查看 Azure 自动化更新评估
description: 本文介绍如何查看更新管理部署的更新评估。
services: automation
ms.subservice: update-management
ms.date: 09/17/2020
ms.topic: conceptual
ms.openlocfilehash: 2e32fc7c1872bf18b7f1c995f281a9b09ec45dc8
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91264437"
---
# <a name="view-update-assessments-in-update-management"></a>查看更新管理中的更新评估

在更新管理中，可以查看有关计算机、缺少的更新、更新部署和计划的更新部署的信息。 你可以从所选的启用 Arc 的服务器，或者在所有已配置的计算机和服务器上从自动化帐户查看作用域为所选的 Azure 虚拟机的评估信息。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)

## <a name="view-update-assessment"></a>查看更新评估

若要从 Azure VM 查看更新评估，请导航到 " **虚拟机** "，然后从列表中选择虚拟机。 在左侧菜单中，选择 "**来宾 + 主机更新**"，然后选择 "**来宾 + 主机更新**" 页上的 "**前往更新管理**"。

在更新管理中，可以查看有关计算机的信息、缺少的更新、更新部署和计划的更新部署。

[![AZURE VM 更新管理评估视图](./media/update-mgmt-view-update-assessments/update-assessment-azure-vm.png)](./media/update-mgmt-view-update-assessments/update-assessment-azure-vm-expanded.png#lightbox)

若要从启用了 Arc 的服务器中查看更新评估，请导航到 " **服务器-Azure Arc** "，然后从列表中选择你的服务器。 从左侧菜单中，选择 " **来宾和主机更新**"。 在 " **来宾 + 主机更新** " 页上，选择 " **前往更新管理**"。

在更新管理中，可以查看有关启用了 Arc 的计算机、缺少更新、更新部署和计划的更新部署的信息。

[![支持 Arc 的服务器的更新管理评估视图](./media/update-mgmt-view-update-assessments/update-assessment-arc-server.png)](./media/update-mgmt-view-update-assessments/update-assessment-arc-server-expanded.png#lightbox)

若要在所有计算机上查看更新评估（包括从自动化帐户启用 Arc 的服务器），请导航到 **自动化** 帐户，并从列表中选择更新管理启用的自动化帐户。 在自动化帐户中，从左侧菜单中选择 " **更新管理** "。

“更新管理”页上会列出你的环境所具有的更新。 如果任何更新被标识为缺失，则会在 " **缺少的更新** " 选项卡上显示这些更新的列表。

[![更新管理默认视图](./media/update-mgmt-overview/update-management-view.png)](./media/update-mgmt-overview/update-management-view-expanded.png#lightbox)

在“合规性”列中，可以看到计算机的最后评估时间。 在“更新代理就绪情况”列中，可以看到更新代理的运行状况。 如果有问题，请选择相应链接，转到可帮助你解决问题的疑难解答文档。

在“信息链接”下，选择更新的链接以打开支持文章，获取有关更新的重要信息。

[![查看更新状态](./media/update-mgmt-view-update-assessments/missing-updates.png)](./media/update-mgmt-view-update-assessments/missing-updates-expanded.png#lightbox)

单击更新的其他位置，打开“日志搜索”窗格。 日志搜索的查询是为该特定更新预定义的。 可以修改此查询或创建自己的查询，以查看详细信息。

[![查看日志查询结果](./media/update-mgmt-view-update-assessments/logsearch-results.png)](./media/update-mgmt-view-update-assessments/logsearch-results-expanded.png#lightbox)

## <a name="view-missing-updates"></a>查看缺少的更新

选择“缺少的更新”可查看计算机中缺少的更新列表。 每个更新都会列出，并且可供选择。 有关需要更新的计算机数的信息、操作系统详细信息，以及指向更多信息的链接都会显示。 “日志搜索”窗格还会显示有关更新的更多详细信息。

![缺少更新](./media/update-mgmt-view-update-assessments/automation-view-update-assessments-missing-updates.png)

## <a name="work-with-update-classifications"></a>使用更新分类

下表列出了更新管理中支持的更新分类，以及每个分类的定义。

### <a name="windows"></a>Windows

|分类  |说明  |
|---------|---------|
|关键更新     | 解决关键、非安全相关错误的特定问题的更新。        |
|安全更新     | 产品特定、安全相关问题的更新。        |
|更新汇总     | 一起打包以便于部署的一组修补程序。        |
|功能包     | 在产品版本以外发布的新产品功能。        |
|服务包     | 应用于应用程序的一组修补程序。        |
|定义更新     | 对病毒或其他定义文件的更新。        |
|工具     | 可帮助完成一个或多个任务的实用工具或功能。        |
|更新     | 对当前安装的应用程序或文件的更新。        |

### <a name="linux"></a>Linux

|分类  |说明  |
|---------|---------|
|关键和安全更新     | 特定问题或产品特定、安全相关问题的更新。         |
|其他更新     | 本质上不是关键更新或不是安全更新的所有其他更新。        |

对于 Linux，更新管理可以区分云中的关键更新和安全更新，同时显示评估数据。 （由于云中的数据扩充，可能会出现这种情况。）为了进行修补，更新管理依赖于计算机上提供的分类数据。 与其他发行版不同，CentOS 在产品的 RTM 版本中未提供此信息。 如果已将 CentOS 计算机配置为返回以下命令的安全数据，则更新管理可以基于分类进行修补：

```bash
sudo yum -q --security check-update
```

当前没有受支持的方法可用来在 CentOS 上提供原生分类数据。 目前，只能尽力为自己实现了此功能的客户提供支持。

若要对 Red Hat Enterprise 版本 6 上的更新进行分类，需要安装 yum 安全插件。 在 Red Hat Enterprise Linux 7 上，yum 本身已包含该插件，无需安装任何内容。 有关详细信息，请参阅以下 Red Hat [知识文章](https://access.redhat.com/solutions/10021)。

## <a name="next-steps"></a>后续步骤

此过程中的下一阶段是 [将更新部署到非合规计算机并查看部署结果](update-mgmt-deploy-updates.md)。
