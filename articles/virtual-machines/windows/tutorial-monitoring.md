---
title: "Azure 监视和 Windows 虚拟机 | Microsoft Docs"
description: "教程 - 使用 Azure PowerShell 监视 Windows 虚拟机"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 05/04/2017
ms.author: davidmu
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 2bf6e3cf2f039a9c92617203a3d4cf2aac8901ce
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017

---

# <a name="monitor-a-windows-virtual-machine-with-azure-powershell"></a>使用 Azure PowerShell 监视 Windows 虚拟机

Azure 监视使用代理从 Azure VM 收集启动和性能数据，将此数据存储在 Azure 存储中，并使其可供通过门户、Azure PowerShell 模块和 Azure CLI 进行访问。 本教程将介绍如何执行下列操作：

> [!div class="checklist"]
> * 在 VM 上启用启动诊断
> * 查看启动诊断
> * 查看 VM 主机指标
> * 安装诊断扩展
> * 查看 VM 指标
> * 创建警报
> * 设置高级监视

本教程需要 Azure PowerShell 模块 3.6 或更高版本。 可以运行 ` Get-Module -ListAvailable AzureRM` 来查找版本。 如果需要升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

若要完成本教程中的示例，必须具备现有虚拟机。 如果需要，此[脚本示例](../scripts/virtual-machines-windows-powershell-sample-create-vm.md)可替你创建一个虚拟机。 根据教程进行操作时，请根据需要替换资源组、VM 名称和位置。

## <a name="view-boot-diagnostics"></a>查看启动诊断

当 Windows 虚拟机启动时，启动诊断代理将捕获屏幕输出，可以使用该输出进行故障排除。 此功能是默认启用的。 捕获的屏幕截图存储在一个 Azure 存储帐户中，该帐户也是默认创建的。 

可以使用 [Get-AzureRmVMBootDiagnosticsData](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmbootdiagnosticsdata) 命令获取启动诊断数据。 在下面的示例中，启动诊断下载到了 *c:\* 驱动器的根目录中。 

```powershell
Get-AzureRmVMBootDiagnosticsData -ResourceGroupName myResourceGroup -Name myVM -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>查看主机指标

Windows VM 在 Azure 中有一个与它交互的专用主机 VM。 系统会自动收集该主机的指标，可以在 Azure 门户中查看这些指标。

1. 在 Azure 门户中，单击“资源组”，选择“myResourceGroup”，然后在资源列表中选择“myVM”。
2. 若要查看主机 VM 的性能情况，请在 VM 边栏选项卡上单击“指标”，然后选择“可用指标”下的任一主机指标。

    ![查看主机指标](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="install-diagnostics-extension"></a>安装诊断扩展

可以使用基本的主机指标，但若要查看更详细的指标和 VM 特定的指标，需在 VM 上安装 Azure 诊断扩展。 使用 Azure 诊断扩展可从 VM 检索其他监视数据和诊断数据。 可以查看这些性能指标，并根据 VM 的性能情况创建警报。 诊断扩展是通过 Azure 门户安装的，如下所述：

1. 在 Azure 门户中，单击“资源组”，选择“myResourceGroup”，然后在资源列表中选择“myVM”。
2. 单击“诊断设置”。 列表中将显示已在上一部分启用的“启动诊断”。 单击“基本指标”对应的复选框。
3. 单击“启用来宾级监视”按钮。

    ![查看诊断指标](./media/tutorial-monitoring/enable-diagnostics-extension.png)

## <a name="view-vm-metrics"></a>查看 VM 指标

可以像查看主机 VM 指标一样查看 VM 指标：

1. 在 Azure 门户中，单击“资源组”，选择“myResourceGroup”，然后在资源列表中选择“myVM”。
2. 若要查看 VM 的性能情况，请在 VM 边栏选项卡上单击“指标”，然后选择“可用指标”下的任一诊断指标。

    ![查看 VM 指标](./media/tutorial-monitoring/monitor-vm-metrics.png)

## <a name="create-alerts"></a>创建警报

可以根据特定的性能指标创建警报。 例如，当平均 CPU 使用率超过特定的阈值或者可用磁盘空间低于特定的空间量时，警报可以发出通知。 警报显示在 Azure 门户中，也可以通过电子邮件发送。 还可以触发 Azure 自动化 Runbook 或 Azure 逻辑应用来响应生成的警报。

以下示例针对平均 CPU 使用率创建警报。

1. 在 Azure 门户中，单击“资源组”，选择“myResourceGroup”，然后在资源列表中选择“myVM”。
2. 在 VM 边栏选项卡上单击“警报规则”，然后单击警报边栏选项卡顶部的“添加指标警报”。
4. 为警报提供**名称**，例如 *myAlertRule*
5. 若要在 CPU 百分比持续 5 分钟超过 1.0 时触发警报，请保留选中其他所有默认值。
6. （可选）选中“电子邮件所有者、参与者和阅读者”对应的框，以便向他们发送电子邮件通知。 默认操作是在门户中显示通知。
7. 单击“确定”按钮。

## <a name="advanced-monitoring"></a>高级监视 

可以使用 [Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview) 以更高级的方法来监视 VM。 可以注册 Operations Management Suite [免费试用版](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-trial)（如果尚未注册）。

如果有权访问 OMS 门户，可以在“设置”边栏选项卡中找到工作区密钥和工作区标识符。 使用 [Set-AzureRmVMExtension](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension) 命令将 OMS 扩展添加到 VM。 更新以下示例中的变量值以反映你的 OMS 工作区密钥和工作区 Id。  

```powershell
$omsId = "<Replace with your OMS Id>"
$omsKey = "<Replace with your OMS key>"

Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
  -ExtensionName "Microsoft.EnterpriseCloud.Monitoring" `
  -VMName myVM `
  -Publisher "Microsoft.EnterpriseCloud.Monitoring" `
  -ExtensionType "MicrosoftMonitoringAgent" `
  -TypeHandlerVersion 1.0 `
  -Settings @{"workspaceId" = $omsId} `
  -ProtectedSettings @{"workspaceKey" = $omsKey} `
  -Location eastus
```

过几分钟后，应该会在 OMS 工作区中看到新 VM。 

![OMS 边栏选项卡](./media/tutorial-monitoring/tutorial-monitor-oms.png)

## <a name="next-steps"></a>后续步骤
在本教程中，你已使用 Azure 安全中心配置并查看了 VM。 你已学习了如何执行以下操作：

> [!div class="checklist"]
> * 创建虚拟网络
> * 创建资源组和 VM 
> * 在 VM 上启用启动诊断
> * 查看启动诊断
> * 查看主机指标
> * 安装诊断扩展
> * 查看 VM 指标
> * 创建警报
> * 设置高级监视

请转到下一教程来了解 Azure 安全中心。

> [!div class="nextstepaction"]
> [管理 VM 安全性](./tutorial-azure-security.md)
