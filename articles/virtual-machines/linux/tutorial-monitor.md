---
title: 教程 - 监视 Azure 中的 Linux 虚拟机
description: 在本教程中，你将学习如何监视 Linux 虚拟机上运行的性能和发现的应用程序组件。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: b06342d5034b820be4e6fd49436546a5aa7b7e02
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75749795"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>教程：监视 Azure 中的 Linux 虚拟机

Azure 监视使用代理从 Azure VM 收集启动和性能数据，将此数据存储在 Azure 存储中，并使其可供通过门户、Azure PowerShell 模块和 Azure CLI 进行访问。 用于 VM 的 Azure Monitor 通过收集性能指标、发现 VM 上安装的应用程序组件来提供高级监视，并包括性能图表和依赖关系图。

在本教程中，你将了解如何执行以下操作：

> [!div class="checklist"]
> * 在 VM 上启用启动诊断
> * 查看启动诊断
> * 查看 VM 主机指标
> * 启用用于 VM 的 Azure Monitor
> * 查看 VM 性能指标
> * 创建警报

## <a name="launch-azure-cloud-shell"></a>启动 Azure Cloud Shell

Azure Cloud Shell 是免费的交互式 shell，可以使用它运行本文中的步骤。 它预安装有常用 Azure 工具并将其配置与帐户一起使用。 

若要打开 Cloud Shell，只需要从代码块的右上角选择“试一试”。  也可以通过转到 [https://shell.azure.com/powershell](https://shell.azure.com/powershell) 在单独的浏览器标签页中启动 Cloud Shell。 选择“复制”以复制代码块，将其粘贴到 Cloud Shell 中，然后按 Enter 来运行它。 

如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.0.30 或更高版本。 运行 `az --version` 即可查找版本。 如需进行安装或升级，请参阅[安装 Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。

## <a name="create-vm"></a>创建 VM

若要查看诊断和指标的状态，需要创建一个 VM。 首先，使用 [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 创建资源组。 以下示例在 *eastus* 位置创建名为 *myResourceGroupMonitor* 的资源组。

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

现使用 [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) 创建 VM。 以下示例将创建名为 myVM  的 VM，并生成 SSH 密钥（如果它们尚不存在于 *~/.ssh/* 中）：

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

在启用启动诊断之前，需要创建一个存储帐户来存储启动日志。 存储帐户的名称必须全局唯一，介于 3 和 24 个字符之间，并且只能包含数字和小写字母。 使用 [az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) 命令创建存储帐户。 本示例使用一个随机字符串来创建唯一的存储帐户名称。

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

启用引导诊断后，每当停止再启动 VM 时，会将有关启动过程的信息写入日志文件。 本示例首先使用 [az vm deallocate](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) 命令解除分配 VM，如下所示：

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

现在，请使用 [az vm start](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-start) 命令启动 VM，如下所示：

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

## <a name="enable-advanced-monitoring"></a>启用高级监视

若要使用用于 VM 的 Azure Monitor 启用对Azure VM 的监视，请执行以下操作：

1. 在 Azure 门户中单击“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。   

2. 在“VM”页上的“监视”部分，选择“见解(预览版)”。  

3. 在“见解(预览版)”页上，选择“立即试用”。  

    ![为 VM 启用用于 VM 的 Azure Monitor](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. 在“Azure Monitor Insights 载入”页上，如果现有的 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区  。  

    该列表预先选择了默认工作区和 VM 在订阅中部署的位置。 

    >[!NOTE]
    >若要创建新的 Log Analytics 工作区以存储 VM 中的监视数据，请参阅[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。 Log Analytics 工作区必须属于[支持的区域](../../azure-monitor/insights/vminsights-enable-overview.md#log-analytics)之一。

启用监视后，可能需要等待几分钟，然后才能查看 VM 的性能指标。

![启用用于 VM 的 Azure Monitor 来监视部署处理](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>查看 VM 性能指标

用于 VM 的 Azure Monitor 包含一组针对几项关键性能指标 (KPI) 的性能图表，帮助你确定虚拟机的性能状况。 若要从 VM 访问，请执行以下步骤。

1. 在 Azure 门户中单击“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。   

2. 在“VM”页上的“监视”部分，选择“见解(预览版)”。  

3. 选择“性能”选项卡。 

此页面不仅包含性能利用率图表，而且还包含一个表格，其中显示了发现的每个逻辑磁盘、其容量、利用率，以及按每个度量列出的总平均值。

## <a name="create-alerts"></a>创建警报

可以根据特定的性能指标创建警报。 例如，当平均 CPU 使用率超过特定的阈值或者可用磁盘空间低于特定的空间量时，警报可以发出通知。 警报显示在 Azure 门户中，也可以通过电子邮件发送。 还可以触发 Azure 自动化 Runbook 或 Azure 逻辑应用来响应生成的警报。

以下示例针对平均 CPU 使用率创建警报。

1. 在 Azure 门户中单击“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。   

2. 在 VM 边栏选项卡上单击“警报规则”，并单击警报边栏选项卡顶部的“添加指标警报”。  

3. 为警报提供**名称**，例如 *myAlertRule*

4. 若要在 CPU 百分比持续 5 分钟超过 1.0 时触发警报，请保留选中其他所有默认值。

5. （可选）选中“电子邮件所有者、参与者和阅读者”对应的框，以便向他们发送电子邮件通知。  默认操作是在门户中显示通知。

6. 单击 **“确定”** 按钮。

## <a name="next-steps"></a>后续步骤

在本教程中，你配置并查看了 VM 的性能。 你已了解如何执行以下操作：

> [!div class="checklist"]
> * 创建资源组和 VM
> * 在 VM 上启用启动诊断
> * 查看启动诊断
> * 查看主机指标
> * 启用用于 VM 的 Azure Monitor
> * 查看 VM 指标
> * 创建警报

请转到下一教程来了解 Azure 安全中心。

> [!div class="nextstepaction"]
> [管理 VM 安全性](../../security/fundamentals/overview.md)
