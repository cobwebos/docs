---
title: Azure 实验室服务的 PowerShell 模块 |Microsoft Docs
description: 本文提供了有关 PowerShell 模块的信息，可帮助管理 Azure 实验室服务中的项目。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 6f5809dcf981a1ecf30cda06af03d2b8a06d3694
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87078828"
---
# <a name="azlabservices-powershell-module-preview"></a>Az.LabServices PowerShell 模块（预览版）
Az LabServices 是一个 PowerShell 模块，用于简化 Azure 实验室服务的管理。 它提供了可编写的函数，用于创建、查询、更新和删除实验室帐户、实验室、Vm 和映像。 有关此模块的详细信息，请参阅 [GitHub 上的 LabServices 主页](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

> [!NOTE]
> 此模块处于 **预览阶段**。 

## <a name="example-command"></a>示例命令
下面是使用 PowerShell 命令停止所有实验室中所有正在运行的 Vm 的示例。

```powershell
Get-AzLabAccount | Get-AzLab | Get-AzLabVm -Status Running | Stop-AzLabVm
```

## <a name="get-started"></a>入门
1. 安装 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) （如果计算机上不存在）。 
2. 将 [LabServices](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Modules/Library/Az.LabServices.psm1) 下载到你的计算机。
3. 导入模块：

    ```powershell
    Import-Module .\Az.LabServices.psm1
    ```
4. 运行以下命令以列出订阅中的所有实验室。

    ```powershell
    Get-AzLabAccount | Get-AzLab
    ```

## <a name="next-steps"></a>后续步骤
请参阅 [GitHub 上的 LabServices 主页](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。
