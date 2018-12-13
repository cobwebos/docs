---
title: 使用 Azure Stack 验证工具 |Microsoft Docs
description: 如何收集日志文件以在 Azure Stack 中进行诊断。
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 12/03/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.openlocfilehash: f754242d0cf7ee30572b21a3f4daf6fd2c0f63ff
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/12/2018
ms.locfileid: "53275901"
---
# <a name="validate-azure-stack-system-state"></a>验证 Azure Stack 系统状态

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 操作员能够知道的运行状况和状态的按需系统非常重要。 Azure Stack 验证工具 (**Test-azurestack**) PowerShell cmdlet，使你能够运行一系列测试以发现故障，如果存在在系统上。 您通常需要在运行此工具，[特权的终结点 (PEP)](azure-stack-privileged-endpoint.md)与 Microsoft 客户服务支持 (CSS) 联系出问题。 整个系统运行状况和状态信息后，CSS 可以收集和分析详细的日志，重点关注的区域，该错误发生，并完成与你要解决此问题。

## <a name="running-the-validation-tool-and-accessing-results"></a>运行验证工具并访问结果

如前面所述，通过 PEP 运行验证工具。 每个测试返回**通过/失败**在 PowerShell 窗口中的状态。 此外，可以在更高版本来访问它创建一个详细的 HTML 报表[日志收集](azure-stack-diagnostics.md)。 下面是测试过程的端到端验证的概述： 

1. 访问特权终结点 (PEP)。 运行以下命令以建立 PEP 会话：

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > 若要访问 PEP ASDK 主机计算机上的，使用-ComputerName AzS-ERCS01。

