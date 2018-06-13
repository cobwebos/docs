---
title: 在 Azure Stack 中运行验证测试 | Microsoft Docs
description: 如何收集日志文件以在 Azure Stack 中进行诊断
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
ms.date: 04/06/2018
ms.author: mabrigg
ms.openlocfilehash: c28216ced2a7cd2995c55a9faacb93cf27e60c65
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31394384"
---
# <a name="run-a-validation-test-for-azure-stack"></a>为 Azure Stack 运行验证测试

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*
 
可以验证 Azure Stack 的状态。 遇到问题时，请联系 Microsoft 客户服务支持。 支持人员会要求你从管理节点运行 Test-AzureStack。 验证测试会对故障进行隔离。 然后，支持人员可以分析详细日志，专注于发生问题的区域，并与你协作来解决问题。

## <a name="run-test-azurestack"></a>运行 Test-AzureStack

遇到问题时，请联系 Microsoft 客户服务支持，然后**运行 Test-AzureStack**。

1. 遇到问题。
2. 联系 Microsoft 客户服务支持。
3. 从特权终结点运行 **Test-AzureStack**。
    1. 访问特权终结点。 有关说明，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。 
    2. 在 ASDK，在登录到的管理主机**AzureStack\CloudAdmin**。  
    在集成的系统上将需要用于提供给你的 OEM 硬件供应商的管理特权终结点的 IP 地址。
    3. 以管理员身份打开 PowerShell。
    4. 运行：`Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint`
    5. 运行：`Test-AzureStack`
4. 如果有任何测试报告了失败，请运行：`Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>`。此 cmdlet 从 Test-AzureStack 收集日志。 有关诊断日志的详细信息，请参阅 [Azure Stack 诊断工具](azure-stack-diagnostics.md)。
5. 将 **SeedRing** 日志发送给 Microsoft 客户服务支持。 Microsoft 客户服务支持将与你协作来解决问题。

## <a name="reference-for-test-azurestack"></a>Test-AzureStack 参考

本部分包含了 Test-AzureStack cmdlet 的概述和验证报告的摘要。

### <a name="test-azurestack"></a>Test-AzureStack

验证 Azure Stack 的状态。 此 cmdlet 报告 Azure Stack 硬件和软件的状态。 支持人员可以使用此报告来缩短解决 Azure Stack 支持案例所需的时间。

> [!Note]  
> Test-AzureStack 可能会检测到不会导致云中断的故障，例如单一故障磁盘或单一物理主机节点故障。

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
<!-- | StorageConnectionString | String          | 否       | 不可用      | 在 1802 中不受支持 -->
| 列出                    | SwitchParameter | 否       | FALSE   |
| 忽略                  | String          | 否       | 不可用      |
| 包括                 | String          | 否       | 不可用      |

Test-AzureStack cmdlet 支持以下通用参数：Verbose、Debug、ErrorAction、ErrorVariable、WarningAction、WarningVariable、OutBuffer、PipelineVariable 和 OutVariable。 有关详细信息，请参阅[有关通用参数](http://go.microsoft.com/fwlink/?LinkID=113216)。 

### <a name="examples-of-test-azurestack"></a>Test-AzureStack 的示例

以下示例假定以 **CloudAdmin** 身份登录并访问特权终结点 (PEP)。 有关说明，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>在没有云方案的情况下以交互方式运行 Test-AzureStack

在 PEP 会话中，运行：

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
      Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>在有云方案的情况下运行 Test-AzureStack

可以使用 Test-AzureStack 针对 Azure Stack 运行云方案。 这些方案包括：

 - 创建资源组
 - 创建计划
 - 创建产品/服务
 - 创建存储帐户
 - 创建虚拟机
 - 使用在测试方案中创建的存储帐户执行 blob 操作
 - 使用在测试方案中创建的存储帐户执行队列操作
 - 使用在测试方案中创建的存储帐户执行表操作

云方案需要云管理员凭据。 
> [!Note]  
> 不能使用 Active Directory 联合服务 (ADFS) 凭据运行云方案。 只能通过 PEP 访问 **Test-AzureStack** cmdlet。 但是，PEP 不支持 ADFS 凭据。

以 UPN 格式 serviceadmin@contoso.onmicrosoft.com (AAD) 键入云管理员用户名。 出现提示时，键入云管理员帐户的密码。

在 PEP 会话中，运行：

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>在没有云方案的情况下运行 Test-AzureStack

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

排除特定的测试：

````PowerShell
  Enter-PSSession -ComputerName <ERCS VM name> -ConfigurationName PrivilegedEndpoint `
  Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="validation-test"></a>验证类型

下表总结了 Test-AzureStack 运行的验证测试。

| 名称                                                                                                                              |
|-----------------------------------------------------------------------------------------------------------------------------------|-----------------------|
| Azure Stack 云托管基础结构摘要                                                                                  |
| Azure Stack 存储服务摘要                                                                                              |
| Azure Stack 基础结构角色实例摘要                                                                                  |
| Azure Stack 云托管基础结构利用率                                                                              |
| Azure Stack 基础结构容量                                                                                               |
| Azure Stack 门户和 API 摘要                                                                                                |
| Azure Stack Azure 资源管理器证书摘要                                                                                               |
| 基础结构管理控制器、网络控制器、存储服务和特权终结点基础结构角色          |
| 基础结构管理控制器、网络控制器、存储服务和特权终结点基础结构角色实例 |
| Azure Stack 基础结构角色摘要                                                                                           |
| Azure Stack 云 Service Fabric 服务                                                                                         |
| Azure Stack 基础结构角色实例性能                                                                              |
| Azure Stack 云主机性能摘要                                                                                        |
| Azure Stack 服务资源消耗摘要                                                                                  |
| Azure Stack 缩放单位关键事件（过去 8 小时）                                                                             |
| Azure Stack 存储服务物理磁盘摘要                                                                               |

## <a name="next-steps"></a>后续步骤

 - 若要详细了解 Azure Stack 诊断工具和问题日志记录，请参阅 [Azure Stack 诊断工具](azure-stack-diagnostics.md)。
 - 若要了解有关疑难解答的详细信息，请参阅[Microsoft Azure 堆栈故障排除](azure-stack-troubleshooting.md)