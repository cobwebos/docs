---
title: 教程 - 监视和更新 Azure 中的 Linux 虚拟机 | Microsoft Docs
description: 本教程介绍如何在 Linux 虚拟机上监视启动诊断和性能指标，以及管理程序包更新
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/06/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f5773d2f6634d1de9674351ff30a15b488bdd672
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2018
ms.locfileid: "38597457"
---
# <a name="tutorial-monitor-and-update-a-linux-virtual-machine-in-azure"></a>教程：监视和更新 Azure 中的 Linux 虚拟机

为确保 Azure 中的虚拟机 (VM) 正常运行，可以查看启动诊断、性能指标，并管理程序包更新。 本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 在 VM 上启用启动诊断
> * 查看启动诊断
> * 查看主机指标
> * 在 VM 上启用诊断扩展
> * 查看 VM 指标
> * 基于诊断指标创建警报
> * 管理程序包更新
> * 监视器更改和清单
> * 设置高级监视

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI 2.0]( /cli/azure/install-azure-cli)。

## <a name="create-vm"></a>创建 VM

若要查看诊断和指标的状态，需要创建一个 VM。 首先，使用 [az group create](/cli/azure/group#az-group-create) 创建资源组。 以下示例在 *eastus* 位置创建名为 *myResourceGroupMonitor* 的资源组。

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

现使用 [az vm create](/cli/azure/vm#az-vm-create) 创建 VM。 以下示例将创建名为 myVM 的 VM，并生成 SSH 密钥（如果它们尚不存在于 *~/.ssh/* 中）：

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>启用启动诊断

Linux VM 启动时，启动诊断扩展将捕获启动输出并将其存储在 Azure 存储中。 此数据可以用于排查 VM 启动问题。 使用 Azure CLI 创建 Linux VM 时，不会自动启用启动诊断。

在启用启动诊断之前，需要创建一个存储帐户来存储启动日志。 存储帐户的名称必须全局唯一，介于 3 和 24 个字符之间，并且只能包含数字和小写字母。 使用 [az storage account create](/cli/azure/storage/account#az-storage-account-create) 命令创建存储帐户。 本示例使用一个随机字符串来创建唯一的存储帐户名称。

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

启用引导诊断时，需要 Blob 存储容器的 URI。 以下命令查询存储帐户以返回此 URI。 URI 值存储在名为 *bloburi* 的变量中，会在下一步骤中使用。

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

现在，请使用 [az vm boot-diagnostics enable](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable) 启用启动诊断。 `--storage` 值是在上一步骤中收集的 Blob URI。

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>查看启动诊断

启用引导诊断后，每当停止再启动 VM 时，会将有关启动过程的信息写入日志文件。 本示例首先使用 [az vm deallocate](/cli/azure/vm#az-vm-deallocate) 命令解除分配 VM，如下所示：

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

现在，请使用 [az vm start]( /cli/azure/vm#az-vm-stop) 命令启动 VM，如下所示：

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

可以使用 [az vm boot-diagnostics get-boot-log](https://docs.microsoft.com/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) 命令获取 *myVM* 的启动诊断数据，如下所示：

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>查看主机指标

Linux VM 在 Azure 中有一个与它交互的专用主机。 系统会自动收集该主机的指标，可以在 Azure 门户中查看这些指标，如下所示：

1. 在 Azure 门户中选择“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。
1. 若要查看主机 VM 的性能情况，请在 VM 窗口中选择“指标”，并选择“可用指标”下面的任一“[主机]”指标。

    ![查看主机指标](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>安装诊断扩展

可以使用基本的主机指标，但若要查看更详细的指标和 VM 特定的指标，需在 VM 上安装 Azure 诊断扩展。 使用 Azure 诊断扩展可从 VM 检索其他监视数据和诊断数据。 可以查看这些性能指标，并根据 VM 的性能情况创建警报。 诊断扩展是通过 Azure 门户安装的，如下所述：

1. 在 Azure 门户中选择“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。
1. 选择“诊断设置”。 在“选取存储帐户”下拉菜单中，如果尚未选择，请选择在上一部分中创建的“mydiagdata[1234]”帐户。
1. 选择“启用来宾级监视”按钮。

    ![查看诊断指标](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>查看 VM 指标

可以像查看主机 VM 指标一样查看 VM 指标：

1. 在 Azure 门户中选择“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。
1. 若要查看 VM 的性能情况，请在 VM 窗口上选择“指标”，并选择“可用指标”下的任一“[来宾]”诊断指标。

    ![查看 VM 指标](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>创建警报

可以根据特定的性能指标创建警报。 例如，当平均 CPU 使用率超过特定的阈值或者可用磁盘空间低于特定的空间量时，警报可以发出通知。 警报显示在 Azure 门户中，也可以通过电子邮件发送。 还可以触发 Azure 自动化 Runbook 或 Azure 逻辑应用来响应生成的警报。

以下示例针对平均 CPU 使用率创建警报。

1. 在 Azure 门户中选择“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。
2. 选择“警报(经典)”，然后在警报窗口顶部选择“添加指标警报(经典)”。
3. 为警报提供**名称**，例如 *myAlertRule*
4. 若要在 CPU 百分比持续 5 分钟超过 1.0 时触发警报，请保留选中其他所有默认值。
5. （可选）选中“电子邮件所有者、参与者和阅读者”对应的框，以便向他们发送电子邮件通知。 默认操作是在门户中显示通知。
6. 选择“确定”按钮。

## <a name="manage-package-updates"></a>管理程序包更新

使用更新管理可以管理 Azure Linux VM 的更新和修补程序。
可以直接在 VM 中快速评估可用更新的状态、计划所需更新的安装以及查看部署结果，验证更新是否已成功应用到 VM。

有关定价信息，请参阅[更新管理的自动化定价](https://azure.microsoft.com/pricing/details/automation/)

### <a name="enable-update-management"></a>启用更新管理

为 VM 启用更新管理：

1. 在屏幕的左侧，选择“虚拟机”。
2. 从列表中选择一个虚拟机。
3. 在 VM 屏幕上的“操作”部分中，选择“更新管理”。 “启用更新管理”屏幕随即打开。

执行验证以确定是否为该 VM 启用了更新管理。
验证包括检查 Log Analytics 工作区和链接的自动化帐户，以及解决方案是否在工作区中。

[Log Analytics](../../log-analytics/log-analytics-overview.md) 工作区用于收集由功能和服务（如更新管理）生成的数据。
工作区提供了一个位置来查看和分析来自多个数据源的数据。
若要在需要更新的 VM 上执行其他操作，可使用 Azure 自动化运行针对 VM 的 Runbook，例如下载和应用更新。

验证过程还会检查 VM 是否预配了 Microsoft Monitoring Agent (MMA) 和自动化混合 Runbook 辅助角色。
此代理用于与虚拟机通信并获取关于更新状态的信息。

选择 Log Analytics 工作区和自动化帐户，然后选择“启用”以启用此解决方案。 启用此解决方案最长需要 15 分钟的时间。

如果在载入过程中发现缺少下列任何先决条件，则会自动添加这些条件：

* [Log Analytics](../../log-analytics/log-analytics-overview.md) 工作区
* [自动化](../../automation/automation-offering-get-started.md)
* VM 上已启用[混合 runbook 辅助角色](../../automation/automation-hybrid-runbook-worker.md)

“更新管理”屏幕随即打开。 配置要使用的位置、Log Analytics 工作区和自动化帐户，然后选择“启用”。 如果这些字段灰显，则意味着已为 VM 启用其他自动化解决方案，因此必须使用同一工作区和自动化帐户。

![启用“更新管理解决方案”](./media/tutorial-monitoring/manage-updates-update-enable.png)

启用解决方案最多可能需要 15 分钟。 在此期间，不应关闭浏览器窗口。 启用该解决方案后，VM 中缺少的更新信息会流向 Log Analytics。 这些数据需花费 30 分钟到 6 小时的时间才能用于分析。

### <a name="view-update-assessment"></a>查看更新评估

启用“更新管理”后，“更新管理”屏幕随即显示。 评估更新完成后，可在“缺失更新”选项卡上查看缺失更新的列表。

 ![查看更新状态](./media/tutorial-monitoring/manage-updates-view-status-linux.png)

### <a name="schedule-an-update-deployment"></a>计划更新部署

若要安装更新，请计划一个遵循你的发布时间和服务窗口的部署。 可选择在部署中包括哪种更新类型。 例如，可包括关键或安全更新，排除更新汇总。

若要为 VM 计划新的更新部署，请选择“更新管理”屏幕顶部的“计划更新部署”。 在“新建更新部署”屏幕中，指定以下信息：

* 名称- 提供用于标识更新部署的唯一名称。
* 更新分类- 选择部署中包含的更新部署的软件类型。 分类类型：
  * 关键和安全更新
  * 其他更新
* **要排查的更新** - 可以提供一个列表，其中包含在更新部署过程中应跳过的包名。 包名支持通配符（例如，\*kernal\*）。

  ![更新计划设置屏幕](./media/tutorial-monitoring/manage-updates-exclude-linux.png)

* 计划设置- 可以接受默认的日期和时间，即当前时间后 30 分钟，或指定不同的时间。
  还可以指定部署是发生一次还是设置定期计划。 若要设置定期计划，请选择“重复周期”下的“重复执行”选项。

  ![更新计划设置屏幕](./media/tutorial-monitoring/manage-updates-schedule-linux.png)

* 维护时段(分钟) - 指定要在其中进行更新部署的时间段。 这有助于确保在定义的服务时段内执行更改。

完成配置计划后，选择“创建”按钮，然后返回到状态仪表板。
请注意，“已计划”表显示你创建的部署计划。

> [!WARNING]
> 对于需要重新启动的更新，VM 将自动重启。

### <a name="view-results-of-an-update-deployment"></a>查看更新部署结果

在计划性部署开始后，可以在“更新管理”屏幕的“更新部署”选项卡上查看该部署的状态。
如果部署当前正在运行，则状态显示为“正在运行”。 如果部署已成功完成，则状态会更改为“成功”。
如果部署中有一个或多个更新失败，则状态为“部分失败”。
选择已完成的更新部署，查看该更新部署的仪表板。

![特定部署的更新部署状态仪表板](./media/tutorial-monitoring/manage-updates-view-results.png)

在“更新结果”中，磁贴总结了 VM 上更新和部署结果的总数。
右侧的表格详细列出了每个更新的细目以及安装结果，结果可能是以下值之一：

* 未尝试 - 由于定义的维护时段时长不足，因而未安装更新。
* 成功- 更新成功
* 失败- 更新失败

若要查看部署创建的所有日志条目，请选择“所有日志”。

选择“输出”磁贴，查看负责管理目标 VM 更新部署的 runbook 的作业流。

若要查看有关部署中错误的详细信息，请选择“错误”。

## <a name="monitor-changes-and-inventory"></a>监视器更改和清单

可以收集和查看清单，了解计算机上的软件、文件、Linux 守护程序、Windows 服务和 Windows 注册表项。 跟踪计算机的配置有助于查明环境中的操作问题，更好地了解计算机的状态。

### <a name="enable-change-and-inventory-management"></a>启用更改和清单管理

为 VM 启用更改和清单管理：

1. 在屏幕的左侧，选择“虚拟机”。
2. 从列表中选择一个虚拟机。
3. 在 VM 屏幕上的“操作”部分中，选择“清单”或“更改跟踪”。 此时会打开“启用更改跟踪和清单”屏幕。

配置要使用的位置、Log Analytics 工作区和自动化帐户，然后选择“启用”。 如果这些字段灰显，则意味着已为 VM 启用其他自动化解决方案，因此必须使用同一工作区和自动化帐户。 即使这些解决方案在菜单上是分开的，它们也是同一解决方案。 启用一个解决方案就会为 VM 启用两个解决方案。

![启用更改和清单跟踪](./media/tutorial-monitoring/manage-inventory-enable.png)

启用解决方案后，可能需要一些时间在 VM 上收集清单，然后才显示数据。

### <a name="track-changes"></a>跟踪更改

在 VM 中的“操作”下选择“更改跟踪”。 选择“编辑设置”，此时会显示“更改跟踪”页。 选择要跟踪的设置类型，然后选择“+ 添加”以配置设置。 Linux 上的可用选项为“Linux 文件”。

有关更改跟踪的详细信息，请参阅[排查 VM 上的更改问题](../../automation/automation-tutorial-troubleshoot-changes.md)

### <a name="view-inventory"></a>查看清单

在 VM 中的“操作”下选择“清单”。 在“软件”选项卡上有一个表，列出了已发现的软件。 可在表中查看每个软件记录的高级详细信息。 这些详细信息包括软件名称、版本、发布者和上次刷新时间。

![查看清单](./media/tutorial-monitoring/inventory-view-results.png)

### <a name="monitor-activity-logs-and-changes"></a>监视活动日志和更改

在 VM 的“更改跟踪”页中，选择“管理活动日志连接”。 此任务打开“Azure 活动日志”页。 选择“连接”，将更改跟踪连接到 VM 的 Azure 活动日志。

启用此设置后，导航到 VM 的“概览”页，然后选择“停止”以停止 VM。 出现提示时，选择“是”即可停止 VM。 将 VM 解除分配以后，请选择“启动”以重启 VM。

停止和启动 VM 时，会在活动日志中记录一个事件。 导航回到“更改跟踪”页。 选择页面底部的“事件”选项卡。 一段时间后，事件会显示在图表和表中。 可以选择每个事件来查看其详细信息。

![在活动日志中查看更改](./media/tutorial-monitoring/manage-activitylog-view-results.png)

此图表显示了一段时间内发生的更改。 添加活动日志连接以后，顶部的线形图会显示 Azure 活动日志事件。 条形图的每一行代表不同类型的可跟踪更改。 这些类型是 Linux 守护程序、文件、软件。 “更改”选项卡显示在可视化效果中显示的更改的详细信息，按更改发生时间以降序方式排列（最近发生的排在最前面）。

## <a name="advanced-monitoring"></a>高级监视

可以使用 [Azure 自动化](../../automation/automation-intro.md)提供的“更新管理”及“更改和清单”等解决方案对 VM 进行更高级的监视。

可以访问 Log Analytics 工作区时，可以通过选择“设置”下的“高级设置”来找到工作区密钥和工作区标识符。 请将 \<workspace-key\> 和 \<workspace-id\> 替换为 Log Analytics 工作区中的值，然后即可使用 **az vm extension set** 将扩展添加到 VM：

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroupMonitor \
  --vm-name myVM \
  --name OmsAgentForLinux \
  --publisher Microsoft.EnterpriseCloud.Monitoring \
  --version 1.3 \
  --protected-settings '{"workspaceKey": "<workspace-key>"}' \
  --settings '{"workspaceId": "<workspace-id>"}'
```

几分钟后，应该会在 Log Analytics 工作区中看到新 VM。

![OMS 边栏选项卡](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>后续步骤

在本教程中，将配置、审核和管理虚拟机更新。 你已了解如何：

> [!div class="checklist"]
> * 在 VM 上启用启动诊断
> * 查看启动诊断
> * 查看主机指标
> * 在 VM 上启用诊断扩展
> * 查看 VM 指标
> * 基于诊断指标创建警报
> * 管理程序包更新
> * 监视器更改和清单
> * 设置高级监视

请转到下一教程来了解 Azure 安全中心。

> [!div class="nextstepaction"]
> [管理 VM 安全性](./tutorial-azure-security.md)
