---
title: "启动和停止 Azure 堆栈 |Microsoft 文档"
description: "了解如何启动和关闭 Azure 堆栈。"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: mabrigg
ms.openlocfilehash: 9bbfffe15dc23d5d7d7b17aa4c79edc8a2aaab8c
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2018
---
# <a name="start-and-stop-azure-stack"></a>启动和停止 Azure 堆栈

*适用范围： Azure 堆栈集成系统 （1712年及更高版本）*

## <a name="stop-azure-stack"></a>停止 Azure 堆栈 

使用以下步骤关闭 Azure 堆栈：

1. 到 Azure 堆栈 ERCS Vm 从一台通过网络访问计算机中打开特权终结点会话 (PEP)。 有关说明，请参阅[Azure 堆栈中使用的特权的终结点](azure-stack-privileged-endpoint.md)。

2. 从 PEP，运行：

    ```powershell
      Stop-AzureStack
    ```

3. 等待幂的所有物理 Azure 堆栈节点关闭。

> [!Note]  
> 可以通过从原始设备制造商 (OEM) 提供你的 Azure 堆栈硬件的说明来验证物理节点的电源状态。 

## <a name="start-azure-stack"></a>启动 Azure 堆栈 

通过执行以下步骤开始 Azure 堆栈。 请按照下列步骤而不考虑 Azure 堆栈的已停止。

1. 在每个 Azure 堆栈环境中的物理节点上的电源。 通过按照从原始设备制造商 (OEM) 人员为 Azure 堆栈提供硬件的说明进行操作来验证说明物理节点上的电源。

2. 等待，直到 Azure 堆栈的基础结构服务启动。 Azure 堆栈的基础结构服务可能需要完成启动过程的两个小时。 你可以验证 Azure 堆栈的启动状态[ **Get ActionStatus** cmdlet](#get-the-startup-status-for-azure-stack)。


## <a name="get-the-startup-status-for-azure-stack"></a>Azure 堆栈中获得的启动状态

启动获取 Azure 堆栈启动例程替换为以下步骤：

1. 到 Azure 堆栈 ERCS Vm 从一台通过网络访问计算机打开特权的终结点会话。

2. 从 PEP，运行：

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>解决 Azure 堆栈的启动和关闭

如果基础结构和租户服务不成功启动后开机 Azure 堆栈环境，请执行以下步骤。 

1. 到 Azure 堆栈 ERCS Vm 从一台通过网络访问计算机打开特权的终结点会话。

2. 运行： 

    ```powershell
      Test-AzureStack
      ```

3. 审阅输出并解决任何运行状况错误。 有关详细信息，请参阅[Azure 堆栈的验证测试](azure-stack-diagnostic-test.md)。

4. 运行：

    ```powershell
      Start-AzureStack
    ```

5. 如果运行**开始 AzureStack**导致失败，请联系 Microsoft 客户服务支持。 

## <a name="next-steps"></a>后续步骤 

了解有关 Azure 堆栈诊断工具的详细信息和发出日志记录，请参阅 [Azure 堆栈诊断工具。 Azure 堆栈诊断工具。 Azure 堆栈诊断工具。 Azure 堆栈诊断工具。
