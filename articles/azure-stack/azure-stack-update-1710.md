---
title: Azure Stack 1710 更新（内部版本 20171020.1）| Microsoft Docs
description: 了解 Azure Stack 集成系统 1710 版的新增功能、已知问题和更新下载位置。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 135314fd-7add-4c8c-b02a-b03de93ee196
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: mabrigg
ms.openlocfilehash: 8c7c39ecdc332c994e5c00f8415462f208e7d20b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2018
---
# <a name="azure-stack-1710-update-build-201710201"></a>Azure Stack 1710 更新（内部版本 20171020.1）

*适用于：Azure Stack 集成系统*

本文介绍此更新包中的改进与修复、此版本的已知问题，以及更新的下载位置。 已知问题分为直接与更新过程相关的已知问题，以及内部版本（安装后）相关的已知问题。

> [!IMPORTANT]
> 此更新包仅适用于 Azure Stack 集成系统。 请勿将此更新包应用于 Azure Stack 开发工具包。

## <a name="improvements-and-fixes"></a>改进和修复

此更新包含以下适用于 Azure Stack 的质量改进和修复。
 
### <a name="windows-server-2016-improvements-and-fixes"></a>Windows Server 2016 的改进和修复

- 适用于 Windows Server 2016 的更新：2017 年 10 月 10 日 — KB4041691（OS 内部版本 14393.1770）。 请参阅[ https://support.microsoft.com/help/4041691 ](https://support.microsoft.com/help/4041691)有关详细信息。

### <a name="additional-quality-improvements-and-fixes"></a>其他质量改进和修复

- 已添加特权终结点 PowerShell cmdlet，帮助对网络时间协议 (NTP) 服务器进行故障排除和更新。
- 已添加对更新特权终结点 Just Enough Administration (JEA) 终结点模块和 cmdlet 允许列表的支持。 
- 已修复特权终结点中的本地语言错误。
- 已添加轮换网关凭据的功能。
- 已删除 CBLocalAdmin 本地管理员帐户。 
- 已修复检测信号警报模板内容，确保检测信号警报可在更新之后正常工作。
- 已修复在 FRU 操作期间处理超时的结构资源提供程序。 
- 已为云开发人员添加在 Azure Stack 上使用 Azure 资源管理器 API 配置文件的功能。
- 已禁用部署虚拟机 (DVM) 上的 Windows 更新服务。 
- 已从用户界面中删除节点开机/关机操作。
- 其他多项性能和稳定性修复。 
 
## <a name="known-issues-with-the-update-process"></a>更新过程的已知问题

本部分描述在安装 1710 更新期间可能遇到的已知问题。

> [!IMPORTANT]
> 如果更新失败，稍后尝试恢复更新时，必须从特权终结点使用 `Resume-AzureStackUpdate` cmdlet。 请勿使用管理员门户来恢复更新。 （这是此版本的已知问题。）有关详细信息，请参阅[使用特权终结点监视 Azure Stack 中的更新](azure-stack-monitor-update.md)。

| 症状  | 原因  | 解决方法 |
|---------|---------|---------|
|在更新过程中，执行更新操作计划的<br>“存储主机重启存储节点”步骤时可能发生<br> 类似于下面的错误。<br><br>**{"name":"Restart Storage Hosts","description":"Restart<br> Storage Hosts.","errorMessage":"Type 'Restart' of Role<br> 'BareMetal' raised an exception:\n\n The computer<br> HostName-05 is skipped.Fail to retrieve its LastBootUpTime<br> via the WMI service with the following error message:<br> The RPC server is unavailable.<br>(Exception from HRESULT: 0x800706BA).\nat Restart-Host** | 此问题的原因是某些配置中可能存在有故障的驱动程序。 | 1.登录到基板管理控制器 (BMC) Web 界面，然后重启错误消息中指定的主机。<br><br>2.使用特权终结点来恢复更新。 |
| 在更新过程中，执行更新操作计划的<br> “步骤: 正在运行步骤 2.4 - 安装更新”之后，更新过程似乎停滞<br> 而不会继续。<br><br>然后，此步骤后接一连串将 .nupkg 文件复制到内部基础结构<br> 文件共享的进程。 例如：<br><br>**Copying 1 files from content\PerfCollector\VirtualMachines to <br> \VirtualMachineName-ERCS03\C$\TraceCollectorUpdate\ <br>PerfCounterConfiguration**<br><br>或显示以下消息：<br><br>**WarningMessage:Task: Invocation of interface 'LiveUpdate'<br> of role 'Cloud\Fabric\VirtualMachines' failed:<br> Type 'LiveUpdate' of Role 'VirtualMachines' raised an<br> exception: There is not enough space on the disk.**  | 此问题的原因是日志文件即将填满基础结构虚拟机上的磁盘，并且 Windows Server 横向扩展文件服务器 (SOFS) 中出现问题，后续的更新将解决此问题。 | 请联系 Microsoft 客户服务和支持 (CSS) 寻求帮助。 | 
| 在更新过程中，执行更新操作计划的<br> “步骤: 正在运行步骤 2.13.2 - 更新<br> *VM_Name*”时出现类似于下面的错误。 （虚拟机<br> 名称可能与此不同。）<br><br>**ActionPlanInstanceWarning ece/MachineName:<br> WarningMessage:Task: Invocation of interface 'LiveUpdate' of<br> role 'Cloud\Fabric\WAS'failed: Type 'LiveUpdate' of Role<br> 'WAS' raised an exception: ERROR during storage<br> initialization: An error occurred while trying to make an API<br> call to Microsoft Storage service: {"Message": "A timeout<br> occurred while communicating with Service Fabric.<br>Exception Type: TimeoutException.<br>Exception message: Operation timed out."}**  | 此问题的原因是 Windows Server 中发生 I/O 超时，后续的更新将解决此问题。 | 请联系 Microsoft CSS 寻求帮助。
| 在更新过程中，执行更新操作计划的<br> “步骤 21: 重启 SQL server 虚拟机”时出现类似于下面的错误。<br><br>**Type 'LiveUpdateRestart' of Role 'VirtualMachines' raised an<br> exception: VerboseMessage:[VirtualMachines:LiveUpdateRestart]<br> Querying for VM MachineName-Sql01. - 10/13/2017 5:11:50 PM VerboseMessage:[VirtualMachines:LiveUpdateRestart]<br> VM is marked as HighlyAvailable. - 10/13/2017 5:11:50 PM<br> VerboseMessage:[VirtualMachines:LiveUpdateRestart] at<br>MS.Internal.ServerClusters.ExceptionHelp.Build at<br>MS.Internal.ServerClusters.ClusterResource.BeginTakeOffline<br>(Boolean force) at Microsoft.FailoverClusters.PowerShell.<br>StopClusterResourceCommand.BeginTimedOperation() at <br>Microsoft.FailoverClusters.PowerShell.TimedCmdlet.Wrapped<br>ProcessRecord() at  Microsoft.FailoverClusters.PowerShell.<br>FCCmdlet.ProcessRecord() - 10/13/2017 5:11:50 PM Warning<br>Message: Task: Invocation of interface 'LiveUpdateRestart' of<br> role 'Cloud\Fabric\VirtualMachines' failed:** | 如果虚拟机无法重启，则可能出现此问题。 | 请联系 Microsoft CSS 寻求帮助。
| 执行更新时，可能出现类似于下面的错误：<br><br>**2017-10-22T01:37:37.5369944Z Type 'Shutdown' of Role 'SQL'<br> raised an exception: An error occurred pausing node<br> 's45r1004-Sql01'.at Stop-SQL, C:\ProgramData\SF\ErcsClusterNode2 <br>\Fabric\work\Applications\ EnterpriseCloud <br>EngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\ <br> CloudDeployment\Roles\SQL\SQL.psm1:line 542 at<br> Shutdown,C:\ProgramData\SF\ErcsClusterNode2\Fabric\work\ <br>Applications \EnterpriseCloudEngineApplicationType&#95;App1\ <br>EnterpriseCloudEngineServicePkg.Code.1.0.597.18\Cloud<br>Deployment\Classes\SQL\SQL.psm1: line 50 at <ScriptBlock&#62;,<br> <No file>: line 18 at <ScriptBlock&#62;, <No file&#62;: line 16** | 如果无法将虚拟机置于暂停状态以清空角色，则可能出现此问题。 | 请联系 Microsoft CSS 寻求帮助。
| 执行更新时，可能会出现以下错误之一：<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation<br> for ADFS/Graph role failed with error: Error checking ADFS<br> probe endpoint *endpoint_URI*: Exception calling<br> "GetResponse" with "0" argument(s): "The remote server<br> returned an error: (503) Server Unavailable." at Invoke-<br>ADFSGraphValidation**<br><br>**Type 'Validate' of Role 'ADFS' raised an exception: Validation<br> for ADFS/Graph role failed with error: Error fetching<br> ADFS properties: Could not connect to <br>net.tcp://localhost:1500/policy.The connection attempt lasted<br> for a time span of 00:00:02.0498923.TCP error code<br> 10061: No connection could be made because the target<br> machine actively refused it 127.0.0.1:1500.<br> at Invoke-ADFSGraphValidation** | 更新操作计划无法验证 Active Directory 联合身份验证服务 (AD FS) 的运行状况。 | 请联系 Microsoft CSS 寻求帮助。

## <a name="known-issues-post-installation"></a>已知问题（安装后）

本部分包含内部版本 20171020.1 的安装后已知问题。

### <a name="portal"></a>门户

- 无法在管理员门户中查看计算或存储资源。 这表示更新安装期间出错，并误将更新报告为成功。 如果发生此问题，请联系 Microsoft CSS 寻求帮助。
- 可能会在门户中看到空白的仪表板。 若要恢复仪表板，请选择门户右上角的齿轮图标，然后选择“还原默认设置”。
- 查看资源组的属性时，“移动”按钮已禁用。 这是预期的行为。 目前不支持在订阅之间移动资源组。
- 对于可在下拉列表中选择订阅、资源组或位置的任何工作流，可能会遇到一个或多个以下问题：

   - 可能会在列表顶部看到一个空白行。 应该仍能按预期选择项。
   - 如果下拉列表中的项列表很短，可能无法查看任何项名称。
   - 如果有多个用户订阅，资源组的下拉列表可能是空的。 

   若要解决后两个问题，可以输入订阅或资源组的名称（如果知道），或者可以改用 PowerShell。
- 删除用户订阅生成孤立的资源。 解决方法是先删除用户资源或整个资源组，然后再删除用户订阅。
- 无法使用 Azure Stack 门户查看订阅的权限。 解决方法是使用 PowerShell 验证权限。
- **服务运行状况**边栏选项卡无法加载。 当你打开的服务运行状况边栏选项卡在管理员或用户门户中，Azure 堆栈会显示错误并不会加载信息。 这是预期的行为。 尽管你可以选择并打开服务运行状况，但此功能尚不可用，但将实现 Azure 堆栈的未来版本中。
 

### <a name="backup"></a>备份

- 请不要在“基础架构备份”边栏选项卡上启用基础结构备份。

### <a name="health-and-monitoring"></a>运行状况和监视

- 如果重新启动基础结构角色实例，可能会收到一条消息，指出重新启动失败。 但实际上重新启动已成功。

### <a name="marketplace"></a>Marketplace
- 尝试使用“从 Azure 添加”选项将项添加到 Azure Stack Marketplace 时，可能无法看到所有可供下载的项。
- 用户无需订阅就能浏览整个 Marketplace，并且将会看到计划和产品/服务等管理项。 对用户而言，这些项是非功能性的。

### <a name="compute"></a>计算
- 用户可以使用相应的选项创建包含异地冗余存储的虚拟机。 此配置会导致虚拟机创建失败。
- 可以配置只包含一个容错域和一个更新域的虚拟机可用性集。
- 没有任何可用于创建虚拟机规模集的 Marketplace 体验。 可以使用模板来创建规模集。
- 无法在门户中使用虚拟机规模集的缩放设置。 解决方法是使用 [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set)。 由于 PowerShell 版本差异，必须使用 `-Name` 参数，而不是 `-VMScaleSetName`。
 
### <a name="networking"></a>网络
- 无法使用门户创建采用公共 IP 地址的负载均衡器。 解决方法是使用 PowerShell 创建负载均衡器。
- 创建网络负载均衡器时，必须创建网络地址转换 (NAT) 规则。 否则，在创建负载均衡器之后尝试添加 NAT 规则时会收到错误。
- 创建 VM 并与公共 IP 地址创建关联之后，无法取消该 IP 地址与虚拟机 (VM) 的关联。 取消关联看似正常运行，但以前分配的公共 IP 地址仍与原始 VM 相关联。 即使将 IP 地址重新分配给新的 VM（通常名为“VIP 交换”），也还会发生这种行为。 以后尝试通过此 IP 地址建立连接都会导致连接到原先关联的 VM，而不是新的 VM。 目前只有在创建新的 VM 时，才能使用新的公共 IP 地址。
 
### <a name="sqlmysql"></a>SQL/MySQL
- 最长可能需要在一小时后，租户才能在新的 SQL 或 MySQL SKU 中创建数据库。 
- 不支持在 SQL 和 MySQL 托管服务器中直接创建不是由资源提供程序执行的项，这可能导致不匹配的状态。
 
### <a name="app-service"></a>应用服务
- 在订阅中创建第一个 Azure 函数之前，用户必须先注册存储资源提供程序。
 
### <a name="field-replaceable-unit-fru-procedures"></a>现场可更换单元 (FRU) 过程

- 在更新运行期间，无法修补脱机映像。 如果需要更换缩放单位节点，请咨询 OEM 硬件供应商，确保更换的节点具有最新修补级别。

## <a name="download-the-update"></a>下载更新

可在[此处](https://aka.ms/azurestackupdatedownload)下载 1710 更新包。

## <a name="next-steps"></a>后续步骤

- 有关 Azure Stack 中的更新管理的概述，请参阅[有关在 Azure Stack 中管理更新的概述](azure-stack-updates.md)。
- 有关如何应用更新的信息，请参阅[在 Azure Stack 中应用更新](azure-stack-apply-updates.md)。
