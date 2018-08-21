---
title: 自动执行使用 PowerShell 的 Azure Stack 验证 |Microsoft Docs
description: 可以自动使用 PowerShell 的 Azure Stack 验证。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1cb4b1a7cfd72ea302676244a53af58e77215aa9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235253"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>自动执行使用 PowerShell 的 Azure Stack 验证 

作为一项服务 (VaaS) 验证提供的功能来自动执行的测试使用启动**LaunchVaaSTests.ps1**脚本。

以下工作流，可以使用 PowerShell:

- 测试通过工作流

此脚本包含工作流的四个元素：

- 安装必备组件。
- 安装并启动本地代理。
- 启动测试，例如集成，功能，可靠性的类别。
- 每个测试传递结果用于监视和报告的报表文件生成。

## <a name="launch-the-test-pass-workflow"></a>启动测试步工作流

1. 打开提升的 PowerShell 命令提示符。

2. 运行以下脚本来下载自动化脚本：

    ````PowerShell  
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://vaastestpacksprodeastus.blob.core.windows.net/packages/Microsoft.VaaS.Scripts.3.0.0.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ````

3. 使用你的值运行以下脚本：

    ````PowerShell  
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>"  -AsPlainText -Force)
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<ServiceAdminUser>", (ConvertTo-SecureString "<ServiceAdminPassword>" -AsPlainText -Force)
    $TenantAdminCreds = New-Object System.Management.Automation.PSCredential "<TenantAdminUser>", (ConvertTo-SecureString "<TenantAdminPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
        -VaaSAccountTenantId <VaaSAccountTenantId> `
        -VaaSSolutionName <VaaSSolutionName> `
        -VaaSTestPassName <VaaSTestPassName> `
        -VaaSTestCategories Integration,Functional `
        -MaxScriptWaitTimeInHours 12 `
        -ServiceAdminCreds $ServiceAdminCreds `
    ````

    **参数**

    | 参数 | 说明 |
    | --- | --- |
    | VaaSUserld | VaaS 用户 id。 | 
    | VaaSUserPassword | VaaS 密码。 |
    | ServiceAdminUser | Azure Stack 服务管理员帐户。  |
    | ServiceAdminPassword | Azure Stack 服务密码。  |
    | TenantAdminUser | 主租户管理员。  |
    | TenantAdminPassword | 主租户的密码。  |
    | FunctionalCategory| 集成，可以正常运行，或。 可靠性。 如果使用多个值，请用逗号分隔各个值。  |

4. 查看测试的结果。 有关读取测试结果的信息，请参阅[监视测试](azure-stack-vaas-monitor-test.md)。

## <a name="next-steps"></a>后续步骤

 - 若要详细了解[作为服务的 Azure Stack 验证](https://docs.microsoft.com/azure/azure-stack/partner)。
 - 若要了解有关 Azure Stack 上的 PowerShell 的详细信息，请参阅[Azure Stack 模块](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0)引用。