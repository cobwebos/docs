---
title: "在 Azure 堆栈中运行的验证测试 |Microsoft 文档"
description: "如何收集诊断 Azure 堆栈中的日志文件"
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: mabrigg
ms.openlocfilehash: 53ef19628b40c4a008143c867c9e7867ac91854d
ms.sourcegitcommit: 562a537ed9b96c9116c504738414e5d8c0fd53b1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2018
---
# <a name="run-a-validation-test-for-azure-stack"></a>为 Azure 堆栈运行验证测试

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*
 
你可以验证 Azure 堆栈的状态。 当你遇到问题时，请联系 Microsoft 客户服务支持。 支持要求你从你管理的节点上运行测试 AzureStack。 验证测试隔离失败。 支持可以然后分析详细的日志、 重点放在发生错误的区域和工作与你解决此问题。

## <a name="run-test-azurestack"></a>运行测试 AzureStack

当你遇到问题时，请联系 Microsoft 客户服务支持，然后运行**运行的测试 AzureStack**。

1. 你遇到的问题。
2. 联系 Microsoft 客户服务支持。
3. 运行**测试 AzureStack**从特权的终结点。
    1. 访问特权终结点。 有关说明，请参阅[Azure 堆栈中使用的特权的终结点](azure-stack-privileged-endpoint.md)。 
    2. 以登录**AzureStack\CloudAdmin**管理主机上。
    3. 以管理员身份打开 PowerShell。
    4. 运行：`Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. 运行：`Test-AzureStack`
4. 如果任何测试报告失败，运行： `Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>` cmdlet 从测试 AzureStack 收集日志。 有关诊断日志的详细信息，请参阅[Azure 堆栈诊断工具](azure-stack-diagnostics.md)。
5. 发送**SeedRing**向 Microsoft 客户服务支持的日志。 Microsoft 客户服务支持适用于你要解决此问题。

## <a name="reference-for-test-azurestack"></a>测试 AzureStack 的引用

本部分概述了适合测试 AzureStack cmdlet，并验证报表的摘要。

### <a name="test-azurestack"></a>测试 AzureStack

验证 Azure 堆栈的状态。 该 cmdlet 报告 Azure 堆栈硬件和软件的状态。 支持人员可以使用此报表以减少解决 Azure 堆栈支持案例的时间。

> [!Note]  
> 测试 AzureStack 可能检测不会导致云服务中断的故障，如单个失败磁盘故障或单个物理主机节点故障。

#### <a name="syntax"></a>语法

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>parameters

| 参数               | 值           | 需要 | 默认 |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | 否       | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | 否       | FALSE   |
| AdminCredential         | PSCredential    | 否       | 不可用      |
| StorageConnectionString | String          | 否       | 不可用      |
| 列出                    | SwitchParameter | 否       | FALSE   |
| 忽略                  | String          | 否       | 不可用      |
| 包括                 | String          | 否       | 不可用      |

测试 AzureStack cmdlet 支持以下常见参数： Verbose、 调试、 ErrorAction、 ErrorVariable、 WarningAction、 WarningVariable、 OutBuffer、 PipelineVariable 和 OutVariable。 有关详细信息，请参阅[有关常见参数](http://go.microsoft.com/fwlink/?LinkID=113216)。 

### <a name="examples-of-test-azurestack"></a>测试 AzureStack 的示例

下面的示例假定你已作为登录**CloudAdmin**和访问特权终结点 (PEP)。 有关说明，请参阅[Azure 堆栈中使用的特权的终结点](azure-stack-privileged-endpoint.md)。 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>没有云方案的情况下以交互方式运行测试 AzureStack

在 PEP 会话中，运行：

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>使用云方案中运行测试 AzureStack

测试 AzureStack 可用于针对 Azure 堆栈运行云方案。 这些方案包括：

 - 创建资源组
 - 创建计划
 - 创建提议
 - 创建存储帐户
 - 创建虚拟机
 - 执行使用在测试方案中创建的存储帐户的 blob 操作
 - 执行队列操作使用在测试方案中创建的存储帐户
 - 执行使用在测试方案中创建的存储帐户的表操作

云方案需要的云管理员凭据。 
> [!Note]  
> 不能运行使用 Active Directory 联合服务 (ADFS) 凭据的云方案。 **测试 AzureStack** cmdlet 才可通过 PEP 访问。 但是，PEP 不支持 ADFS 凭据。

以 UPN 格式键入云管理员用户名serviceadmin@contoso.onmicrosoft.com(AAD)。 出现提示时，键入云管理员帐户的密码。

在 PEP 会话中，运行：

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>没有云方案的情况下运行测试 AzureStack

在 PEP 会话中，运行：

````PowerShell
  $session = New-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>列出可用的测试方案：

在 PEP 会话中，运行：

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>运行指定的测试

在 PEP 会话中，运行：

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

若要排除特定的测试：

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>验证测试

下表总结了运行测试 AzureStack 的验证测试。

| 名称                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Azure 堆栈云托管基础结构摘要                                                                                  |
| Azure 堆栈存储服务摘要                                                                                              |
| Azure 堆栈基础结构角色实例摘要                                                                                  |
| Azure 堆栈云托管基础结构使用率                                                                              |
| Azure 堆栈基础结构容量                                                                                               |
| Azure 堆栈门户和 API 摘要                                                                                                |
| Azure 堆栈 Azure 资源管理器证书摘要                                                                                               |
| 基础结构管理控制器、 网络控制器、 存储服务和特权终结点基础结构角色          |
| 基础结构管理控制器、 网络控制器、 存储服务和特权终结点基础结构角色实例 |
| Azure 堆栈基础结构角色摘要                                                                                           |
| Azure 堆栈云 Service Fabric 服务                                                                                         |
| Azure 堆栈基础结构角色实例性能                                                                              |
| Azure 堆栈云主机性能摘要                                                                                        |
| Azure 堆栈服务资源消耗摘要                                                                                  |
| Azure 堆栈缩放单元关键事件 （最后一个 8 小时数）                                                                             |
| Azure 堆栈存储服务的物理磁盘摘要                                                                               |

## <a name="next-steps"></a>后续步骤

 - 若要了解有关 Azure 堆栈诊断工具和问题日志记录的详细信息，请参阅[Azure 堆栈诊断工具](azure-stack-diagnostics.md)。
 - 若要了解有关疑难解答的详细信息，请参阅[Microsoft Azure 堆栈故障排除](azure-stack-troubleshooting.md)