---
title: Azure Stack 中的诊断
description: 如何收集日志文件以在 Azure Stack 中进行诊断
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 06/27/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: 50fef25a3b7b71821e64638729eb8d93f65b9e31
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37064162"
---
# <a name="azure-stack-diagnostics-tools"></a>Azure Stack 诊断工具

Azure Stack 是一个大型集合，其中的组件可以一起工作并互相交互。 所有这些组件会生成自己独特的日志。 这样一来，问题可能就难以诊断，尤其是在错误来自多个交互的 Azure Stack 组件的情况下。 

我们的诊断工具有助于确保日志收集机制易用且高效。 下图演示了如何在 Azure Stack 中使用日志收集工具：

![Azure Stack 诊断工具](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>跟踪收集器
 
跟踪收集器默认启用，可以在后台持续运行，以便从 Azure Stack 组件服务收集所有 Windows 事件跟踪 (ETW) 日志。 ETW 日志存储在一个常用的本地共享中，其时间限制为五天。 一旦达到此限制，就会在创建新文件时删除最旧的文件。 每个文件默认允许的最大大小为 200 MB。 每 2 分钟进行一次大小检查，如果当前文件 >= 200 MB，则会保存该文件并生成新文件。 按事件会话生成的文件的总大小也存在 8 GB 的限制。 

## <a name="log-collection-tool"></a>日志集合工具
 
可以使用 PowerShell cmdlet **Get-AzureStackLog** 从 Azure Stack 环境中的所有组件收集日志。 此工具将日志以 zip 文件形式保存在用户定义的位置。 如果 Azure Stack 技术支持团队需要日志来排查问题，他们可能要求你运行此工具。

> [!CAUTION]
> 这些日志文件可能包含个人身份信息 (PII)。 在公开发布任何日志文件之前，请考虑到这一因素。
 
下面是一些收集的示例日志类型：
*   **Azure Stack 部署日志**
*   **Windows 事件日志**
*   **Panther 日志**
*   **群集日志**
*   **存储诊断日志**
*   **ETW 日志**

这些文件由跟踪收集器收集并保存在共享中。 然后，可以根据需要使用 **Get-AzureStackLog** PowerShell cmdlet 来收集它们。

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>若要在 Azure 堆栈上运行 Get AzureStackLog 集成系统 
若要在集成系统上运行日志收集工具，需访问特权终结点 (PEP)。 下面是一个可以通过 PEP 来运行的示例脚本，用于在集成系统上收集日志：

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```

- 参数 **OutputSharePath** 和 **OutputShareCredential** 用于将日志上传到外部共享文件夹。
- 如上一示例所示，可以使用 **FromDate** 和 **ToDate** 参数来收集特定时间段的日志。 在某些情况下（例如，在集成系统上应用更新包以后收集日志），可以使用这种方法。


 
### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>在 Azure Stack 开发工具包 (ASDK) 系统上运行 Get-AzureStackLog
1. 以登录**AzureStack\CloudAdmin**主机上。
2. 以管理员身份打开 PowerShell 窗口。
3. 运行 **Get-AzureStackLog** PowerShell cmdlet。

**示例：**

  收集所有角色的所有日志：

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs
  ```

  从 VirtualMachines 和 BareMetal 角色收集日志：

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal
  ```

  从 VirtualMachines 和 BareMetal 角色收集日志，通过日期筛选功能筛选出过去 8 小时的日志文件：
    
  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  从 VirtualMachines 和 BareMetal 角色收集日志，通过日期筛选功能筛选出 8 小时前到 2 小时前这个时间段的日志文件：

  ```powershell
  Get-AzureStackLog -OutputPath C:\AzureStackLogs -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>ASDK 系统和集成系统的参数考虑事项

- 如果未指定 **FromDate** 和 **ToDate** 参数，则默认收集过去四小时的日志。
- 可以使用 **TimeOutInMinutes** 参数设置日志收集的超时。 它默认设置为 150（2.5 小时）。
- 在版本 1805年及更高版本中，默认情况下禁用的转储文件日志收集。 若要启用它，使用**IncludeDumpFile**开关参数。 
- 目前，可以使用 **FilterByRole** 参数按以下角色筛选日志收集：

   |   |   |   |
   | - | - | - |
   | ACS                    | DeploymentMachine                | NC                         |
   | ACSBlob                | DiskRP                           | 网络                    |
   | ACSFabric              | 域                           | NonPrivilegedAppGateway    |
   | ACSFrontEnd            | ECE                              | NRP                        |
   | ACSMetrics             | ExternalDNS                      | OEM                        |
   | ACSMigrationService    | Fabric                           | PXE                        |
   | ACSMonitoringService   | FabricRing                       | SeedRing                   | 
   | ACSSettingsService     | FabricRingServices               | SeedRingServices           |
   | ACSTableMaster         | FRP                              | SLB                        |   
   | ACSTableServer         | 库                          | SlbVips                    |
   | ACSWac                 | 网关                          | SQL                        |   
   | ADFS                   | HealthMonitoring                 | SRP                        |
   | ASAppGateway           | HRP                              | 存储                    |   
   | NCAzureBridge          | IBC                              | StorageAccounts            |    
   | AzurePackConnector     | IdentityProvider                 | StorageController          |  
   | AzureStackBitlocker    | iDns                             | 租户                     |
   | BareMetal              | InfraServiceController           | TraceCollector             |
   | BRP                    | 基础结构                   | URP                        |
   | CA                     | KeyVaultAdminResourceProvider    | UsageBridge                |
   | 云                  | KeyVaultControlPlane             | VirtualMachines            |
   | 群集                | KeyVaultDataPlane                | WAS                        |
   | 计算                | KeyVaultInternalControlPlane     | WASBootstrap               |
   | CPI                    | KeyVaultInternalDataPlane        | WASPUBLIC                  |
   | CRP                    | KeyVaultNamingService            |                            |
   | DatacenterIntegration  | MonitoringAgent                  |                            |
   |                        |                                  |                            |

### <a name="bkmk_gui"></a>使用图形用户界面收集日志
也可利用在主 Azure Stack 工具 GitHub 工具存储库 http://aka.ms/AzureStackTools 中提供的开源 Azure Stack 工具，而不必为 Get-AzureStackLog cmdlet 提供所需的参数来检索 Azure Stack 日志。

**ERCS_AzureStackLogs.ps1** PowerShell 脚本存储在 GitHub 工具存储库中，并定期进行更新。 若要确保使用发布的最新版本，应直接从 http://aka.ms/ERCS 下载该版本。 从 PowerShell 管理会话启动以后，此脚本会连接到特权终结点并使用提供的参数运行 Get-AzureStackLog。 如果未提供参数，此脚本会默认通过图形用户界面提示你提供参数。

若要了解有关 ERCS_AzureStackLogs.ps1 PowerShell 脚本的详细信息，你可以观看[简短视频](https://www.youtube.com/watch?v=Utt7pLsXEBc)或查看脚本的[自述文件](https://github.com/Azure/AzureStack-Tools/blob/master/Support/ERCS_Logs/ReadMe.md)位于 Azure 堆栈工具 GitHub 存储库。 

### <a name="additional-considerations"></a>其他注意事项

* 此命令需要一些时间来运行，具体取决于日志所收集的角色。 影响因素还包括指定用于日志收集的时限，以及 Azure Stack 环境中的节点数。
* 当日志收集运行时，请查看在 **OutputSharePath** 参数（在命令中指定）中创建的新文件夹。
* 每个角色的日志位于各个 zip 文件中。 根据所收集日志的大小，一个角色的日志可能会拆分成多个 zip 文件。 对于此类角色，如果需要将所有日志文件解压缩到单个文件夹中，请使用可以批量解压缩的工具（例如 7zip）。 选择角色的所有压缩文件，然后选择“解压缩到此处”。 这样就会将该角色的所有日志文件解压缩到单个合并的文件夹中。
* 在压缩的日志文件所在的文件夹中，还会创建名为 **Get-AzureStackLog_Output.log** 的文件。 此文件是一个命令输出日志，可以用来排查日志收集过程中的问题。 日志文件包含有时`PS>TerminatingError`可以安全地忽略，除非后缺少所预期的日志文件条目记录集合运行。
* 调查某个特定的故障时，可能需要多个组件中的日志。
    -   所有基础结构 VM 的系统和事件日志收集在 *VirtualMachines* 角色中。
    -   所有主机的系统和事件日志收集在 *BareMetal* 角色中。
    -   故障转移群集和 Hyper-V 事件日志收集在“存储”角色中。
    -   ACS 日志收集在“存储”角色和 *ACS* 角色中。

> [!NOTE]
> 会对收集的日志强制实施大小和保留时间限制，因为必须确保对存储空间进行有效的利用，从而确保该空间不会充斥着日志。 但是，在诊断问题时，有时可能需要某些日志，但这些日志可能因为这些限制而不再存在了。 因此，**强烈建议**你每隔 8 到 12 小时就将日志卸载到外部存储空间（Azure 中的存储帐户、其他本地存储设备，等等）并在该处保留 1 - 3 月，具体取决于你的要求。 另外，请确保该存储位置已加密。

## <a name="next-steps"></a>后续步骤
[Microsoft Azure Stack 故障排除](azure-stack-troubleshooting.md)

