---
title: "使用 PowerShell 创建策略分配以识别 Azure 环境中的不合规资源 | Microsoft Docs"
description: "使用 PowerShell 创建 Azure 策略分配，识别不合规资源。"
services: azure-policy
keywords: 
author: bandersmsft
ms.author: banders
ms.date: 11/02/2017
ms.topic: quickstart
ms.service: azure-policy
ms.custom: mvc
ms.openlocfilehash: 5c00d50817e40de0a43d05eb85662b494247d8fa
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/18/2017
---
# <a name="create-a-policy-assignment-to-identify-non-compliant-resources-in-your-azure-environment-using-powershell"></a>使用 PowerShell 创建策略分配，识别 Azure 环境中的不合规资源

了解 Azure 中符合性的第一步需要了解自己当前资源的状况。 本快速入门教程指导你完成创建策略分配的过程，以识别未使用托管磁盘的虚拟机。

此过程结束时，应已成功识别哪些虚拟机未使用托管磁盘，也就是不符合要求。


PowerShell 用于从命令行或脚本创建和管理 Azure 资源。 本指南详述如何使用 PowerShell 创建策略分配，识别 Azure 环境中的不合规资源。

本指南需要 Azure PowerShell 模块 4.0 或更高版本。 运行  ```Get-Module -ListAvailable AzureRM```  即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure PowerShell 模块](/powershell/azure/install-azurerm-ps)。

在开始之前，请确保安装 PowerShell 的最新版本。 有关详细信息，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azureps-cmdlets-docs)。

如果你还没有 Azure 订阅，可以在开始前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="opt-in-to-azure-policy"></a>选择加入 Azure 策略

Azure 策略现已在公共预览版中提供，因此需要注册才能请求访问权限。

1. 在 https://aka.ms/getpolicy 转到 Azure 策略，然后选择左侧窗格中的“注册”。

   ![搜索策略](media/assign-policy-definition/sign-up.png)

2. 通过选择“订阅”列表中想要使用的订阅选择加入 Azure 策略。 然后选择“注册”。

   ![选择加入使用 Azure 策略](media/assign-policy-definition/preview-opt-in.png)

   在预览版中，将自动批准请求。 请预留 30 分钟，以便系统完成注册。

## <a name="create-a-policy-assignment"></a>创建策略分配

在此快速入门中，我们将创建一个策略分配，分配“审核缺少托管磁盘的虚拟机”定义。 此策略分配识别不符合策略定义中所设置条件的资源。

按照下列步骤操作以创建新的策略分配。

运行以下命令查看所有策略定义并找到想要分配的定义：

```powershell
$definition = Get-AzureRmPolicyDefinition
```

Azure 策略附带可供使用的内置策略定义。 内置策略定义示例如下：

- 强制实施标记和值
- 应用标记和值
- 需要 SQL Server 版本 12.0

接下来，使用 `New-AzureRmPolicyAssignment` cmdlet 将策略定义分配给所需范围。

对于本教程，我们为该命令提供以下信息：
- 策略分配的显示名称。 在此示例中，我们使用“审核缺少托管磁盘的虚拟机”。
- **策略** - 指的是策略定义，即为用于创建分配的依据。 在此示例中，即策略定义 - 审核缺少托管磁盘的虚拟机
- 范围 - 范围用于确定在其中实施策略分配的资源或资源组。 它可以从订阅延伸至资源组。 此示例中，我们向 FabrikamOMS 资源组分配该策略定义。
- $definition - 需要提供策略定义的资源 ID。在此示例中，我们使用策略定义“缺少托管磁盘的审核虚拟机”的 ID。

```powershell
$rg = Get-AzureRmResourceGroup -Name "FabrikamOMS"
$definition = Get-AzureRmPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/e5662a6-4747-49cd-b67b-bf8b01975c4c
New-AzureRMPolicyAssignment -Name Audit Virtual Machines without Managed Disks Assignment -Scope $rg.ResourceId -PolicyDefinition $definition
```

你现已准备好识别不合规的资源，了解环境的符合性状态。

## <a name="identify-non-compliant-resources"></a>识别不合规的资源

1. 导航回 Azure 策略登录页。
2. 选择左侧窗格中的“符合性”，搜索创建的“策略分配”。

   ![策略符合性](media/assign-policy-definition/policy-compliance.png)

   如果存在与此新分配不符合的任何现有资源，将在“不符合资源”选项卡下显示，如上图所示。

## <a name="clean-up-resources"></a>清理资源

本教程系列中的其他指南建立在本快速入门的基础之上。 如何打算继续浏览后续教程，请勿清除本快速入门中创建的资源。 如果不打算继续，则通过运行以下命令删除创建的分配：

```powershell
Remove-AzureRmPolicyAssignment -Name “Audit Virtual Machines without Managed Disks Assignment” -Scope /subscriptions/ bc75htn-a0fhsi-349b-56gh-4fghti-f84852/resourceGroups/FabrikamOMS
```

## <a name="next-steps"></a>后续步骤

在此快速入门中，你已分配策略定义来识别 Azure 环境中的不合规资源。

若要详细了解分配策略以确保将来所创建资源的符合性，请继续以下教程：

> [!div class="nextstepaction"]
> [创建和管理策略](./create-manage-policy.md)
