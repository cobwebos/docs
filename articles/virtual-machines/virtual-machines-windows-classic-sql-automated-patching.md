<properties
	pageTitle="对 SQL Server VM（经典）进行自动修补 | Azure"
	description="介绍在 Azure 中运行且使用经典部署模式的 SQL Server 虚拟机的自动修补功能。"
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines-windows"
	ms.date="05/18/2016"
	wacn.date=""/>

# 在 Azure 虚拟机（经典）中对 SQL Server 进行自动修补

> [AZURE.SELECTOR]
- [资源管理器](/documentation/articles/virtual-machines-windows-sql-automated-patching)
- [经典](/documentation/articles/virtual-machines-windows-classic-sql-automated-patching)

自动修补将为运行 SQL Server 的 Azure 虚拟机建立一个维护时段。只能在此维护时段内安装自动更新。对于 SQL Server，这可以确保在数据库的最佳可能时间发生系统更新和任何关联的重新启动。自动修补依赖 [SQL Server IaaS 代理扩展](/documentation/articles/virtual-machines-windows-classic-sql-server-agent-extension)。

[AZURE.INCLUDE [了解部署模型](../includes/learn-about-deployment-models-classic-include.md)] 资源管理器模型。若要查看本文的 Resource Manager 版本，请参阅[在 Azure 虚拟机 Resource Manager 中对 SQL Server 进行自动修补](/documentation/articles/virtual-machines-windows-sql-automated-patching)。

## 先决条件

若要使用自动修补，请考虑以下先决条件：

**操作系统**：

- Windows Server 2012
- Windows Server 2012 R2

**SQL Server 版本**：

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**：

- 如果你打算使用 PowerShell 配置自动修补，请[安装最新的 Azure PowerShell 命令](/documentation/articles/powershell-install-configure)。

>[AZURE.NOTE] 自动修补依赖 SQL Server IaaS 代理扩展。当前的 SQL 虚拟机库映像默认添加此扩展。有关详细信息，请参阅 [SQL Server IaaS 代理扩展](/documentation/articles/virtual-machines-windows-classic-sql-server-agent-extension)。

## 设置

下表描述了可为自动修补配置的选项。实际配置步骤根据你使用的是 Azure 门户还是 Azure Windows PowerShell 命令而有所不同。

|设置|可能的值|说明|
|---|---|---|
|**自动修补**|启用/禁用（已禁用）|为 Azure 虚拟机启用或禁用自动修补。|
|**维护计划**|每天、星期一、星期二、星期三、星期四、星期五、星期六、星期日|为虚拟机下载和安装 Windows、SQL Server 和 Microsoft 更新的计划。|
|**维护开始时间**|0-24|更新虚拟机的本地开始时间。|
|**维护时段持续时间**|30-180|允许完成更新下载和安装的分钟数。|
|**修补程序类别**|重要说明|要下载并安装的更新类别。|

## 门户中的配置

在经典部署模型中创建新的 SQL Server 虚拟机时，可以使用 [Azure 门户](https://portal.azure.cn/)配置自动修补。

以下 Azure 门户屏幕截图显示了“可选配置”|“SQL 自动修补”下的这些选项。

![Azure 门户中的 SQL 自动修补](./media/virtual-machines-windows-classic-sql-automated-patching/IC778484.jpg)

对于现有的 SQL Server 2012 或 2014 虚拟机，请在虚拟机属性的“配置”部分选择“自动修补”设置。在“自动修补”窗口中，可以启用功能、设置维护计划和起始小时，以及选择维护时段持续时间。如以下屏幕快照中所示。

![Azure 门户中的自动修补配置](./media/virtual-machines-windows-classic-sql-automated-patching/IC792132.jpg)

>[AZURE.NOTE] 当你首次启用自动修补时，Azure 将在后台配置 SQL Server IaaS 代理。在此期间，Azure 门户可能不会显示已配置自动修补。请等待几分钟，以便安装和配置代理。之后，Azure 门户将反映新设置。

## 使用 PowerShell 进行配置

你也可以使用 PowerShell 来配置自动修补。

以下示例使用 PowerShell 在现有的 SQL Server VM 上配置自动修补。**New-AzureVMSqlServerAutoPatchingConfig** 命令将为自动更新配置新的维护时段。

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

下表根据此示例描述了对目标 Azure VM 产生的实际效果：

|参数|效果|
|---|---|
|**DayOfWeek**|每个星期四安装修补程序。|
|**MaintenanceWindowStartingHour**|在上午 11:00 开始更新。|
|**MaintenanceWindowsDuration**|必须在 120 分钟内安装修补程序。根据开始时间，修补必须在下午 1:00 之前完成。|
|**PatchCategory**|此参数的唯一可能设置为“Important”。|

可能需要花费几分钟来安装和配置 SQL Server IaaS 代理。

若要禁用自动修补，请对 New-AzureVMSqlServerAutoPatchingConfig 运行不带 -Enable 参数的同一个脚本。与安装一样，可能需要花费几分钟时间来禁用自动修补。

## 后续步骤

有关其他可用自动化任务的信息，请参阅 [SQL Server IaaS 代理扩展](/documentation/articles/virtual-machines-windows-classic-sql-server-agent-extension)。

有关在 Azure VM 中运行 SQL Server 的详细信息，请参阅 [Azure 虚拟机中的 SQL Server 概述](/documentation/articles/virtual-machines-windows-sql-server-iaas-overview)。

<!---HONumber=Mooncake_0704_2016-->