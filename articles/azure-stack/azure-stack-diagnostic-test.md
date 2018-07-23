---
title: 在 Azure Stack 中运行验证测试 | Microsoft Docs
description: 如何收集的 Azure Stack 中的诊断日志文件。
services: azure-stack
author: mattbriggs
manager: femila
cloud: azure-stack
ms.assetid: D44641CB-BF3C-46FE-BCF1-D7F7E1D01AFA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/19/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: a70c736489b25f6e8fd0d838c4c7b4b4db96a4f2
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188867"
---
# <a name="run-a-validation-test-for-azure-stack"></a>为 Azure Stack 运行验证测试

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*
 
可以验证 Azure Stack 的状态。 遇到问题时，请联系 Microsoft 客户服务支持。 支持人员会要求你运行**Test-azurestack**从管理节点。 验证测试会对故障进行隔离。 然后，支持人员可以分析详细日志，专注于发生问题的区域，并与你协作来解决问题。

## <a name="run-test-azurestack"></a>运行 Test-AzureStack

遇到问题时，请联系 Microsoft 客户服务支持，然后**运行 Test-AzureStack**。

1. 遇到问题。
2. 联系 Microsoft 客户服务支持。
3. 运行**Test-azurestack**从特权终结点。
    1. 访问特权终结点。 有关说明，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。 
    2. 在 ASDK 上，以 **AzureStack\CloudAdmin** 身份登录到管理主机。  
    在集成系统中，需要使用由 OEM 硬件供应商提供的管理特权终结点 IP 地址。
    3. 以管理员身份打开 PowerShell。
    4. 运行：`Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint`
    5. 运行：`Test-AzureStack`
4. 如果有任何测试报告了失败，请运行：`Get-AzureStackLog -FilterByRole SeedRing -OutputPath <Log output path>`。此 cmdlet 从 Test-AzureStack 收集日志。 有关诊断日志的详细信息，请参阅 [Azure Stack 诊断工具](azure-stack-diagnostics.md)。
5. 将 **SeedRing** 日志发送给 Microsoft 客户服务支持。 Microsoft 客户服务支持将与你协作来解决问题。

## <a name="reference-for-test-azurestack"></a>Test-AzureStack 参考

本部分包含了 Test-AzureStack cmdlet 的概述和验证报告的摘要。

### <a name="test-azurestack"></a>Test-AzureStack

验证 Azure Stack 的状态。 此 cmdlet 报告 Azure Stack 硬件和软件的状态。 支持人员可以使用此报告来缩短解决 Azure Stack 支持案例所需的时间。

> [!Note]  
> **Test-azurestack**可能会检测到不会导致云中断的故障，例如单一故障磁盘或单一物理主机节点故障。

#### <a name="syntax"></a>语法

````PowerShell
  Test-AzureStack
````

#### <a name="parameters"></a>parameters

| 参数               | 值           | 需要 | 默认 |
| ---                     | ---             | ---      | ---     |
| ServiceAdminCredentials | PSCredential    | 否       | FALSE   |
| DoNotDeployTenantVm     | SwitchParameter | 否       | FALSE   |
| AdminCredential         | PSCredential    | 否       | NA      |
| 列出                    | SwitchParameter | 否       | FALSE   |
| 忽略                  | String          | 否       | NA      |
| 包括                 | String          | 否       | NA      |
| BackupSharePath         | String          | 否       | NA      |
| BackupShareCredential   | PSCredential    | 否       | NA      |


Test-AzureStack cmdlet 支持以下通用参数：Verbose、Debug、ErrorAction、ErrorVariable、WarningAction、WarningVariable、OutBuffer、PipelineVariable 和 OutVariable。 有关详细信息，请参阅[有关通用参数](http://go.microsoft.com/fwlink/?LinkID=113216)。 

### <a name="examples-of-test-azurestack"></a>Test-AzureStack 的示例

以下示例假定以 **CloudAdmin** 身份登录并访问特权终结点 (PEP)。 有关说明，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。 

#### <a name="run-test-azurestack-interactively-without-cloud-scenarios"></a>在没有云方案的情况下以交互方式运行 Test-AzureStack

在 PEP 会话中，运行：

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack
````

#### <a name="run-test-azurestack-with-cloud-scenarios"></a>在有云方案的情况下运行 Test-AzureStack

可以使用**Test-azurestack**针对 Azure Stack 运行云方案。 这些方案包括：

 - 创建资源组
 - 创建计划
 - 创建套餐
 - 创建存储帐户
 - 创建虚拟机
 - 使用在测试方案中创建的存储帐户执行 blob 操作
 - 使用在测试方案中创建的存储帐户执行队列操作
 - 使用在测试方案中创建的存储帐户执行表操作

云方案需要云管理员凭据。 
> [!Note]  
> 不能运行云方案使用 Active Directory 联合身份验证服务 (AD FS) 的凭据。 只能通过 PEP 访问 **Test-AzureStack** cmdlet。 但是，PEP 不支持的 AD FS 凭据。

以 UPN 格式键入云管理员用户名serviceadmin@contoso.onmicrosoft.com(Azure AD)。 出现提示时，键入云管理员帐户的密码。

在 PEP 会话中，运行：

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -ServiceAdminCredentials <Cloud administrator user name>
````

#### <a name="run-test-azurestack-without-cloud-scenarios"></a>在没有云方案的情况下运行 Test-AzureStack

在 PEP 会话中，运行：

````PowerShell
  $session = New-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Invoke-Command -Session $session -ScriptBlock {Test-AzureStack}
````

#### <a name="list-available-test-scenarios"></a>列出可用的测试方案：

在 PEP 会话中，运行：

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -List
````

#### <a name="run-a-specified-test"></a>运行指定的测试

在 PEP 会话中，运行：

````PowerShell
  Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
````

排除特定的测试：

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Ignore AzsInfraPerformance
````

### <a name="run-test-azurestack-to-test-infrastructure-backup-settings"></a>运行 Test-azurestack 测试基础结构备份设置

配置基础结构备份之前, 测试备份共享路径和凭据使用**AzsBackupShareAccessibility**测试。

在 PEP 会话中，运行：

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
````
配置备份之后, 可以运行 AzsBackupShareAccessibility 来验证该共享是可从 ERCS，来自 PEP 会话中运行访问：

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint  -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility
````

若要测试与已配置的备份共享的新凭据，在 PEP 会话中运行：

````PowerShell
    Enter-PSSession -ComputerName <ERCS-VM-name> -ConfigurationName PrivilegedEndpoint -Credential $localcred
    Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential <PSCredential for backup share>
````

### <a name="validation-test"></a>验证类型

下表总结了运行的验证测试**Test-azurestack**。

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
| Azure Stack 缩放单元关键事件（过去 8 小时）                                                                             |
| Azure Stack 存储服务物理磁盘摘要                                                                               |
|Azure Stack 备份共享可访问性摘要                                                                                     |

## <a name="next-steps"></a>后续步骤

 - 若要详细了解 Azure Stack 诊断工具和问题日志记录，请参阅 [Azure Stack 诊断工具](azure-stack-diagnostics.md)。
 - 若要了解有关疑难解答的详细信息，请参阅[Microsoft Azure Stack 故障排除](azure-stack-troubleshooting.md)