2. 在 PEP 中后，运行： 

   ```powershell
   Test-AzureStack
   ```

   请参阅[参数注意事项](azure-stack-diagnostic-test.md#parameter-considerations)并[使用用例示例](azure-stack-diagnostic-test.md#use-case-examples)部分，了解详细信息。

3. 如果任何测试报告**失败**，请运行：

   ```powershell
   Get-AzureStackLog -FilterByRole SeedRing -OutputSharePath "<path>" -OutputShareCredential $cred
   ```

   该 cmdlet 将收集生成的 Test-azurestack 日志。 有关诊断日志的详细信息，请参阅 [Azure Stack 诊断工具](azure-stack-diagnostics.md)。 不应收集日志，或如果请与 CSS 联系测试报告**给出警告**。

4. 如果系统已指示将运行验证工具通过 CSS，CSS 代表将请求收集以继续诊断问题的日志。

## <a name="tests-available"></a>可用的测试

验证工具可一系列的系统级测试和运行基本的云方案提供见解与当前的状态，并在系统中确定问题。

### <a name="cloud-infrastructure-tests"></a>云基础结构的测试

这些影响最小测试基础结构级别一起使用，并为您提供各种系统组件和功能的信息。 目前，测试分为以下类别：

| 测试类别                                        | -参数包括，和-忽略 |
| :--------------------------------------------------- | :-------------------------------- |
| Azure Stack 警报摘要                            | AzsAlertSummary                   |
| Azure Stack 备份共享可访问性摘要       | AzsBackupShareAccessibility       |
| Azure Stack 控制平面摘要                    | AzsControlPlane                   |
| Azure Stack Defender 摘要                         | AzsDefenderSummary                |
| Azure Stack 托管基础结构固件摘要  | AzsHostingInfraFWSummary          |
| Azure Stack 云托管基础结构摘要     | AzsHostingInfraSummary            |
| Azure Stack 云托管基础结构利用率 | AzsHostingInfraUtilization        |
| Azure Stack 基础结构容量                  | AzsInfraCapacity                  |
| Azure Stack 基础结构性能               | AzsInfraPerformance               |
| Azure Stack 基础结构角色摘要              | AzsInfraRoleSummary               |
| Azure Stack 更新摘要                           | AzsInfraUpdateSummary             |
| Azure Stack 门户和 API 摘要                   | AzsPortalAPISummary               |
| Azure Stack 缩放单位 VM 事件                     | AzsScaleUnitEvents                |
| Azure Stack 缩放单位 VM 资源                  | AzsScaleUnitResources             |
| Azure Stack SDN 验证摘要                   | AzsSDNValidation                  |
| Azure Stack 服务 Fabric 角色摘要              | AzsSFRoleSummary                  |
| Azure Stack BMC 摘要                              | AzsStampBMCSummary                |
| Azure Stack 存储服务摘要                 | AzsStorageSvcsSummary             |
| Azure Stack SQL 存储摘要                        | AzsStoreSummary                   |
| Azure Stack VM 放置摘要                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>云方案测试

除了上述的基础结构测试，还必须运行云方案测试以检查在基础结构组件之间的功能的能力。 云管理员凭据才可运行这些测试，因为它们涉及资源部署。 
    > [!NOTE]
    >
    > Currently you cannot run cloud scenario tests using Active Directory Federated Services (AD FS) credentials. 

以下云方案所测试的验证工具：
- 创建资源组   
- 创建计划              
- 产品/服务创建            
- 创建存储帐户   
- 创建虚拟机 
- Blob 存储操作   
- 队列存储操作  
- 表存储操作  

## <a name="parameter-considerations"></a>参数注意事项

- 将参数**列表**可以用于显示所有可用的测试类别。

- 参数**Include**并**忽略**可用于包含或排除测试类别。 请参阅[提供了测试](azure-stack-diagnostic-test.md#tests-available)速记与这些自变量一起使用的详细信息的部分。

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- VM 将部署为属于一个租户的云方案测试。 可以使用**DoNotDeployTenantVm**要禁用此功能。 

- 你需要提供**ServiceAdminCredential**参数中所述运行云方案测试[使用用例示例](azure-stack-diagnostic-test.md#use-case-examples)部分。

- **BackupSharePath**并**BackupShareCredential**中所示测试基础结构备份设置时，将使用[使用用例示例](azure-stack-diagnostic-test.md#use-case-examples)部分。

- 验证工具还支持常见的 PowerShell 参数：Verbose、 Debug、 ErrorAction、 ErrorVariable、 WarningAction、 WarningVariable、 OutBuffer、 PipelineVariable 和 OutVariable。 有关详细信息，请参阅[有关通用参数](http://go.microsoft.com/fwlink/?LinkID=113216)。  

## <a name="use-case-examples"></a>使用用例示例

### <a name="run-validation-without-cloud-scenarios"></a>运行验证，而无需云方案

运行验证工具，而无需**ServiceAdminCredential**参数跳过运行云方案测试： 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>使用云方案运行验证

提供的验证工具**ServiceAdminCredentials**参数默认情况下运行云方案测试： 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

如果你想要运行而无需运行测试的其余部分仅云方案，则可以使用**Include**参数来执行此操作： 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

必须以 UPN 格式键入云管理员用户名称：serviceadmin@contoso.onmicrosoft.com (Azure AD)。 出现提示时，键入云管理员帐户的密码。

### <a name="run-validation-tool-to-test-system-readiness-before-installing-update-or-hotfix"></a>运行验证工具，在安装更新或修补程序之前测试系统准备情况

在开始安装更新或修补程序之前，应运行验证工具，检查 Azure Stack 的状态：

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>运行验证工具以测试基础结构备份设置

*之前*配置基础结构备份，可以测试备份共享路径和凭据使用**AzsBackupShareAccessibility**测试： 

  ```powershell
  New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential <PSCredentials-for-backup-share>
  ```

*之后*配置备份，你可以运行**AzsBackupShareAccessibility**验证共享是否可从 ERCS 访问：

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

若要测试与已配置的备份共享的新凭据，请运行： 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```



## <a name="next-steps"></a>后续步骤

若要了解有关 Azure Stack 诊断工具和问题日志记录的详细信息，请参阅[Azure Stack 诊断工具](azure-stack-diagnostics.md)。

若要了解有关疑难解答的详细信息，请参阅[Microsoft Azure Stack 故障排除](azure-stack-troubleshooting.md)。