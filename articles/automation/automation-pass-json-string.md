---
title: "将 JSON 对象传递到 Azure 自动化 Runbook | Microsoft Docs"
description: "如何将参数作为 JSON 对象传递给 runbook"
services: automation
documentationcenter: dev-center-name
author: eslesar
manager: carmonm
keywords: "powershell, runbook, json, azure 自动化"
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 06/15/2017
ms.author: eslesar
ms.openlocfilehash: eac0e95a46731b9d396ea0590e629d61ca6a7d70
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="pass-a-json-object-to-an-azure-automation-runbook"></a>将 JSON 对象传递到 Azure 自动化 Runbook

它可用于存储想要传递到 JSON 文件中的 Runbook 的数据。
例如，你可能会创建包含所有想要传递给 Runbook 的参数的 JSON 文件。
为此，必须先将 JSON 转换为字符串，将字符串转换为 PowerShell 对象，然后再将其内容传递到 Runbook。

在此示例中，将创建一个调用 [Start-AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603661.aspx) 的 PowerShell 脚本来启动 PowerShell Runbook，将 JSON 的内容传递给 Runbook。
PowerShell Runbook 会启动一个 Azure VM，从传入的 JSON 获取 VM 的参数。

## <a name="prerequisites"></a>先决条件
若要完成本教程，需要以下各项：

* Azure 订阅。 如果还没有帐户，则可以[激活 MSDN 订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)或<a href="/pricing/free-account/" target="_blank">[注册免费帐户](https://azure.microsoft.com/free/)。
* [自动化帐户](automation-sec-configure-azure-runas-account.md) ，用来保存 Runbook 以及向 Azure 资源进行身份验证。  此帐户必须有权启动和停止虚拟机。
* Azure 虚拟机。 我们需停止和启动该虚拟机，因此其不应为生产用 VM。
* 在本地计算机上安装的 Azure Powershell。 若要详细了解如何获得 Azure PowerShell，请参阅 [Install and configure Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0)（安装和配置 Azure PowerShell）。

## <a name="create-the-json-file"></a>创建 JSON 文件

在文本文件中键入以下测试，并在本地计算机上将其保存为 `test.json`。

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

## <a name="create-the-runbook"></a>创建 Runbook

在 Azure 自动化中创建名为“Test-Json”的新 PowerShell Runbook。
若要了解如何创建新的 PowerShell Runbook，请参阅[我的第一个 PowerShell Runbook](automation-first-runbook-textual-powershell.md)。

要接受 JSON 数据，Runbook 必须将一个对象作为输入参数。

然后，Runbook 可以使用 JSON 中定义的属性。

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Connect to Azure account   
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationID $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzureRmVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup
 ```

 在自动化帐户中保存并发布此 Runbook。

## <a name="call-the-runbook-from-powershell"></a>通过 PowerShell 调用 Runbook

现在可以通过 Azure PowerShell 从本地计算机调用 Runbook。
运行以下 PowerShell 命令：

1. 登录到 Azure：
   ```powershell
   Login-AzureRmAccount
   ```
    系统会提示输入 Azure 凭据。
1. 获取 JSON 文件的内容并将其转换为字符串：
    ```powershell
    $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
    ```
    `JsonPath` 是保存 JSON 文件的位置路径。
1. 将 `$json` 的字符串内容转换为 PowerShell 对象：
   ```powershell
   $JsonParams = @{"json"=$json}
   ```
1. 为 `Start-AzureRmAutomstionRunbook` 的参数创建哈希表：
   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```
   请注意，你正在将 `Parameters` 的值设置为包含 JSON 文件中的值的 PowerShell 对象。 
1. 启动 Runbook
   ```powershell
   $job = Start-AzureRmAutomationRunbook @RBParams
   ```

Runbook 将使用 JSON 文件中的值来启动 VM。

## <a name="next-steps"></a>后续步骤

* 若要了解有关使用文本编辑器编辑 PowerShell 和 PowerShell 工作流 Runbook 的详细信息，请参阅 [编辑 Azure 自动化中的文本 Runbook](automation-edit-textual-runbook.md) 
* 若要了解创建和导入 Runbook 的详细信息，请参阅[在 Azure 自动化中创建或导入 Runbook](automation-creating-importing-runbook.md)


