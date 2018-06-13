---
title: 启动和停止 Azure Stack | Microsoft Docs
description: 了解如何启动和关闭 Azure Stack。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 53015ba5c282bbe9c7b8185b080ffb6d834b6c75
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31391127"
---
# <a name="start-and-stop-azure-stack"></a>启动和停止 Azure Stack
你应遵循本文正确关闭并重新启动 Azure 堆栈服务中的过程。 

## <a name="stop-azure-stack"></a>停止 Azure Stack 

使用以下步骤关闭 Azure Stack：

1. 从可以通过网络访问 Azure Stack ERCS VM 的计算机打开特权终结点会话 (PEP)。 有关说明，请参阅[使用 Azure Stack 中的特权终结点](azure-stack-privileged-endpoint.md)。

2. 从 PEP 运行：

    ```powershell
      Stop-AzureStack
    ```

3. 等待所有物理 Azure Stack 节点关闭电源。

> [!Note]  
> 可以按照提供 Azure Stack 硬件的原始设备制造商 (OEM) 的指示，确认物理节点的电源状态。 

## <a name="start-azure-stack"></a>启动 Azure Stack 

使用以下步骤启动 Azure Stack。 请按照下列步骤操作，而不论 Azure Stack 是如何停止的。

1. 将 Azure Stack 环境中每个物理节点的电源打开。 按照提供 Azure Stack 硬件的原始设备制造商 (OEM) 的指示，确认物理节点的电源开启指示。

2. 等待直到 Azure Stack 基础结构服务启动。 Azure Stack 基础结构服务完成启动过程可能需要两个小时。 可以使用 [**Get-ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack) 确认 Azure Stack 的启动状态。


## <a name="get-the-startup-status-for-azure-stack"></a>获取 Azure Stack 的启动状态

使用以下步骤获取 Azure Stack 启动例程的启动状态：

1. 从可以通过网络访问 Azure Stack ERCS VM 的计算机打开特权终结点会话。

2. 从 PEP 运行：

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>针对 Azure Stack的启动和关闭进行故障排除

如果打开 Azure Stack 环境的电源 2 小时后基础结构和租户服务未成功启动，请执行以下步骤。 

1. 从可以通过网络访问 Azure Stack ERCS VM 的计算机打开特权终结点会话。

2. 运行： 

    ```powershell
      Test-AzureStack
      ```

3. 查看输出并解决任何运行状况错误。 有关详细信息，请参阅[运行 Azure Stack 的验证测试](azure-stack-diagnostic-test.md)。

4. 运行：

    ```powershell
      Start-AzureStack
    ```

5. 如果运行 **Start-AzureStack** 的结果为失败，请联系 Microsoft 客户服务支持。 

## <a name="next-steps"></a>后续步骤 

了解有关 Azure 堆栈诊断工具的详细信息和发出日志记录，请参阅[Azure 堆栈诊断工具](azure-stack-diagnostics.md)。
