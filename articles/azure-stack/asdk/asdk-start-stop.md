---
title: 启动和停止 Azure 堆栈开发工具包 (ASDK) |Microsoft 文档
description: 了解如何启动和关闭向下 Azure 堆栈开发工具包 (ASDK)。
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/11/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: dfb565803746ecdda9b36a4e12a3c3f2b4d9e0d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
ms.locfileid: "31427333"
---
# <a name="start-and-stop-the-azure-stack-development-kit-asdk"></a>启动和停止 Azure 堆栈开发工具包 (ASDK)
建议不要只需重新启动 ASDK 主机计算机。 相反，你应该按照本文章来正确关闭并重新启动 ASDK 服务中的过程。 

## <a name="stop-azure-stack"></a>停止 Azure Stack 
若要正确关闭 Azure 堆栈服务和 ASDK 主机计算机，请使用以下 PowerShell 命令：

1. 以 AzureStack\CloudAdmin 身份登录到 ASDK 主机。
2. 以管理员身份打开 PowerShell（非 PowerShell ISE）。
3. 运行以下命令以建立特权终结点 (PEP) 会话： 

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 接下来，在 PEP 会话中，使用**停止 AzureStack** cmdlet 可停止 Azure 堆栈服务并关闭 ASDK 主机计算机：

   ```powershell
   Stop-AzureStack
   ```
5. 查看 PowerShell 输出，以确保 Azure 堆栈的所有服务都已成功关闭之前 ASDK 主机计算机关机。 关闭过程需要几分钟。

## <a name="start-azure-stack"></a>启动 Azure Stack 
主机计算机启动时，ASDK 服务应自动启动。 但是，ASDK 基础结构服务启动时间异 ASDK 主机计算机的硬件配置的性能。 可能需要几个小时，为所有服务在某些情况下成功重新启动。

无论 ASDK 已关闭，你应使用以下步骤以验证所有 Azure 堆栈服务都已启动并在完全可操作主机计算机已开机后： 

1. ASDK 主计算机上的电源。 
2. 以 AzureStack\CloudAdmin 身份登录到 ASDK 主机。
3. 以管理员身份打开 PowerShell（非 PowerShell ISE）。
4. 运行以下命令以建立特权终结点 (PEP) 会话：

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
5. 接下来，在 PEP 会话中，运行以下命令以检查 Azure 堆栈服务的启动状态：

   ```powershell
   Get-ActionStatus Start-AzureStack
   ```
6. 检查输出，以确保 Azure 堆栈服务已经成功重新启动。

若要了解有关正确关闭和重新启动 Azure 堆栈服务的建议过程的详细信息，请参阅[启动和停止 Azure 堆栈](.\.\azure-stack-start-and-stop.md)。 

## <a name="troubleshoot-startup-and-shutdown"></a>解决启动和关闭 
如果 Azure 堆栈服务不成功启动后 power ASDK 主机计算机上的两个小时内，请执行以下步骤：

1. 以 AzureStack\CloudAdmin 身份登录到 ASDK 主机。
2. 以管理员身份打开 PowerShell（非 PowerShell ISE）。
3. 运行以下命令以建立特权终结点 (PEP) 会话：

   ```powershell
   Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint
   ```
4. 接下来，在 PEP 会话中，运行以下命令以检查 Azure 堆栈服务的启动状态：

   ```powershell
   Test-AzureStack
   ```
5. 审阅输出并解决任何错误。 有关详细信息，请参阅[运行 Azure Stack 的验证测试](.\.\azure-stack-diagnostic-test.md)。
6. 在 PEP 会话内重新启动 Azure 堆栈服务，通过运行**开始 AzureStack** cmdlet:

   ```powershell
   Start-AzureStack
   ```

如果运行**开始 AzureStack**导致失败，请访问[Azure 堆栈支持论坛](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurestack)获取 ASDK 故障诊断支持。 

## <a name="next-steps"></a>后续步骤 
了解有关 Azure 堆栈诊断工具的详细信息和发出日志记录，请参阅[Azure 堆栈诊断工具](.\.\azure-stack-diagnostics.md)。
