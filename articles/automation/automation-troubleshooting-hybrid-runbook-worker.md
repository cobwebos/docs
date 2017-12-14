---
title: "对 Azure 自动化混合 Runbook 辅助角色相关问题进行故障排除 | Microsoft Docs"
description: "介绍 Azure 自动化中最常见的混合 Runbook 辅助角色问题的相关表现、起因及解决方法。"
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 02c6606e-8924-4328-a196-45630c2255e9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/25/2017
ms.author: magoedte
ms.openlocfilehash: 75f4ac1bc940a2b1d8e4ac6aeac8b80c642489da
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/14/2017
---
# <a name="troubleshooting-tips-for-hybrid-runbook-worker"></a>混合 Runbook 辅助角色的故障排除提示

本文介绍如何解决可能遇到的自动化混合 Runbook 辅助角色相关错误，并提供可能的解决方案建议。

## <a name="a-runbook-job-terminates-with-a-status-of-suspended"></a>runbook 作业以暂停状态终止

runbook 在尝试执行三次后立刻暂停。 存在可能会阻止 runbook 成功完成的情况，相关错误消息未包括指示原因的任何其他信息。 本文提供与混合 Runbook 辅助角色 runbook 执行失败相关的问题的故障排除步骤。

如果本文未解决 Azure 问题，请访问 [MSDN 和 Stack Overflow](https://azure.microsoft.com/support/forums/) 上的 Azure 论坛。 可以将问题发布到这些论坛上，或者发布到[ Twitter 上的 @AzureSupport](https://twitter.com/AzureSupport)。 此外，还可以通过在 [Azure 支持](https://azure.microsoft.com/support/options/)站点上选择“获取支持”来发出 Azure 支持请求。

### <a name="symptom"></a>症状
Runbook 执行失败，返回的错误为“无法运行作业操作‘激活’，因为进程意外停止。 该作业操作已尝试 3 次。”

导致此错误有多个可能的原因： 

1. 混合辅助角色在代理或防火墙后面
2. 运行混合辅助角色的计算机不满足最低[硬件要求](automation-offering-get-started.md#hybrid-runbook-worker)  
3. runbook 无法使用本地资源进行身份验证

#### <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>原因 1：混合 Runbook 辅助角色在代理或防火墙后面
运行混合 Runbook 辅助角色的计算机在防火墙或代理服务器后面，可能不允许或未正确配置出站网络访问。

#### <a name="solution"></a>解决方案
验证计算机是否在端口 443 上对 *.azure-automation.net 有出站访问权限。 

#### <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>原因 2：计算机不满足最低硬件要求
运行混合 Runbook 辅助角色的计算机应满足最低硬件要求，才能指定它托管此功能。 否则，在执行过程中，根据其他后台进程的资源使用率和 runbook 所导致的争用，该计算机将变为过度使用，从而导致 runbook 作业延迟或超时。 

#### <a name="solution"></a>解决方案
首先，确认指定为运行混合 Runbook 辅助角色功能的计算机满足最低硬件要求。  如果满足，请监视 CPU 和内存利用率，以确定混合 Runbook 辅助角色进程的性能和 Windows 之间的任何关联。  如果存在内存或 CPU 压力，这可能指示需要升级或添加额外的处理器或增加内存来解决资源瓶颈问题，从而解决此错误。 或者，选择其他可支持最低要求的计算资源，并在工作负荷需求指示需要增加时进行扩展。         

#### <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>原因 3：runbook 无法使用本地资源进行身份验证

#### <a name="solution"></a>解决方案
检查 **Microsoft-SMA** 事件日志中是否有描述为 Win32 Process Exited with code [4294967295] 的相应事件。  此错误的原因是尚未在 runbook 中配置身份验证，或者未为混合辅助角色组指定运行方式凭据。  请查看 [Runbook](automation-hrw-run-runbooks.md#runbook-permissions) 权限，确认已正确为 runbook 配置身份验证。  

## <a name="next-steps"></a>后续步骤

如需有关解决自动化中其他问题的帮助，请参阅[解决 Azure 自动化常见问题](automation-troubleshooting-automation-errors.md) 