使用[虚拟机规模集](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)和 [Azure Monitor 的自动缩放功能](../articles/monitoring-and-diagnostics/monitoring-overview-autoscale.md)时，可以轻松[自动缩放](../articles/monitoring-and-diagnostics/insights-autoscale-best-practices.md)[虚拟机 (VM)](../articles/virtual-machines/windows/overview.md)。 VM 需要成为规模集的成员才能自动缩放。 本文提供了用于更好地了解如何使用自动和手动方法以纵向方式和横向方式缩放 VM 的信息。

## <a name="horizontal-or-vertical-scaling"></a>横向缩放或纵向缩放

Azure Monitor 的自动缩放功能仅以横向方式调整资源的规模，即增加（“放”）或减少（“缩”）VM 的数量。 横向缩放在使用云服务的情况下更为灵活，因为这样可以运行数千个 VM 来处理负载。 可通过自动或手动更改规模集容量（或实例计数）进行横向缩放。 

纵向缩放保持 VM 数量不变，但会增强（“提高”）或削弱（“降低”）VM 的功能。 功能以内存、CPU 速度或磁盘空间等属性进行度量。 纵向缩放依赖于较大型硬件的可用性，此可用性会快速达到上限，并因区域而异。 纵向缩放通常还需要停止和重新启动 VM。 可通过在规模集的 VM 配置中设置新大小来进行纵向缩放。

在 [Azure 自动化](../articles/automation/automation-intro.md)中使用 runbook，可轻松地[增加或减少规模集中的 VM](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-vertical-scale-reprovision.md)。

## <a name="create-a-virtual-machine-scale-set"></a>创建虚拟机规模集

使用规模集可以轻松地将相同的 VM 作为集来进行部署和管理。 可以使用 [Azure 门户](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-portal-create.md)、[Azure PowerShell](../articles/virtual-machines/windows/tutorial-create-vmss.md) 或 [Azure CLI](../articles/virtual-machines/linux/tutorial-create-vmss.md) 创建 Linux 或 Windows 规模集。 还可以使用 SDK（如 [Python](/develop/python) 或 [Node.js](/nodejs/azure)）或直接使用 [REST API](/rest/api/compute/virtualmachinescalesets) 创建和管理规模集。 VM 的自动缩放通过将指标和规则应用于规模集来完成。

## <a name="configure-autoscale-for-a-scale-set"></a>为规模集配置自动缩放

自动缩放提供适当数量的 VM 来处理应用程序上的负载。 使用它，可以添加 VM 来处理增加的负载，并可以删除处于空闲状态的 VM 来节省资金。 需要基于一组规则来指定要运行的最小 VM 数目和最大 VM 数目。 设置最小数目可确保应用程序在没有负载的情况下也会运行。 设置最大值是为了限制每小时可能会产生的总成本。

可以在使用 [Azure PowerShell](../articles/monitoring-and-diagnostics/insights-powershell-samples.md#create-and-manage-autoscale-settings) 或[Azure CLI](https://docs.microsoft.com/cli/azure/monitor/autoscale-settings) 创建规模集时启用自动缩放。 也可以在创建规模集后启用自动缩放。 可以使用 [Azure 资源管理器模板](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md)创建规模集、安装扩展以及配置自动缩放。 在 Azure 门户中，可以从 Azure Monitor 启用自动缩放，也可以从规模集设置启用自动缩放。

![启用自动缩放](./media/virtual-machines-autoscale/virtual-machines-autoscale-enable.png)
 
### <a name="metrics"></a>度量值

使用 Azure Monitor 的自动缩放功能，可以基于[指标](../articles/monitoring-and-diagnostics/insights-autoscale-common-metrics.md)增加或减少正在运行的 VM 数量。 默认情况下，VM 针对磁盘、网络和 CPU 使用情况提供基本的主机级别指标。 使用诊断扩展配置诊断数据收集时，额外的来宾 OS 性能计数器将可用于磁盘、CPU 和内存。

![指标条件](./media/virtual-machines-autoscale/virtual-machines-autoscale-criteria.png)

如果应用程序需要基于无法通过主机获得的指标进行缩放，则规模集中的 VM 需要安装 [Linux 诊断扩展](../articles/virtual-machines/linux/diagnostic-extension.md)或 [Windows 诊断扩展](../articles/virtual-machines/windows/ps-extensions-diagnostics.md)。 如果使用 Azure 门户创建规模集，则还需要使用 Azure PowerShell 或 Azure CLI 安装带有所需诊断配置的扩展。
 
### <a name="rules"></a>规则

[规则](../articles/monitoring-and-diagnostics/monitoring-autoscale-scale-by-custom-metric.md)将某个指标与要执行的操作结合起来。 当满足规则条件时，将触发一个或多个自动缩放操作。 例如，可能会有一个规则，它定义了如果平均 CPU 使用率高于 85%，则将 VM 数量加 1。

![自动缩放操作](./media/virtual-machines-autoscale/virtual-machines-autoscale-actions.png)
 
### <a name="notifications"></a>通知

可以[设置触发器](../articles/monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md)，以便基于你创建的自动缩放规则调用特定 Web URL 或发送电子邮件。 通过 webhook 可以将 Azure 警报通知路由到其他系统以便用于后处理或自定义通知。

## <a name="manually-scale-vms-in-a-scale-set"></a>手动缩放规模集中的 VM

### <a name="horizontal"></a>横向

可以通过更改规模集的容量添加或删除 VM。 在 Azure 门户中，可以通过在“缩放”屏幕上向左或向右滑动“替代条件”块来减小或增大规模集中 VM 的数量（显示为**实例计数**）。

如果使用 Azure PowerShell，则需要使用 [Get-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermvmss) 获取规模集对象。 然后将 **sku.capacity** 属性设置为所需 VM 的数量，并使用 [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) 更新规模集。 如果使用 Azure CLI，则使用 [az vmss scale](https://docs.microsoft.com/cli/azure/vmss#az_vmss_scale) 命令的 **--new-capacity** 参数更改容量。

### <a name="vertical"></a>垂直

可以在 Azure 门户中的“大小”屏幕上手动更改规模集的 VM 大小。 可以在 Azure PowerShell 中使用 Get-AzureRmVmss，设置映像引用 sku 属性，然后使用 [Update-AzureRmVmss](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmss) 和 [Update-AzureRmVmssInstance](https://docs.microsoft.com/powershell/module/azurerm.compute/update-azurermvmssinstance)。

## <a name="next-steps"></a>后续步骤

- 在[规模集的设计注意事项](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview.md)中了解有关规模集的详细信息。

