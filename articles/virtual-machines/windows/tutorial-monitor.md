---
title: 教程 - 监视 Azure 中的 Windows 虚拟机 | Microsoft Docs
description: 本教程介绍如何监视 Windows 虚拟机上运行的性能和发现的应用程序组件。
services: virtual-machines-windows
documentationcenter: virtual-machines
author: mgoedtel
manager: carmonm
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: a2f4083841c801db3edf1b2838b8d3271b700731
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679342"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>教程：监视 Azure 中的 Windows 虚拟机

Azure 监视使用代理从 Azure VM 收集启动和性能数据，将此数据存储在 Azure 存储中，并使其可供通过门户、Azure PowerShell 模块和 Azure CLI 进行访问。 用于 VM 的 Azure Monitor 通过收集性能指标、发现 VM 上安装的应用程序组件来提供高级监视，并包括性能图表和依赖关系图。

本教程介绍如何执行下列操作：

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

## <a name="create-virtual-machine"></a>创建虚拟机

若要在本教程中配置 Azure 监视和更新管理，需要 Azure 中的 Windows VM。 首先，使用 [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential) 设置 VM 的管理员用户名和密码：

```azurepowershell-interactive
$cred = Get-Credential
```

现在，使用 [New-AzVM](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) 创建 VM。 以下示例在“EastUS”位置  创建一个名为 myVM  的 VM。 如果资源组 *myResourceGroupMonitorMonitor* 和支持的网络资源不存在，则会创建它们：

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

创建资源和 VM 需要几分钟的时间。

## <a name="view-boot-diagnostics"></a>查看启动诊断

当 Windows 虚拟机启动时，启动诊断代理将捕获屏幕输出，可以使用该输出进行故障排除。 此功能是默认启用的。 捕获的屏幕截图存储在一个 Azure 存储帐户中，该帐户也是默认创建的。

可以使用 [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/az.compute/get-azvmbootdiagnosticsdata) 命令获取启动诊断数据。 在下面的示例中，启动诊断下载到了 *c:\* 驱动器的根目录中。

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>查看主机指标

Windows VM 在 Azure 中有一个与它交互的专用主机 VM。 系统会自动收集该主机的指标，可以在 Azure 门户中查看这些指标。

1. 在 Azure 门户中单击“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。   
2. 要查看主机 VM 的性能情况，请在 VM 边栏选项卡上单击“指标”，并选择“可用指标”下的任一主机指标。  

    ![查看主机指标](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>启用高级监视

若要使用用于 VM 的 Azure Monitor 启用对Azure VM 的监视，请执行以下操作：

1. 在 Azure 门户中单击“资源组”，选择“myResourceGroupMonitor”，并在资源列表中选择“myVM”。   

2. 在“VM”页上的“监视”部分，选择“见解(预览版)”。  

3. 在“见解(预览版)”页上，选择“立即试用”。  

    ![为 VM 启用用于 VM 的 Azure Monitor](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

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

6. 单击“确定”  按钮。

## <a name="next-steps"></a>后续步骤

在本教程中，你配置并查看了 VM 的性能。 你已了解如何：

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
